## Vue双向响应原理
- vuejs对data数据进行双向绑定，对数据进行操作，会自动更新UI状态图，主要是使用了ES5的Object.defineProperty的数据劫持，
```
class Vue{
  constructor(options){
     this._data = options.data;
     observe(this._data,options.render);
     _proxy.call(this,options.data);
  }
}
function observe(data,cb){
 Object.keys(data).forEach(key=>{
  defineReactive(data,key,data[key],cb);
 })
}
function defineReactive(obj,key,val,cb){
  Object.defineProperty(obj,key,{
    enumerable:true,
    configurable:true,
    get:()=>{
      return val;
    },
    set:(newVal)=>{
      val = newVal;
      cb(val);
    }
  })
}
function render(val){
  console.log(val)
}
function _proxy(data){
  const self = this;
  Object.keys(data).forEach(key=>{
    Object.defineProperty(data,key,{
      enumerable:true,
      configurable:true,
      get:()=>{
        return self._data[key];
      },
      set:(newVal)=>{
        self._data[key] = newVal;
      }
    })
  })
}
let app = new Vue({
  el:'#app',
  data:{
    name:'Harhao',
    age:23
  },
  render
})
```
