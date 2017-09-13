
#  ES6 常用新特性学习

>   暂时略去某些不常用特性
>
>   《深入浅出 ES6》InfoQ 中文站 http://www.infoq.com/cn/es6-in-depth/

## 0 ES6简介

***ES6 == ECMAScript 6 == ECMAScript 2015*** ，Javascript 语言最新规范

**并非所有浏览器都支持ES6！** http://kangax.github.io/compat-table/es5/  

若要使用ES6，请使用IE10+ 及最新版的火狐、谷歌Chrome浏览器。

Babel 是一个广泛使用的ES6转码器，可以将ES6代码转为ES5代码，从而在现有环境执行。这意味着，你可以用ES6的方式编写程序，又不用担心现有环境是否支持。

>Babel 相关使用可参考阮一峰的《ES6标准入门》

## 1 let和const

与变量有关的问题：

-   JS没有块级作用域：在JS函数中的`var `声明，其作用域是**函数体的全部**。 
    -   在代码块内声明的变量，其作用域是整个函数作用域而不是块级作用域。
-   循环内变量过度共享

### let是更完美的var

`let` 与`var`一样，也可以用来声明变量，但它有着更好的作用域规则。

-   **let 声明的变量拥有块级作用域。**也就是说用`let` 声明的变量的作用域只是外层块，而不是整个外层函数。
-   **let 声明的全局变量不是全局对象的属性。**这就意味着，你不可以通过 `window.变量名 `的方式访问这些变量。它们只存在于一个不可见的块的作用域中，这个块理论上是 Web 页面中运行的所有JS代码的外层块。
-   形如 for (let x...) 的循环在每次迭代时**都为x创建新的绑定**。
-   let 声明的变量直到控制流到达该变量被定义的代码行时才会被装载，所以**在到达之前使用该变量会触发错误。**
-   **用 let 重定义变量会抛出一个语法错误（SyntaxError）。**

### const

`const`声明的变量只可以在声明时赋值，不可随意修改，否则会导致`SyntaxError`（语法错误）。

用`const`声明变量后必须要赋值，否则也抛出语法错误。

## 2 迭代器和 for-of 循环

### for-of循环

ES6之前的数组遍历方式：

```javascript
// for loop
for (var index = 0; index < myArray.length; index++) {
  console.log(myArray[index]); 
}
//or ES5 forEach
 myArray.forEach(function (value) {
   console.log(value); 
 });
//注意，不要使用 for(var index in myArray) {} 形式。for-in 循环用来遍历对象属性。
```

而在ES6中：

```javascript
for (var value of myArray) { 
  console.log(value); 
}
```

-   它可以正确响应 break 、continue 和 return 语句（forEach 不能）
-   不仅支持数组，还支持大多数**类数组对象**。如 DOM NodeList 对象 
-   也支持**字符串**遍历，它将字符串视为一系列的Unicode字符来进行遍历
-   同样支持  **Map 和 Set 对象**（ES6新增类型）遍历
-   **不**支持普通对象（用for-in）。

for-of 循环语句通过方法调用来遍历各种**集合**。这些集合对象都有一个 **迭代器** 方法。

### 迭代器 略

## 3 箭头函数 Arrow Functions

箭头符号在JavaScript诞生时就已经存在。箭头序列`-->`同样是单行注释的一部分（同`<!--`）。古怪的是，在HTML中`-->`之前的字符是注释的一部分，而在JS中`-->`之后的部分才是注释。但，只有当箭头在**行首**时才会注释当前行。这是因为在其它上下文中，`-->`是一个JS运算符：“**趋向于**”运算符！

`（while (n --> 0)  // "n goes to zero"）` 

### `=>`表达式

一个只有一个参数的简单函数，可以使用新标准中的箭头函数，它的语法：`标识符=>表达式`

#### 基础语法

```javascript
(param1, param2, …, paramN) => { statements }
(param1, param2, …, paramN) => expression
         // 等价于:  => { return expression; }

// 如果只有一个参数，圆括号是可选的:
(singleParam) => { statements }
singleParam => { statements }

// 无参数的函数需要使用圆括号:
() => { statements }
```

