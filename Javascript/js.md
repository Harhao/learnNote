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
 #### 2. 用原生JavaScript实现一个parseInt函数和parseFloat函数。
  - JavaScript的parseInt实现
    ```bash
    function parseInt(value){
      if(isNaN(value)){
        console.error(`${value} is not a number`);
        return NaN;
      }
      value = value>0?Math.floor(value):(-1*Math.floor(Math.abs(value)));
      return value;
    }
    ```
  - JavaScript的parseFloat实现
    ```bash
    function parseFloat(value){
      if(isNaN(value)){
        console.error(`${value} is not a number`);
        return NaN;
      }
      return value;
    }
    ```
 #### 3. 用原生JavaScript实现一个queryUrlParameter函数。
  - 例如 `https://www.baidu.com/s?ie=UTF-8&wd=hello` 取出参数key和value组成{"ie":"UTF-8","wd":"hello"}对象参数字面量
    ```bash
    function queryUrlParameter(url){
      let obj = {};
      if(url.indexOf('?') <0){
        return obj;
      }
      const arr = url.split('?');
      const params = arr[1].split("&");
      params.forEach(val=>{
        const tmpArr = val.split('=');
        obj[tmpArr[0]] = tmpArr[1];
      });
      return obj;
    }
    function queryUrlParameter(url){
      let obj = {};
      if(url.indexOf('?') <0){
        return obj;
      }
      let reg =/([^&?=]+)=([^&?=])+/g;
      url.replace(reg,function(){
        obj[arguments[1]] = arguments[2];
      });
      return obj;
    }
    ```
 #### 4. 用原生JavaScript实现一个Array.concat函数。
  - JavaScript实现一个类Array.concat函数
    ```bash
    Array.prototype.myConCat = function(arr){
      const _this = this;
      arr.forEach(ele =>{
        _this.push(ele);
      })
      return _this;
    }
     Array.prototype.myConCat = function(arr){
      const _this = this;
      return [..._this,...arr];
    }
    ```
  - JavaScript实现一个类Array.concat函数但是数组元素不可有相同
    ```bash
     Array.prototype.myConCat = function(arr){
      const _this = this;
      return new Set([..._this,...arr]);
    }
    ```
 #### 5. 用原生JavaScript实现一个获取字符串真实的长度,直接通过length获取字符串长度，对于\u0xfff以上的四字节字符会被认为是两个字符，通过ES6的正则修饰符\u可以正确匹配四字节字符串
  - JavaScript实现一个codePointLength函数
    ```bash
    function codePointLength(str){
      const result = str.match(/[\s\S]/gu);
      return result?result.length:0;
    }
    ```
