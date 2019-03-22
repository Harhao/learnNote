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