```javascript
//ES5
function () {  return console.log(_this.string);  }
//ES6
() => console.log(this.string)
```

#### 高级语法

```javascript
// 返回对象字面量时应当用圆括号将其包起来:
params => ({foo: bar})

// 支持 多重参数（Rest parameters） 和 默认参数:
(param1, param2, ...rest) => { statements }
(param1 = defaultValue1, param2, …, paramN = defaultValueN) => { statements }

// 参数列表中的解构赋值也是被支持的
var f = ([a, b] = [1, 2], {x: c} = {x: a + b}) => a + b + c;
f();  // 6
```

无需输入`function`和`return`，一些小括号、大括号以及分号也可以省略。例：

```javascript
// ES5
var selected = allJobs.filter(function (job) {
    return job.isSelected();
});
// ES6
var selected = allJobs.filter(job => job.isSelected());
```

####  多重参数

如果要写一个接受多重参数（也可能没有参数，或者是[不定参数、默认参数](http://www.infoq.com/cn/articles/es6-in-depth-rest-parameters-and-defaults)、[参数解构](http://www.infoq.com/cn/articles/es6-in-depth-destructuring)）的函数，需要用小括号包裹参数：

```javascript
// ES5
var total = values.reduce(function (a, b) {
     return a + b;
}, 0);
// ES6
var total = values.reduce((a, b) => a + b, 0);
```

#### 块语句

除表达式外，箭头函数还可以包含一个**块语句**。例：

```javascript
// ES5
$("#confetti-btn").click(function (event) {
    playTrumpet();
    fireConfettiCannon();
});
// ES6
$("#confetti-btn").click(event => {
    playTrumpet();
    fireConfettiCannon();
});
```

注意，使用了**块语句**的箭头函数**不会自动返回值**，你需要使用`return`语句将所需值返回。

#### 创建普通对象

当使用箭头函数创建**普通对象**时，你总是需要将对象包裹在小括号里。

```javascript
// 为每一个puppy创建一个新的空对象
var chewToys = puppies.map(puppy => {});   // 这样写会报Bug！
var chewToys = puppies.map(puppy => ({})); // puppy => ({}) 用小括号包裹空对象就可以了。
```

一个空对象`{}`和一个空的块`{}`看起来完全一样。ES6中的规则是，紧随箭头的 `{` 被解析为块的开始，而不是对象的开始。因此，`puppy => {}`这段代码就被解析为没有任何行为并返回`undefined`的箭头函数。

#### this

普通`function`函数和箭头函数的行为有一个微妙的区别，**箭头函数没有它自己的this值**，箭头函数内的`this`值**继承自外围作用域。**

在ES6中，`this`需要遵循以下规则：

-   通过`object.method()`语法调用的方法使用**非箭头函数定义**，这些函数需要从调用者的作用域中获取一个有意义的`this`值。
-   其它情况全都使用箭头函数。

```javascript
// ES6
{
   ...
   addAll: function addAll(pieces) {
        _.each(pieces, piece => this.add(piece));
   },
   ...
}
```

`addAll`方法从它的调用者处获取了`this`值，内部函数是一个箭头函数，所以它继承了**外围作用域**的`this`值。

在ES6中你可以用更简洁的方式编写对象字面量中的方法，所以上面这段代码可以简化成：

```javascript
// ES6的方法语法
{
    ...
    addAll(pieces) {
        _.each(pieces, piece => this.add(piece));
    },
    ...
}
```

箭头函数与非箭头函数间还有一个细微的区别，箭头函数不会获取它们自己的`arguments`对象。

## 4 类Class

### 方法定义语法

```javascript
var obj = {
     // 现在不再使用function关键字给对象添加方法
     // 而是直接使用属性名作为函数名称。
     method(args) { ... },
     // 只需在标准函数的基础上添加一个“*”，就可以声明一个生成器函数。
     *genMethod(args) { ... },
     // 借助|get|和|set|可以在行内定义访问器。
     // 只是定义内联函数，即使没有生成器。
     // 注意通过这种方式装载的getter不能接受参数
     get propName() { ... },
     // 注意通过这种方式装载的setter至少接受一个参数
     set propName(arg) { ... },
     // []语法可以用于任意支持预计算属性名的地方，来满足上面的第4中情况。
     // 这意味着你可以使用symbol，调用函数，联结字符串
     // 或其它可以给property.id求值的表达式。
     // 这个语法对访问器或生成器同样有效，我在这里只是举个例子。
     [functionThatReturnsPropertyName()] (args) { ... }
};
```

### 类定义语法

>   完整内容参考 https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Classes

#### 类声明

你首先需要声明你的类，然后访问它，否则会抛出一个[`ReferenceError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ReferenceError)：

```javascript
class Polygon {
  // 构造函数，可选，一个类只能拥有一个名为 constructor 的方法，否则会抛出 SyntaxError 异常。
  // 不可以用生成器作为构造函数。
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
}
```

#### 类表达式

一个**类表达式**是定义类的另一种方式。类表达式可以被命名或未被命名。赋予命名类表达式的名称是类的主体的本地名称。

```javascript
/* 匿名类 */ 
let Polygon = class {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
};

/* 命名的类 */ 
let Polygon = class Polygon {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
};
```

#### 类体和方法定义

类的成员需要定义在一对花括号 `{}` 里，花括号里的代码和花括号本身组成了类体。类成员包括类构造器和类方法（包括静态方法和实例方法）。

##### 原型方法

```javascript
class Polygon {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
  get area() {
    return this.calcArea()
  }
  calcArea() {
    return this.height * this.width;
  }
}
const square = new Polygon(10, 10);
//调用area方法 100
console.log(square.area);
```

##### 静态方法

`static` 关键字用来定义类的静态方法。静态方法是指那些不需要对类进行[实例化](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Introduction_to_Object-Oriented_JavaScript#The_object_(class_instance))，使用类名就可以直接访问的方法，需要注意的是静态方法不能被实例化的对象调用。静态方法经常用来作为**工具函数**。

```javascript
class Point {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }
    static distance(a, b) {
        const dx = a.x - b.x;
        const dy = a.y - b.y;
        return Math.sqrt(dx*dx + dy*dy);
    }
}

const p1 = new Point(5, 5);
const p2 = new Point(10, 10);
console.log(Point.distance(p1, p2));
```

#### 使用 `extends` 创建子类

`extends` 关键字在类声明或类表达式中用于创建一个类作为另一个类的一个子类。

```javascript
class Dog extends Animal {
  speak() {
    console.log(this.name + ' barks.');
  }
}
```

请注意，类不能扩展常规（不可构造/非构造的）对象。如果要继承常规对象，可以改用[`Object.setPrototypeOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf):

