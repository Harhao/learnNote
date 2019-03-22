## Vuex 问题解惑
- 我们不能直接修改state是怎么做到的？
  因为Vuex源码中通过 this._commiting 设置一个布尔值控制不允许外部对state进行修改。在vuex设置strict:true严格模式下，只有mutation才能操作state的修改
  而action背后的操作也是通过mutation的commit提交对state的数据修改。外部行为直接对state进行修改，this._committing始终为false。vuex通过断言检查this._commiting会抛出警告错误外部对state的修改。在vuex源码src的store.js的可以查看到原理实现。
  ```
    // store internal state
    //设置不允许外部进行修改的标志_commiting
    this._committing = false
    this._actions = Object.create(null)
    this._actionSubscribers = []
    this._mutations = Object.create(null)
    this._wrappedGetters = Object.create(null)
    this._modules = new ModuleCollection(options)
    this._modulesNamespaceMap = Object.create(null)
    this._subscribers = []
    this._watcherVM = new Vue()
    
    // 内部对state进行修改，设置this._commiting为true
    _withCommit (fn) {
      const committing = this._committing
      this._committing = true
      fn()
      this._committing = committing
    } 
    //对操作state来源判断
    function enableStrictMode (store) {
      store._vm.$watch(function () { return this._data.$$state }, () => {
        if (process.env.NODE_ENV !== 'production') {
          assert(store._committing, `do not mutate vuex store state outside mutation handlers.`)
        }
      }, { deep: true, sync: true })
    } 

  ```
- store是怎么注入每一个组件实例中，可以直接通过this.$store获取到同一个store数据对象的引用？
  创建store实例，通过export default new vuex.Store({...})导出，在入口文件main.js，引入vuex,并通过vue.use(Vuex);然后在实例化一个Vue实例对象中，通过options的方法传入一个store给root根组件实例。这个时候根实例可以获取到一个store，通过this.$store的方式，如下面：
  ```
  new Vue({
    el: '#app',
    router,
    store,
    components: { App },
    template: '<App/>'
  })
  ```
  vuex的安装注册是通过vue.mixin混合的方式在生命周期beforeCreate中,如果是非根实例组件，可以通过options.parent.$store这样的方式层层向上获取到一个store。并且通过将options.store绑定在this.$store上。
  ```
  export default function (Vue) {
      const version = Number(Vue.version.split('.')[0])

      if (version >= 2) {
        Vue.mixin({ beforeCreate: vuexInit })
      } else {
        // override init and inject vuex init procedure
        // for 1.x backwards compatibility.
        const _init = Vue.prototype._init
        Vue.prototype._init = function (options = {}) {
          options.init = options.init
            ? [vuexInit].concat(options.init)
            : vuexInit
          _init.call(this, options)
        }
      }

      /**
       * Vuex init hook, injected into each instances init hooks list.
       */

      function vuexInit () {
        const options = this.$options
        // store injection
        if (options.store) {
          this.$store = typeof options.store === 'function'
            ? options.store()
            : options.store
        } else if (options.parent && options.parent.$store) {
          this.$store = options.parent.$store
        }
      }
    }
  ```
