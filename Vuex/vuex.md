## Vuex 问题解惑
- 我们不能直接修改state是怎么做到的？
  因为Vuex源码中通过 this._commiting 设置一个布尔值控制不允许外部对state进行修改。在vuex设置strict:true严格模式下，只有mutation才能操作state的修改
  而action背后的操作也是通过mutation的commit提交对state的数据修改。外部行为直接对state进行修改，this._committing始终为false。vuex通过断言检查this._commiting会抛出警告错误外部对state的修改。在vuex源码src的store.js的可以查看到原理实现。
  ```bash
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
  ```bash
  new Vue({
    el: '#app',
    router,
    store,
    components: { App },
    template: '<App/>'
  })
  ```
  vuex的安装注册是通过vue.mixin混合的方式在生命周期beforeCreate中,如果是非根实例组件，可以通过options.parent.$store这样的方式层层向上获取到一个store。并且通过将options.store绑定在this.$store上。
  ```bash
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
- state和getter是不是通过new Vue()来使其变为响应式的？
  获取state的方式在src目录下的store.js有定义,是通过一个vue实例_vm获取，而_vm是一个中央总线bus的一个vue对象实例：
  ```bash
  get state () {
    return this._vm._data.$$state
  }
  store._vm = new Vue({
    data: {
      $$state: state
    }
  })
  function resetStoreVM (store, state, hot) {
      const oldVm = store._vm

      // bind store public getters
      store.getters = {}
      const wrappedGetters = store._wrappedGetters
      const computed = {}
      forEachValue(wrappedGetters, (fn, key) => {
        // use computed to leverage its lazy-caching mechanism
        //computed 对象挂载至 vue实例 _vm的computed属性上，得益于vue的计算属性特性，数据的变更同样可以同步至其他相关组件上
        computed[key] = () => fn(store)
        Object.defineProperty(store.getters, key, {
          get: () => store._vm[key],
          enumerable: true // for local getters
        })
      })

      // use a Vue instance to store the state tree
      // suppress warnings just in case the user has added
      // some funky global mixins
      const silent = Vue.config.silent
      Vue.config.silent = true
      store._vm = new Vue({
        data: {
          $$state: state
        },
        computed
      })
      Vue.config.silent = silent

      // enable strict mode for new vm
      if (store.strict) {
        enableStrictMode(store)
      }

      if (oldVm) {
        if (hot) {
          // dispatch changes in all subscribed watchers
          // to force getter re-evaluation for hot reloading.
          store._withCommit(() => {
            oldVm._data.$$state = null
          })
        }
        Vue.nextTick(() => oldVm.$destroy())
      }
    } 
  ```
 - state内部是如何实现支持模块配置和模块嵌套的？
  vuex中的子module模块是在root模块下通过modules字段定义，在vuex中通过registerMoudle注册所有的子模块,使用递归遍历方式把所有的子模块数据挂载在一个
  root的父元素上，形成一个树形的状态树。
    ```bash
      function installModule (store, rootState, path, module, hot) {
        const isRoot = !path.length
        const namespace = store._modules.getNamespace(path)

        // register in namespace map
        if (module.namespaced) {
          store._modulesNamespaceMap[namespace] = module
        }

        // set state
        if (!isRoot && !hot) {
          const parentState = getNestedState(rootState, path.slice(0, -1))
          const moduleName = path[path.length - 1]
          store._withCommit(() => {
            Vue.set(parentState, moduleName, module.state)
          })
        }

        const local = module.context = makeLocalContext(store, namespace, path)

        module.forEachMutation((mutation, key) => {
          const namespacedType = namespace + key
          registerMutation(store, namespacedType, mutation, local)
        })

        module.forEachAction((action, key) => {
          const type = action.root ? key : namespace + key
          const handler = action.handler || action
          registerAction(store, type, handler, local)
        })

        module.forEachGetter((getter, key) => {
          const namespacedType = namespace + key
          registerGetter(store, namespacedType, getter, local)
        })

        module.forEachChild((child, key) => {
          installModule(store, rootState, path.concat(key), child, hot)
        })
      }
    ```