```javascript
var Animal = {
  speak() {
    console.log(this.name + ' makes a noise.');
  }
};

class Dog {
  constructor(name) {
    this.name = name;
  }
  speak() {
    super.speak();//调用对象的父对象上的函数。
    console.log(this.name + ' barks.');
  }
}
//继承常规对象
Object.setPrototypeOf(Dog.prototype, Animal);

var d = new Dog('Mitzie');
d.speak();
```

## 5 模板字符串 template strings

模板字符串除了使用**反撇号字符** ` 代替普通字符串的引号 ' 或 " 外，它们看起来与普通字符串并无二致。在最简单的情况下，它们与普通字符串的表现一致：  

```javascript
context.fillText(`Ceci n'est pas une chaîne.`, x, y);
```

模板字符串为JavaScript提供了简单的**字符串插值**功能:

```javascript
function authorize(user, action) {
  if (!user.hasPrivilege(action)) {
    throw new Error(
      `用户 ${user.name} 未被授权执行 ${action} 操作。`);
  }
}
```

`${user.name}`和`${action}`被称为模板占位符，JavaScript将把`user.name`和`action`的值插入到最终生成的字符串中. 

-   模板占位符中的代码可以是任意JavaScript表达式，所以函数调用、算数运算等这些都可以作为占位符使用，你甚至可以在一个模板字符串中嵌套另一个，称之为模板套构（template inception）。

- 如果这两个值都不是字符串，可以按照常规将其转换为字符串。如：如果action是一个对象，将会调用它的.toString()方法将其转换为字符串值。

- 如果你需要在模板字符串中书写反撇号，你必须使用反斜杠将其转义  \`\ \`\`等价于"`"。

