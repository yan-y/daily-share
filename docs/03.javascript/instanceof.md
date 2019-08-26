## instanceof

instanceof运算符用于测试构造函数的prototype属性是否出现在对象的原型链中的任何位置

来看一个简单的例子：

```javascript
function Car(make, model, year) {
    this.make = make;
    this.model = model;
    this.year = year;
}
var auto = new Car('Honda', 'Accord', 1998);

console.log(auto instanceof Car); // true

console.log(auto instanceof Object); // true
```

### 语法

```javascript
object instanceof constructor
```

### 参数

`object`
要检测的对象

`constructor`
某个构造函数

## 描述

`instanceof`运算符用来检测`constructor.prototype`是否存在于参数`object`的原型链上。

```javascript
// 定义构造函数
function C(){} 
function D(){} 

var o = new C();


o instanceof C; // true，因为 Object.getPrototypeOf(o) === C.prototype


o instanceof D; // false，因为 D.prototype不在o的原型链上

o instanceof Object; // true,因为Object.prototype.isPrototypeOf(o)返回true
C.prototype instanceof Object // true,同上

C.prototype = {};
var o2 = new C();

o2 instanceof C; // true

o instanceof C; // false,C.prototype指向了一个空对象,这个空对象不在o的原型链上.

D.prototype = new C(); // 继承
var o3 = new D();
o3 instanceof D; // true
o3 instanceof C; // true 因为C.prototype现在在o3的原型链上
```

需要注意的是，如果表达式 `obj instanceof Foo` 返回`true`，则并不意味着该表达式会永远返回`true`，因为`Foo.prototype`属性的值有可能会改变，改变之后的值很有可能不存在于`obj`的原型链上，这时原表达式的值就会成为`false`。另外一种情况下，原表达式的值也会改变，就是改变对象`obj`的原型链的情况，虽然在目前的ES规范中，我们只能读取对象的原型而不能改变它，但借助于非标准的`__proto__`伪属性，是可以实现的。比如执行`obj.__proto__ = {}`之后，`obj instanceof Foo`就会返回`false`了。

### instanceof和多全局对象(多个frame或多个window之间的交互)

在浏览器中，我们的脚本可能需要在多个窗口之间进行交互。多个窗口意味着多个全局环境，不同的全局环境拥有不同的全局对象，从而拥有不同的内置类型构造函数。这可能会引发一些问题。比如，表达式 `[] instanceof window.frames[0].Array` 会返回`false`，因为 `Array.prototype !== window.frames[0].Array.prototype`，并且数组从前者继承。

起初，你会认为这样并没有意义，但是当你在你的脚本中开始处理多个frame或多个window以及通过函数将对象从一个窗口传到另一个窗口时，这就是一个有效而强大的话题。比如，实际上你可以通过使用 `Array.isArray(myObj)` 或者`Object.prototype.toString.call(myObj) === "[object Array]"`来安全的检测传过来的对象是否是一个数组。