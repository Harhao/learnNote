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
#### 3. 利用JavaScript实现一个单向链表
  
    ```bash
    class ListChain {
      constructor() {
        this.size = 0;
        this.header = new Node(null, null);
      }
      find(header, currentIndex, index) {
        if (currentIndex === index) {
          return header;
        }
        return this.find(header.next, ++currentIndex, index);
      }
      addNode(value, index) {
        let prevNode = this.find(this.header, 0, index);
        prevNode.next = new Node(value, prevNode.next);
        this.size++;
        return prevNode.next;
      }
      removeNode(index) {
        let prevNode = this.find(this.header, 0, index);
        let node = prevNode.next;
        prevNode.next = node.next;
        node.next = null;
        this.size--;
        return node;
      }
      insertNode(value, index) {
        return this.addNode(value, index);
      }
      removeFirstNode() {
        return this.removeNode(0);
      }
      removeLastNode() {
        return this.removeNode(this.size);
      }
    }

    const link = new ListChain();
    link.addNode({name:'huahao',age:12},0);
    link.addNode({name:'kiwis',age:12},1);
    link.addNode({name:'harhao',age:13},2);
    link.insertNode({name:'zouyang',age:14},0);
    console.log(JSON.stringify(link));
    ```
