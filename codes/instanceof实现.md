## instanceof实现
#### 概念:
`instanceof` 的功能是：判断一个实例是否是其父类型或祖先类型所生成的实例。
返回值为`Boolean`类型，`true`或`false`
obj instanceof constructor
#### 思路:
根据其功能，换言之即判断：
`constructor.prototype`是否在`obj的原型链`上

故可得：
1、取constructor的原型（constructor.prototype）
2、在原型链上取obj的原型（obj = obj._ _ proto _ _）
3、循环比较是否相等：
	（1）相等，则是该类型的实例，返回true
	（2）不想等，则继续在原型链往上找该实例的原型 obj = obj._ _ proto _ _
	（3）找到原型链的尽头（obj === null），则不是该类型的实例，返回false
	
#### 代码：
```javascript
function instanceOf(obj,constructor){
	//取constructor的原型
	let prototype = constructor.prototype;
	//取obj的原型
	obj = obj.__proto__;
	while(true){
		if(obj === prototype)
			return true;
		obj = obj.__proto__;
		if(obj === null)
			return false;
	}
}
```