- 同样，如果你需要在模板字符串中引入字符$和{。无论你要实现什么样的目标，你都需要用反斜杠转义每一个字符：                                         

    \`\\$\`和\`\\{\`.

- 与普通字符串不同的是，模板字符串可以多行书写。模板字符串中所有的空格、新行、缩进，都会原样输出在生成的字符串中。

    ### 标签模板（tagged templates）略

## 6 不定参数和默认参数

###  不定参数

不定参数可以解决可读性与参数索引的问题。

```javascript
function containsAll(haystack, ...needles) {
  for (var needle of needles) {
    if (haystack.indexOf(needle) === -1) {
      return false;
    }
  }
  return true;
}
```

在所有函数参数中，只有**最后一个**才可以被标记为不定参数。

如果没有额外的参数，不定参数就是一个空数组，它永远不会是undefined。

###  默认参数

JavaScript有严格的默认参数格式，未被传值的参数默认为undefined。ES6引入了一种新方式，可以指定任意参数的默认值。

```javascript
function animalSentence(ani2="tigers", ani3="bears") {
    return `Lions and ${ani2} and ${ani3}! Oh my!`;// 模板字符串 template strings
}
```

默认参数的定义形式为`[param1[ = defaultValue1 ][, ..., paramN[ = defaultValueN ]]]`，对于每个参数而言，定义默认值时`=`后的部分是一个**表达式**，如果调用者没有传递相应参数，将使用该表达式的值作为参数默认值。

默认参数有几个微妙的细节需要注意：

-   默认值表达式在函数调用时**自左向右求值**，这一点与Python不同。这也意味着，**默认表达式可以使用该参数之前已经填充好的其它参数值。**举个例子，我们优化一下刚刚那个动物语句函数：

    ```javascript
    function animalSentenceFancy(ani2="tigers",ani3=(ani2 == "bears") ? "sealions" : "bears")
    {
      return `Lions and ${ani2} and ${ani3}! Oh my!`;
    }
    //animalSentenceFancy("bears")将返回“Lions and bears and sealions. Oh my!”。
    ```

- 传递undefined值等效于不传值。

- 没有默认值的参数隐式默认为undefined

## 7 模块 Modules

>   同时参考 http://es6.ruanyifeng.com/#docs/module

### 模块基础知识

每一个ES6模块都是一个包含JS代码的文件，模块本质上就是一段脚本，而不是用`module`关键字定义一个模块，但是模块与脚本还是有两点区别：

-   在ES6模块中，无论你是否加入“`use strict;`”语句，默认情况下模块都是在严格模式下运行。
-   在模块中你可以使用`import`和`export`关键字。

#### export

默认情况下，模块中的所有声明相对于模块而言都是寄存在**本地**的。如果你希望公开在模块中声明的内容，并让其它模块加以使用，你一定要***导出*** 这些功能。想要导出模块的功能有很多方法，其中最简单的方式是添加`export`关键字。

可以`export`所有的最外层函数、类以及`var`、`let`或`const`声明的变量。

```javascript
export var year = 1958;
export function multiply(x, y) {
  return x * y;
};
```

需要特别注意的是，`export`命令规定的是对外的接口(而非值)，必须与模块内部的变量建立一一对应关系。`function`和`class`的输出，也必须遵守这样的写法。

```javascript
// 报错
export 1;
// 报错
var m = 1; export m;
// 报错
function f() {}
export f;
//正确写法：在接口名与模块内部变量之间，建立一一对应的关系
export var m = 1;
var m = 1;  export {m};
var n = 1;  export {n as m};
export function f() {};
function f() {}   
export {f};
```

##### Export列表

不需要标记每一个被导出的特性，你只需要在花括号中按照列表的格式写下你想导出的所有名称：

```javascript
export {detectCats, Kittydar};//不一定非要把它放在模块文件的首行，可以在模块文件最外层作用域的任一处
// 此处不需要 `export`关键字
function detectCats(canvas, options) { ... }
 class Kittydar { ... }
```

##### 重命名import和export

有时候，导出的名称会与需要使用的其它名称产生冲突，ES6为你提供了重命名的方法解决这个问题，当你在导入名称时可以这样做：

```javascript
//导入的时候重命名
import {flip as flipOmelet} from "eggs.js";
//导出的时候也可以重命名
function v1() { ... }
function v2() { ... }

export {
   v1 as streamV1,
   v2 as streamV2,
   v2 as streamLatestVersion
};
```

##### export default

使用`import`命令的时候，用户需要知道所要加载的变量名或函数名，否则无法加载。但是，用户肯定希望快速上手，未必愿意阅读文档，去了解模块有哪些属性和方法。

为了给用户提供方便，让他们不用阅读文档就能加载模块，就要用到`export default`命令，为模块指定默认输出。其他模块加载该模块时，`import`命令可以为该匿名函数指定任意名字。

`export default`命令只能使用一次。

```javascript
// export-default.js
export default function () {
  console.log('foo');
}
//-------------------------------
// 或者 非匿名函数
export default function foo() {
  console.log('foo');
}
// 或者
function foo() {
  console.log('foo');
}
export default foo;
//------------------------------------------------------
// import-default.js
import customName from './export-default';
customName(); // 'foo'

```

```javascript
let myObject = {
      field1: value1,
      field2: value2
    };
export {myObject as default};
//or
export default {
      field1: value1,
      field2: value2
    };
```

本质上，`export default`就是输出一个叫做`default`的变量或方法，然后系统允许你为它取任意名字。正是因为`export default`命令其实只是输出一个叫做`default`的变量，所以它后面不能跟变量声明语句。

因为`export default`本质是将该命令后面的值，赋给`default`变量以后再默认，所以可以直接将一个值写在`export default`之后。

```javascript
// 正确:指定外对接口为default
export default 42;
// 报错:没有指定对外的接口
export 42;
```

### import

使用`export`命令定义了模块的对外接口以后，其他 JS 文件就可以通过`import`命令加载这个模块。

```javascript
// main.js
import {firstName, lastName, year} from './profile';
```

`import`命令接受一对大括号，里面指定要从其他模块导入的变量名。大括号里面的变量名，**必须**与被导入模块（`profile.js`）对外接口的名称相同。

如果想为输入的变量重新取一个名字，`import`命令要使用`as`关键字，将输入的变量重命名。

```javascript
import { lastName as surname } from './profile';
```

如果想在一条`import`语句中，同时输入默认方法和其他变量，可以写成下面这样。

```javascript
import _, { each } from 'lodash';
```

`import`命令具有提升效果，会提升到整个模块的头部，首先执行。

由于`import`是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构。

```javascript
// 报错
import { 'f' + 'oo' } from 'my_module';
```

#### 模块对象 / 整体加载

用星号（`*`）指定一个对象，所有输出值都加载在这个对象上面。

```javascript
// circle.js 输出两个方法area和circumference。
export function area(radius) {
  return Math.PI * radius * radius;
}
export function circumference(radius) {
  return 2 * Math.PI * radius;
}

// main.js
import { area, circumference } from './circle';
console.log('圆面积：' + area(4));
//或 整体加载
import * as circle from './circle';
console.log('圆面积：' + circle.area(4));
```

当你`import *`时，导入的其实是一个模块命名空间对象，模块将它的所有属性都导出了。所以如果“circle”模块导出一个名为`area()`的函数，然后用上面这种方法“circle”将其全部导入后，你就可以这样调用函数了：`circle.area()`。

### 聚合模块

有时一个程序包中主模块的代码比较多，为了简化这样的代码，可以用一种统一的方式将其它模块中的内容聚合在一起导出，可以通过这种简单的方式将所有所需内容导入再导出：

```javascript
//world-foods.js
// 导入"sri-lanka"并将它导出的内容的一部分重新导出
export {Tea, Cinnamon} from "sri-lanka";
// 导入"singapore"并将它导出的内容全部导出
export * from "singapore";
```

与真正的导入内容的方法不同的是，这些导入内容再重新导出的方法不会在作用域中绑定你导入的内容。

>   如果你打算用`world-foods.js`中的`Tea`来写一些代码，你会发现当前模块作用域中根本找不到`Tea`。