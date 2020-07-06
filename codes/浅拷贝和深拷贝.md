```javascript
//二叉树节点
function TreeNode(val) {
      this.val = val;
      this.left = this.right = null;
}
//visit访问函数
function visit(node){
    console.log(node.val);
}
//先序遍历代码
let PreOrder = function(root){
	if(root){
		visit(root);
		PreOrder(root.left);
		PreOrder(root.right);
	}
}
//层序遍历代码
let levelOrder = function(root){
    let queue = [];
    queue.push(root);

    while(queue){
        let top = queue.shift();
        if(top){
            visit(top);   
            queue.push(top.left);
            queue.push(top.right);
        }   
    }   
}
```