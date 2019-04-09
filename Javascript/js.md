### JavaScript学习笔记
#### 1. 自定义实现一个call函数和apply，改变this的指向问题。call函数传入的是一个参数列表，而apply传入的是一个参数数组
  - JavaScript的apply和call函数传统使用方法
  
    ```bash
    function getValue(){
      console.log(...arguments);
    }
    let a = {};
    getValue.call(a,"hello","world");//=> print "hello world"
    getValue.apply(a,["hello","world"]);//=> print "hello world"
    ```
  - JavaScript的apply和call主要改变是在一个新的对象中添加添加一个新函数，然后调用函数,删除函数定义，最后返回函数返回值。
    ```bash
    //call自定义实现
    Function.prototype.newCall = function(context){
      context = context || window;
      context.fn = this;
      const args = [...arguments].slice(1);
      const result = context.fn(...args);
      delete context.fn;
      return result;
    }
    //apply自定义实现
    Function.prototype.newApply = function(context){
      context = context || window;
      /*
      * this的值为需要apply的函数
      * 例如: 
      * let a = {};
      * getValue.newApply(a,["hello","world"]);
      */
      context.fn = this;
      let result;
      if(arguments[1]){
        result = context.fn(...arguments[1]);
      }else{
        result = context.fn();
      }
      delete context.fn;
      return result;
    }
    ```
  - JavaScript的bind实现,bind函数返回的是一个函数,并且改变this的指向
    ```bash
    Function.protype.newBind = function(context){
      if(typeof this != 'function'){
        throw new TypeError("type error);
        const _this = this;
        const args = [...arguments].slice(1);
        return function F(){
          if(this instanceof F){
            return new _this(...args,...arguments);
          }
          return _this.apply(context,args.concat(...arguments));
        }
      }
    }
    ```
 #### 2. 防抖与节流,函数的防抖与节流都是多次防止触发事件函数，造成页面卡顿。在如滚动事件、表单提交按钮防止过快多次反复触发使用居多，在开发随着鼠标滚动dom元素进入视野，开始执行动画效果需要监听检测鼠标滚动事件，这个时候防抖与节流发挥很大的效果。
   - 
