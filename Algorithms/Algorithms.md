### JavaScript数据结构与算法
#### 1. 利用JavaScript实现一个栈的操作
  - 栈的特点是先入后出，只能往栈顶添加元素
  
    ```bash
    class Stack{
      constructor(){
        this.stack = [];
       }
       add(element){
        this.stack.push(element);
       }
       getLength(){
        return this.stack.length;
       }
       getTopElement(){
        this.stack.pop();
       }
    }
    ```
#### 2. 利用JavaScript实现一个队列的操作
  - 队列的特点是先入先出，并且往队列尾部添加元素
  
    ```bash
    class Queue{
      constructor(){
        this.queue = [];
      }
      add(element){
        this.queue.push(element);
      }
      shift(){
        this.queue.shift();
      }
      getLength(){
        return this.stack.length;
      }
    }
    ```
