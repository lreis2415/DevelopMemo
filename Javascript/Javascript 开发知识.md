# Javascript 开发知识积累

## 判断 undefined 和 null

>https://blog.csdn.net/itmyhome1990/article/details/47305207
```javascript
// undefined
if(typeof(exp) == 'undefined'){ alert("undefined"); }
// null
var exp = null; if (!exp && typeof(exp)!="undefined" && exp!=0){ alert("is null"); }
```
`typeof` 返回的是字符串，有六种类型： 
“number”、”string”、”boolean”、”object”、”function”、”undefined”

### 动态添加/移除对象属性

可以通过`prototype` 属性为之前定义的对象类型增加属性。这为该类型的所有对象，而不是仅仅一个对象增加了一个属性。  

为某个对象添加属性：

- `obj.newKey = value;`
- `obj[newKey] = value;` 此时的newKey 可以是从外面传进来的

#### 删除属性

可以用 `delete` 操作符删除一个不是继承而来的属性。

```javascript
//Creates a new object, myobj, with two properties, a and b.
var myobj = new Object;
myobj.a = 5;
myobj.b = 12;
//Removes the a property, leaving myobj with only the b property.
delete myobj.a;
```

### 复制、合并对象

[Object.assign()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)