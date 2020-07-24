## Iterator迭代器实现
#### 概念:
迭代器（Iterator）就是这样一种机制。它是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署 Iterator 接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）

原生具有Iterator接口的数据结构有：
**1、Array**
**2、String**
**3、函数的arguments对象**
**4、Set**
**5、Map**

#### 代码：
```javascript
function Iterator(arr){
    let nextIndex = 0;

    return {
	    next:function(){
			return nextIndex<arr.length?
			{value:arr[nextIndex++],done:false}:
			{value:undefined,done:true}
		}
	}
}

var it = Iterator('helloworld');
```
#### 效果：
![Alt text](./1595604960556.png)
