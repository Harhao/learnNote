### JavaScript知识文集
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
        
