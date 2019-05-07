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
#### 3. 利用JavaScript实现一个链表
  - 实现一个单向链表
  
    ```bash
    class Node{
        constructor(value,next){
            this.next = next;
            this.value = value;
        }
    }
    class LinkChain{
        constructor(){
            this.size = 0;
            this.header = new Node(null,null);
        }
        find(header,currentIndex,index){
            if(index === currentIndex){
                return header;
            }
            return this.find(header.next,++currentIndex,index);
        }
        addNode(value,index){
            let prevNode = this.find(this.header,0,index);
            prevNode.next = new Node(value,prev.next);
            this.size++;
            return prevNode.next;
        }
        removeNode(index){
            let prevNode = this.find(this.header,0,index);
            let node = prevNode.next;
            prevNode.next = node.next;
            node.next =null;
            this.size --;
            return node;
        }
        insertNode(value,index){
            this.addNode(value,index);
        }
        removeFirstNode(){
            return this.removeNode(0);
        }
        removeLastNode(){
            return this.removeNode(this.size);
        }
        isEmpty(){
            return this.size === 0;
        }
        getSize(){
            return this.size;
        }
        getNode(index){
            this.checkIndex(index);
            if(this.isEmpty())
                return;
            return this.find(this.header,0,index).next;
        }
        checkIndex(index){
            if(index <0 || index>this.size)
                throw Error("index error");
        }

    }
    const chain = new LinkChain();
    chain.addNode({Name:"kiwis",age:12},0)
    chain.addNode({Name:"harhao",age:13},1)
    chain.insertNode({Name:'Miss chun',age:14},0)
    ```
#### 4. 利用JavaScript实现一个BST树的操作
  - 二叉搜索树的特点是父节点比左节点值大，比右节点值小
  
    ```bash
    class Node {
      constructor(value) {
        this.value = value;
        this.left = null;
        this.right = null;
      }
    }
    class BSTree {
      constructor() {
        this.root = null;
      }
      addNode(value) {
        this.root = this._addChild(this.root, value);
      }
      _addChild(node, val) {
        if (!node) {
          return new Node(value);
        }
        if (val > node.value) {
          node.right = this._addChild(node.right, val);
        } else if (val < node.value) {
          node.left = this._addChild(node.left, val);
        }
        return node;
      }
      getMaxNode(node){
        if(!node.right)
          return node;
        return this.getMaxNode(node.right);
      }
      getMinNode(root){
         if(!node.left)
          return node;
        return this.getMinNode(node.left);
      }
    }
    ```
#### 5. 利用JavaScript实现一个优先队列
  - 优先队列的特点是根据权重确定元素加入队列的位置(插队操作),比如医院的急诊室根据病情危急情况，优先救助比较急的患者。优先队列也是这个操作
  
    ```bash
    class Node{
      constructor(value,prority){
        this.value = value;
        this.prority = prority;
      }
    }
    class Queue{
      constructor(){
        this.queue = [];
      }
      enQueue(node){
        //如果当前队列为空
        if(!this.queue){
          this.queue.push(node);
          return;
        }
        this.queue.forEach((element,index)=>{
         //如果是优先权最小的元素入队列
         if(index == this.queue.length){
          this.queue.push(node);
         }
         if(element.prority <= node.prority){
          this.queue.splice(index,0,node);
         }
        });
      }
      deQueue(){
        this.queue.shift();
      }
      getLength(){
        return this.stack.length;
      }
    }
    ```
