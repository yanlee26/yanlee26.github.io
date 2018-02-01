---
date: 2016-06-08
layout: post
title: JS高级教程总结
thread: 9
categories: JS
tags: [BIT]
excerpt: Prefesional JS In Summary
---

###  Chapter3 基本概念

- 3.4数据类型

1. 五种简单类型（基本数据类型）：

Null,Undefined,String,Number,Boolean，symbol（ES6）

> 判断：`typeof`

1. 1 Undefined:undefined

> 变量声明而未初始化（显式初始化变量是好习惯）

1. 2 Null:null

> 空指针，此即typeof（null）===undefined的根本原因

* 由于ES数据类型具有动态性，故没必要定义其它类型 

2. 一种对象类型：Object（一组数据和功能的集合）

判断:`instanceof`

> ES 中Object类型是所有其它实例的基础，Object类型所
具有的所有属性和方法同样存在于具体对象中

```
constructor:保存用于创建当前对象的函数（构造函数即Object）
hasOwnProperty():检查给定的属性是否在当前对象实例中
isPrototypeOf():检查传入的对象是否是传入对象的原型
propertyIsEnumerable():是否可通过for-in枚举
toLocalString():返回对象字符串表示（与本区对应）
toString():返回对象字符串表示
valueOf():返回对象的字符串，数值或者布尔值表示。同toString
```
- 3.5操作符

1. 一元操作符

1. 递增递减：++a,a++: 前/后置操作时决定变量值在执行操作前/后改变
2. 加减：a+=n,a-=n
3. 位操作符：NOT(~),OR(|),AND(&),XOR(^),(<<),(>>),(>>>)
4. 布尔操作符：!,&&,||
5. 算术性操作符：+-*/
6. 关系操作符：>,<
7. 相等操作符 ：
    a. 相等与否（操作数成立则true）：==,!=
    b. 全等与否（比较之前不转换操作数）：===,!==
8. 条件操作符：variable=boolean_expression?true_value:false_value
9. 赋值操作符：+=，-=，*=，/=，%=，<<=,>>=,>>>=
10. 逗号操作符：var a,b,c

- 语句

> 1. if(condition) statement1 else statement2
> 2. do{statement}while(expression)
> 3. while(expression) statement
> 4. for(initialization;expression;post-loop-expression) statement
> 5. for(property in expression) statement
> 6. label:statement
> 7. break/continue:break 立即跳出循环（强制继续执行循环后语句），continue立即退出
循环（当前循环），但从循环顶部继续执行
> 8. with(expression) statement
> 9. switch(expression){
case value:statement1
break;
case value:statement2
break;
...
case default
break;
}

- 3. 7 函数-可以封装任意条语句，在任何地方任何时候执行

> 没有重载：可以为一函数编写两个定义，只要定义的签名（接受的参数类型和数量）不同即可
ES中函数没有签名，真正重载不可能做到只能模拟
function add(n){
return n+100
}
function add(n){
return n+200
}//覆盖掉上一个
var result=add(100);//300

###  Chapter4 变量作用域及内存

> JS 变量松散的本质决定了它只是在特定时间用于保存特定值的一个名字而已

4. 1 基本类型（简单的数据段）和引用类型（可能有多个值构成的对象）

> 因可以操作保存在变量中的实际值，基本数据类型按值访问；
引用类型值保存在内存中，不同于其它语言，JS不允许直接访问内存中的位置（操作实际对象）。
当复制保存着某个变量时，操作的是对象的引用；但在为对象添加属性时，操作的是实际的对象
访问变量的方式有按值和按引用两种，而参数只能按值传递。ES中所有函数的参数都是按值传递的！

[JS函数参数按值传递的](https://q.cnblogs.com/q/39352/)

```
function setName(obj){obj.name='yl';obj={};obj.name='hello'} 
var p={};setName(p);p.name;//'yl'
//即使在内部修改了参数的值，但原始的引用仍然不变。实际上，当函数内部重写obj时，该变量引用的就是
一个在函数执行完立即销毁的局部变量了。完全可以把ES函数的参数想象成局部变量
```
> 执行环境和作用域（execution context& scope）
EC:决定了变量或函数有权访问的其它数据，决定了它们各自的行为；每个EC都有一个与之关联的变量对象（vo）
,环境中定义的所有变量和函数都保存在这个对象中。虽然无法访问该对象，但解析器在处理数据时会在后台使用它。
某个执行环境中的所有代码执行完毕后，该环境被销毁，其中的所有变量和函数定义也随之而去。
每个函数都有自己的执行环境，当执行流进入一个函数时，函数环境就会被推入一个环境栈中；函数执行之后，栈将其
弹出，把控制权返回给之前的执行环境。
Scope:当代码在一个环境中执行时，会创建变量对象的一个作用域链（scope chain）,以保证对执行环境有权访问的
所有变量和函数的有序访问。其前端始终是当前执行的代码所在环境的变量对象。全局执行环境的变量对象始终都是作用
域链中的最后一个对象。
这些环境之间的联系是线性的有次序的，每个环境都可以向上搜索sc，以查询变量和函数名；但反之不行。
那sc可以延长吗？ 可以。。。
利用try-catch,with语句

4. 2 无块级作用域

> 1. 声明变量：使用var声明的变量会自动被添加到最近的执行环境中（在函数内部是局部环境，with语句中是
函数环境，如果忘记var则被添加到全局环境）2.查询标识符：当在某个环境中为了读取或写入而引用一个标识符
 时，必须通过搜索来确定标识符实际代表什么。
 
4. 3 垃圾回收GC

> 标记清除(mark-sweep)和引用计数(reference-counting)
mark-sweep:变量进入环境即将变量标记为‘进入环境’，逻辑上永远无法释放进入环境变量所占用的内存；当变量
离开时，记‘离开环境’

###  Chapter 5 引用类型

> 引用类型的值（对象）是引用类型的一个实例，ES中引用类型是用于将数据和功能组织在一起的一种数据结构。
常被不妥当地称为类，有时候也称对象定义。

5. 1 Object
5. 2 Array

- 检测：Array.isArray();instanceof Array
- 转换：toString(),join()方法
- 栈方法：push,pop
- 队列方法：shift,unshift
- 排序sort(),reverse()
- 操作方法：concat(),splice()
    a. delete:splice(0,2);b:insert:splice(0,2,'red','blue');c:replace:splice(2,1,'a','b')
- 位置方法：indexOf(),lastIndexOf()
- 迭代方法：every(),filter(),map(),forEach(),some()
- 归并：reduce(),reduceRight()

5. 3 Date:
Date.parse():接收一个表示日期字符串的参数，然后尝试解析成毫秒数
Date.UTC()：同样返回时间戳，在构建时与parse使用不同的信息。
> 日期和时间都是基于本地时区而非GMT来创建

5. 4 RegExp
5. 5 Function

> ES中函数即对象，每个函数都是Function的一个实例，函数名是指向函数对象的一个指针，与其它引用类型一样有属性和方法

- 5. 5.1 没有重载
上述重载案例与下边等价：

```
var add=function(n){return n+100};
add=function(n){return n+200}//覆盖上个
```
- 5.5.2函数声明与函数表达式

> js引擎（解析器）会率先读取函数声明，并使其在执行任何代码之前可用；而对
函数表达式则同解析普通语句一样，等到解析器执行到其所在代码行，才被解释执行。

- 5.5.3 作为值的函数（ES中函数也是变量，所以可作为值使用）
```
 function createComparisonFunction(propertyName) {
            return function(object1, object2){
                var value1 = object1[propertyName];
                var value2 = object2[propertyName];
        
                if (value1 < value2){
                    return -1;
                } else if (value1 > value2){
                    return 1;
                } else {
                    return 0;
                }
            };
        }
        var data = [{name: "Zachary", age: 28}, {name: "Nicholas", age: 29}];
        data.sort(createComparisonFunction("name"));
        alert(data[0].name);  //Nicholas
        data.sort(createComparisonFunction("age"));
        alert(data[0].name);  //Zachary     
```

- 5. 5.4 函数内部属性：arguments，this
- 5. 5.5 函数属性和方法

> 每个函数都包含两个属性：length（函数希望接收的参数个数）和prototype（对ES中引用类型而言，prototype保存了
其所有实例的属性和方法，即toString(),valueOf()等方法实际上保存在prototype名下，只不过通过各自对象的实例访问）

> 每个函数都包含两个非继承而来的方法：call(),apply():用途是在特定作用域中调用函数，实际上是设置函数体内this指向。
ES5中还有一个bind方法，用于创建一个函数实例，其this值会被绑定到
传给bind函数的值。

> 另外每个函数继承的toString(),toLocalString()，valueOf()始终返回函数代码
```
function sayColor=function(){alert(this.color)}
window.color='red';
var o={color:'blue'};
var objSayColor=sayColor.bind(o);
objSayColor();//'blue'
```
- 5.5.6 基本包装对象

> ES提供了三个特殊的引用类型（Boolean,Number,String），与其它引用类型类似但也具有各自基本类型相应的行为
注意：引用类型和基本类型主要区别就是对象生命周期：使用new操作符创建的引用类型实例，在执行流离开当前作用域之前
一直保存在内存中。而自动创建的基本包装对象，只存在于一行代码执行的瞬间，然后立即销毁。即我们不能给基本类型添加
属性和方法。
Object构造函数如工厂方法一样，根据传入值的类型返回基本包装对象实例

```
var obj=new Object('hello');obj instanceOf String;//true
```

- 5. 6.1 Boolean 

> Boolean实例重写了valueOf()方法并返回true/false；重写了toString()方法，返回'true'/'false'
注意：布尔表达式中所有对象都会被转换为true

```
var a=new Boolean(true)
undefined
a
Boolean {[[PrimitiveValue]]: true}
a.toString()
"true"
a.valueOf()
true
```
- 5. 6.2 Number

> 方法：toFixed(),toExponential(),toPrecision()

- 5. 6.3 String
1. 字符方法

> charAt(),charCodeAt()

2. 字符操作方法

> concat(),slice(),subString(),substr(),

3. 字符串位置方法

> indexOf(),lastIndexOf()

4. trim(),trimLeft(),trimRight()方法

5. 大小写转换方法

> toLocalUpperCase(),toUpperCase(),toLowerCase()

6. 模式匹配方法

> search(),replace(),match(),split()

7. localeCompare()方法，fromCharCode()方法

- 5. 7 单体内置对象

> 定义：由ES实现提供的不依赖于宿主环境的对象，即在ES程序执行之前既存在了

1. Global对象（兜底对象，任何不属于其它对象的属性和方法都是它的属性和方法）

> 如isNaN()，isFinite(),parseInt(),parseFloat(),encodeURI(),encodeURIComponent()
eval(),window对象，Math对象（min(),max(),ceil(),floor(),random()）

###  Chapter 6 面向对象 Object Oriented Programing

> ES对象：包含基本值，对象或函数的无序属性的集合。

6. 1.1 属性类型（数据属性+访问器属性）

1. 数据属性：

- [[Configurable]]:是否可配置，delete(true)
- [[Enumerable]]: 是否可枚举，for-in(true)
- [[Writable]]:是否可写(true)
- [[Writable]]:包含这个属性的数据值（undefined）

> ES5中Object.defineProperty(object,propertyName,descriptor)方法，包含属性所在对象，属性名，描述符对象三个参数，可以修改对象的默认特性。
注意：一旦把属性定义为不可配置的就再也甭能把它设置成可配置的了。

2. 访问器特性，对象的属性：

> 不包含数据值，包含一对getter，setter函数（非必须），有以下四个特性。访问器属性不能直接定义，必须用
Object.defineProperty()定义，Object.defineProperties()可以定义多个属性
- [[Configurable]]:同上(true)
- [[Enumerable]]:同上(true)
- [[Get]]:读取属性时调用的函数（undefined）
- [[Set]]:写入属性时调用的函数（undefined）
```
var book = {
    _year: 2004,
    edition: 1
};
Object.defineProperty(book, "year", {
    get: function(){
        return this._year;
    },
    set: function(newValue){
    
        if (newValue > 2004) {
            this._year = newValue;
            this.edition += newValue - 2004;
            }
        }
    });
    
book.year = 2005;
alert(book.edition);   //2
```

6. 1.3 读取属性特性

>Object.getOwnPropertyDescriptor()方法，两个参数属性所在的对象+读取其描述符的属性名，返回一对象.
JS中可以针对任何对象（BOM，DOM），使用该方法。

```
var descriptor = Object.getOwnPropertyDescriptor(book, "_year");
alert(descriptor.value);          //2004
alert(descriptor.configurable);   //false
alert(typeof descriptor.get);     //"undefined"
var descriptor = Object.getOwnPropertyDescriptor(book, "year");
alert(descriptor.value);          //undefined
alert(descriptor.enumerable);     //false
alert(typeof descriptor.get);     //"function"
```        

6. 2 创建对象

6. 2.1 工厂模式---用函数来封装以特定接口创建对象的细节

> 特点：虽然解决了创建多个相似对象的问题，但却没有解决对象识别问题(怎样知道对象类型)。

```
function createPerson(name, age, job){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function(){
        alert(this.name);
    };    
    return o;
}
var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
person1.sayName();   //"Nicholas"
person2.sayName();   //"Greg"
```

6. 2.2 构造函数模式---如Object，Array这样的原生构造函数，运行时会自动出现在EC中，也可创建自定义的。

```
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function(){
        alert(this.name);
    };    
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
person1.sayName();   //"Nicholas"
person2.sayName();   //"Greg"
alert(person1 instanceof Object);  //true
alert(person1 instanceof Person);  //true
alert(person2 instanceof Object);  //true
alert(person2 instanceof Person);  //true
alert(person1.constructor == Person);  //true
alert(person2.constructor == Person);  //true
alert(person1.sayName == person2.sayName);  //false   
```
> 特点：没有显式创建对象，没有return语句，直接将属性和方法赋给this，首字母大写，必须使用new构建实例。
优点：可以将构造函数的实例标识为一种特定的类型；
缺点：每个方法都要在每个实例上创建一遍，不同实例上同名函数是不同的。

```
this.sayName=new Function('alert(this.name)');//与声明函数逻辑上等价
person1.sayName===person2.sayName;//false
//优化，但此时全局函数有些名不副实
this.sayName=sayName;
function sayName(){}
```

6. 2.3 原型模式

> 我们创建的每个函数，都有一个指向一个对象的且是一个指针的prototype属性，其作用是包含可以有特定
类型所有实例共享的属性和方法。即prototype通过调用构造函数而创建的那个实例的原型对象。此时可以将所有实例共享其所包含的属性和方法。
优点：让任意实例共享其原型对象所包含的所有属性和方法。
缺点：省略了为构造函数传递初始化参数这一环节，导致所有实例默认获得相同属性，对于包含引用类型的属性而言，问题不可忽视

```
//原型模式案例
function Person(){}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    alert(this.name);
};

var person1 = new Person();
person1.sayName();   //"Nicholas"

var person2 = new Person();
person2.sayName();   //"Nicholas"

alert(person1.sayName == person2.sayName);  //true

alert(Person.prototype.isPrototypeOf(person1));  //true
alert(Person.prototype.isPrototypeOf(person2));  //true

//only works if Object.getPrototypeOf() is available
if (Object.getPrototypeOf){
    alert(Object.getPrototypeOf(person1) == Person.prototype);  //true
    alert(Object.getPrototypeOf(person1).name);  //"Nicholas"
}
//虽然可以通过实例访问原型中的值，但不能通过对象实例重写原型中的值。只是‘屏蔽’
person1.name = "Greg";
alert(person1.name);   //"Greg" – from instance
alert(person2.name);   //"Nicholas" – from prototype
//使用delete操作符完全可以删除该属性
delete person1.name;
alert(person1.name);   //"Nicholas" - from the prototype
//hasOwnProperty（）,in操作符结合
function hasPrototypeProperty(object,name){return !object.hasOwnProperty(name)&&(name in object)}
//ES5中的Object.keys()方法用于取得对象上所有可枚举的对象属性，接收一个对象为参数返回一个包含所有可枚举属性的字符串数组
var keys=Object.keys(Person.prototype)
//简化的原型语法,对象字面量形式创建的对象，但此时constructor不再指向Person了
//此种写法本质上重写了默认的prototype对象，因此constructor属性也就变成了新对象的constructor属性（指向Object）
Person.prototype={
constructor:Person,//加上此句话重设constructor，此举导致constructor的[[Enumerable]]被设置为true，默认false
    name:'Tom',
    age:'18',
    say:function(){}
}
//要消除此bug还需要再加这句话
Object.defineProperty(Person.prptotype,'constructor',{
enumerable:false,
value:Person
})
//原生对象的原型
Array.prototype===[].__proto__
String.prototype===''.__proto__
Object.prototype==={}.__proto__
//此方法困境
Person.prototype = {
        constructor: Person,
        name : "Nicholas",
        age : 29,
        job : "Software Engineer",
        friends : ["Shelby", "Court"],
        sayName : function () {
            alert(this.name);
        }
    };
var person1 = new Person();
var person2 = new Person();
person1.friends.push("Van");
alert(person1.friends);    //"Shelby,Court,Van"
alert(person2.friends);    //"Shelby,Court,Van"
alert(person1.friends === person2.friends);  //true

```
6. 2.4 组合使用构造函数和原型模式---用途广泛，认可度最高，首选

```
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.friends = ["Shelby", "Court"];
}
Person.prototype = {
    constructor: Person,
    sayName : function () {
        alert(this.name);
    }
};
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
person1.friends.push("Van");
alert(person1.friends);    //"Shelby,Court,Van"
alert(person2.friends);    //"Shelby,Court"
alert(person1.friends === person2.friends);  //false
alert(person1.sayName === person2.sayName);  //true
```
6. 2.5 动态原型模式

> 把所有信息封装在构造函数中，通过构造函数初始化原型，保持了同时使用构造函数和原型的优点。即
可以通过检查某个应该存在的方法是否有效来决定是否需要初始化原型.
谨记：此时不能使用对象字面量重写原型，否则会切断实例与新原型之间的联系

```
function Person(name, age, job){    
    //properties
    this.name = name;
    this.age = age;
    this.job = job;
    
    //methods
    if (typeof this.sayName != "function"){
    
        Person.prototype.sayName = function(){
            alert(this.name);
        };
        
    }
}
var friend = new Person("Nicholas", 29, "Software Engineer");
friend.sayName();
```

6. 2.6 寄生构造函数模式

> 返回的对象与构造函数或者构造函数与原型属性之间没关系。

```
function SpecialArray(){       
    //create the array
    var values = new Array();
    
    //add the values
    values.push.apply(values, arguments);
    
    //assign the method
    values.toPipedString = function(){
        return this.join("|");
    };
    
    //return it
    return values;        
}

var colors = new SpecialArray("red", "blue", "green");
alert(colors.toPipedString()); //"red|blue|green"
alert(colors instanceof SpecialArray);
```

6. 2.7 稳妥构造函数模式

```
function Person(name,age,job){
var o=new Object;
o.sayName=function(){alert(name)}
return o
}
var p1=new Person('yl',26,'software engineer');
p1.sayName();//'yl'
```
6. 3 继承（接口继承和实现继承（ES仅支持此继承））---依赖原型链继承
6. 3.1 原型链

> 基本思想：利用原型让一个引用类型继承另一个引用类型的属性和方法。

```
function SuperType(){
    this.property = true;
}

SuperType.prototype.getSuperValue = function(){
    return this.property;
};

function SubType(){
    this.subproperty = false;
}

//inherit from SuperType
SubType.prototype = new SuperType();

SubType.prototype.getSubValue = function (){
    return this.subproperty;
};

var instance = new SubType();
alert(instance.getSuperValue());   //true

alert(instance instanceof Object);      //true
alert(instance instanceof SuperType);   //true
alert(instance instanceof SubType);     //true

alert(Object.prototype.isPrototypeOf(instance));    //true
alert(SuperType.prototype.isPrototypeOf(instance)); //true
alert(SubType.prototype.isPrototypeOf(instance));   //true
```

6. 3.2 借用构造函数

```
function SuperType(){
    this.colors = ["red", "blue", "green"];
}

function SubType(){  
    //inherit from SuperType
    SuperType.call(this);
}
//传递参数
    function SubType(){  
            //inherit from SuperType passing in an argument
            SuperType.call(this, "Nicholas");
            
            //instance property
            this.age = 29;
        }

var instance1 = new SubType();
instance1.colors.push("black");
alert(instance1.colors);    //"red,blue,green,black"

var instance2 = new SubType();
alert(instance2.colors);    //"red,blue,green"
```

6. 3.3 组合继承

> 使用原型链实现对原型属性和方法的继承，通过借用构造函数实现对实例属性的继承

```
function SuperType(name){
    this.name = name;
    this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function(){
    alert(this.name);
};

function SubType(name, age){  
    SuperType.call(this, name);
    
    this.age = age;
}

SubType.prototype = new SuperType();

SubType.prototype.sayAge = function(){
    alert(this.age);
};

var instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
alert(instance1.colors);  //"red,blue,green,black"
instance1.sayName();      //"Nicholas";
instance1.sayAge();       //29

var instance2 = new SubType("Greg", 27);
alert(instance2.colors);  //"red,blue,green"
instance2.sayName();      //"Greg";
instance2.sayAge();       //27
```
6. 3.4 原型式继承

```
var person = {
    name: "Nicholas",
    friends: ["Shelby", "Court", "Van"]
};

var anotherPerson = Object.create(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");

var yetAnotherPerson = Object.create(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");

alert(person.friends);   //"Shelby,Court,Van,Rob,Barbie"
```
6. 3.5 寄生式继承

6. 3.6 寄生组合式继承

```
function object(o){
    function F(){}
    F.prototype = o;
    return new F();
}

function inheritPrototype(subType, superType){
    var prototype = object(superType.prototype);   //create object
    prototype.constructor = subType;               //augment object
    subType.prototype = prototype;                 //assign object
}
                        
function SuperType(name){
    this.name = name;
    this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function(){
    alert(this.name);
};

function SubType(name, age){  
    SuperType.call(this, name);
    
    this.age = age;
}

inheritPrototype(SubType, SuperType);

SubType.prototype.sayAge = function(){
    alert(this.age);
};

var instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
alert(instance1.colors);  //"red,blue,green,black"
instance1.sayName();      //"Nicholas";
instance1.sayAge();       //29
var instance2 = new SubType("Greg", 27);
alert(instance2.colors);  //"red,blue,green"
instance2.sayName();      //"Greg";
instance2.sayAge();       //27
```

###  Chapter 7 函数表达式(与函数声明的区别)

7. 1 递归

arguments.callee是一个指向正在执行函数的指针，实现递归
```
function factorial(num){
    if (num <= 1){
        return 1;
    } else {
        //return num*factorial(num-1)
        return num * arguments.callee(num-1);
    }
}
var anotherFactorial = factorial;
factorial = null;
//alert(anotherFactorial(4));  //error
alert(anotherFactorial(4));  //24
// //严格模式下debug---使用命名函数表达式
var factorial=(function f(num){
if(num<-1){return 1}
else{return num*f(num-1)}
})
```

7. 2 闭包closure

> 匿名函数与闭包：前者-创建一个函数并赋值给变量；后者-有权访问另一个函数作用域中变量的函数（在一个函数内创建另一函数）。
原理：明白作用域链的概念，当某函数被调用时，会创建一个EC及相应的作用域链；
然后使用arguments和其它命名参数的值来初始化函数的活动对象（activation object）
此时，外部函数作用域链处于第二位，再外的第三位，。。。，最外到全局执行环境。
其实，作用域链包含两级变量对象--本地活动对象和全局变量对象，其本质是一个指向
变量对象的指针列表，仅仅引用并不包含变量对象。

7. 2.1 闭包与变量

> 闭包的副作用：只能取得包含函数中任何变量的最后一个值。

```
function createFunctions(){
        var result = new Array();
        for (var i=0; i < 10; i++){
            result[i] = function(){
                return i;
            };
        }
        return result;
    }
var funcs = createFunctions();
    //every function outputs 10
    //原因：每个函数作用域链中都保存着createFunctions()函数的活动对象，它们引用的是
    同一个变量i。当createFunction（）函数返回后，i都是10.
    for (var i=0; i < funcs.length; i++){
        document.write(funcs[i]() + "<br />");
    }
//解决方案如下：创建一个匿名函数强制让闭包行为符合预期（函数参数是按值传递的）
for (var i=0; i < 10; i++){
                    result[i] = function(num){
                        return function(){
                            return num;
                        };
                    }(i);
                }
//或者用ES6语法变var i为let i
```

7.2.2 关于this对象

> this对象是在运行时基于函数的执行环境绑定的：在全局下this===window；当函数被作为某个对象的方法
调用时，this===调用其的对象。但匿名函数的作用域具有全局性，其this对象通常指向window；除非通过call（）
apply()改变。

```
var name = "The Window";
var object = {
    name : "My Object",
    getNameFunc : function(){
        //var that=this;
        return function(){
            return this.name;
            //return that.name;
        };
    }
};
alert(object.getNameFunc()());  //"The Window"
//细微变化可能改变this
var name='window';
var obj={name:'obj',getName:function(){return this.name}}
obj.getName();//'obj'
(obj.getName)();//'obj'
(obj.getName=obj.getName)();//'window',this得不到维持

```

> 原因分析：每个函数在调用时都会自动取得两个特殊变量this和arguments；内部函数在搜索
此二变量时只会搜索到其活动对象为之，故永不可能直接访问外部函数中此二变量。一种解决方式是把外部作用
域中this对象保存在闭包中。如上注释部分。

7. 2.3 内存泄漏

> 闭包会引用包含函数的整个活动对象！即使闭包不直接引用ele，包含函数的活动对象也仍然
会保存一个引用。

```
function assignHandler(){
    var ele=document.getElementById('xx');
    var id=ele.id;//抽出闭包中循环引用的变量
    ele.onclick=function(){}
    ele=null;//清除对dom对象的引用
}
```

7. 2.4 模仿块级作用域

> JS 从不会告诉你是否声明了同一个变量；只会无视后续声明（会执行声明的初始化），匿名函数可以
模仿块级作用域并避免此问题。并且只要做到闭包中没有指向匿名函数的引用，就可以减少闭包占用内存的问题。

```
(function(){//block scope})();
var someFunction=function(){//block scope}
function(){//block scope}();//error ,函数声明不能跟（），js将function当作函数声明的开始
```

7. 2.5 private variables私有变量

> 事实上，JS中没有私有成员的概念；所有对象的属性都是公有的。但有个私有变量的概念---任何在函数
中定义的变量。
特权方法（privileged method）: 有权访问私有变量和私有函数的公有方法。有以下两种
但在函数中定义特权有个缺点：必须使用构造函数模式实现，其缺点是每个实例都会创建一组新方法，使用静态私有变量
可以避免此问题（私有变量和函数是由实例共享的，作为一个闭包总是保存着对包含作用域的引用）。

```
//模式一：在构造函数中定义特权方法，将特权方法作为闭包（有权访问在构造函数中的所有属性和方法）
function MyObject(){
    //私有变量和私有函数
    var privateVariable=10;
    function privateFunction(){
        return false;
    }
    //特权方法
    this.publicMethod=functionn(){
        privateVariable++;
        return privateFunciton();
    }
}
// 使用静态私有变量
(function(){
    var name = "";
    Person = function(value){                
        name = value;                
    };
    Person.prototype.getName = function(){
        return name;
    };
    Person.prototype.setName = function (value){
        name = value;
    };
})();
var person1 = new Person("Nicholas");
alert(person1.getName());   //"Nicholas"
person1.setName("Greg");
alert(person1.getName());   //"Greg"
var person2 = new Person("Michael");
alert(person1.getName());   //"Michael"
alert(person2.getName());   //"Michael"

```

7. 4.2 模块模式

> 上述模式用于为自定义类型创建私有变量和特权方法，而模块模式则是为单例创建私有变量和特权的方法。

```
function BaseComponent(){
}

function OtherComponent(){
}

var application = function(){

//private variables and functions
var components = new Array();

//initialization
components.push(new BaseComponent());

//public interface
return {
getComponentCount : function(){
return components.length;
},

registerComponent : function(component){
if (typeof component == "object"){
    components.push(component);
}
}
};
}();

application.registerComponent(new OtherComponent());
//增强的模块模式
function BaseComponent(){
}

function OtherComponent(){
}

var application = function(){

    //private variables and functions
    var components = new Array();

    //initialization
    components.push(new BaseComponent());

    //create a local copy of application
    var app = new BaseComponent();

    //public interface
    app.getComponentCount = function(){
        return components.length;
    };

    app.registerComponent = function(component){
        if (typeof component == "object"){
            components.push(component);
        }
    };

    //return it
    return app;
}();
alert(application instanceof BaseComponent);
application.registerComponent(new OtherComponent());
alert(application.getComponentCount());  //2
```

###  Chapter 8 BOM

8. 1 window对象

8. 1.1 global scope

> 定义的全局变量和在window上直接定义的变量细微差别就是前者不可以用delete操作符删除
var a='hello';window.b='world';delete window.a;//false;delete window.b;//true
因var 添加的window属性的[[Configurable]]===false

8. 1.2 窗口关系及框架
8. 1.3 窗口位置
```
//使用下边代码可以跨浏览器取得窗口左边和上边位置。
var leftPos = (typeof window.screenLeft == "number") ? 
                          window.screenLeft : window.screenX;
var topPos = (typeof window.screenTop == "number") ? 
                    window.screenTop : window.screenY;
alert("Left: " + leftPos);
alert("Top: " + topPos);
```
8.1.4 窗口大小
```
var pageWidth = window.innerWidth,
    pageHeight = window.innerHeight;
    if (typeof pageWidth != "number"){
        if (document.compatMode == "CSS1Compat"){
            pageWidth = document.documentElement.clientWidth;
            pageHeight = document.documentElement.clientHeight;
        } else {
            pageWidth = document.body.clientWidth;
            pageHeight = document.body.clientHeight;
        }
    }
    alert("Width: " + pageWidth);
    alert("Height: " + pageHeight);
```
8. 1.5 导航和打开

```
window.open();
// 接收四个参数：URL，窗口目标，一个特性字符串，布尔值
```

8. 1.6 setInterval()和setTimeOut()
8. 1.7 系统对话框：alert(),confirm(),prompt()
8. 2 location 对象

```
window.location===document.location
```
8. 3 navigator对象
8. 4 screen对象
8. 5 history对象

###  Chapter 9 客户端检查

###  Chapter 10 DOM

> DOM是针对HTML和XML文档的一个API，描绘了一个层次化的节点树，允许开发人员增删改
查页面的一部分。注意IE中的DOM对象都是以COM对象的形式实现的。

10. 1 节点层次

> DOM树：DOM可以将HTML，XML文档描述成一个由多层次节点构成的结构。节点分几种不同的类型，
每种类型分别表示文档中不同的信息及标记。每个节点都有自己的特点数据和方法且与其它节点存在
关系，由此构成了层次，所有页面标记则表现为一个以特定节点为根节点的树形结构。每一段标记
都可以通过树中一个节点来表示（html元素由元素节点表示，attribute由属性节点表示，documentType由文档
类型节点表示，commit由注释节点表示）。

10. 1.1 Node类型（12种）
    1. **Node.ELEMENT_NODE(1)**;
    2. **Node.ATTRIBUTE_NODE(2)**;
    3. **Node.TEXT_NODE(3);**
    4. Node.ENTITY_REFERENCE_NODE(5);
    6. Node.ENTITY_NODE(6);
    7. Node.PROCESSING_INSTRUCTION_NODE(7);
    8. **Node.COMMIT_NODE(8);**
    9. **Node.DOCUMENT_NODE(9);**
    10. **Node.DOCUMENT_TYPE_NODE(10);**
    11. Node.DOCUMENT_FRAGMENT_NODE(11);
    12. Node.NOTATION_NODE(12);

###  Chapter11 DOM扩展

> DOM扩展主要是selectorsAPI和H5

- querySelector()
- querySelectorAll()
- matchesSelector()
- 元素遍历
    1. childElementCount:返回子元素（不含文本节点和注释）个数；
    2. firstElementChild:指向首子元素；
    3. lastElementChild:指向首尾元素；
    4. previousElementSibling:指向前一个同辈元素；
    5. nextElementSibling:指向后一个同辈元素；

11. 3HTML5

> H5规范围绕如何使用新增标记定义了大量的JS API。其中一些与DOM重叠，定义了浏览器应该支持的DOM扩展

11. 3.1与类相关的扩充

- getElementByClassName():返回带有指定类的所有元素的NodeList；
- classList():
    1. div.classList.remove('user'):删除类集中某类
    2. div.classList.add('user'):添加类集中某类
    3. div.classList.toggle('user'):切换类集中某类
    4. div.classList.contains('user'):查询类集中某类
11. 3.2焦点管理

```
var btn=document.getElementById('my-button');
btn.focus();
document.hasFocus();//true
//通过检查文档是否活得了焦点来判断用户是否在与页面交互
```
11. 3.3 HTMLDocument的变化

- readyState属性：loading（加载中）,complete(已完成)
- compatMode兼容模式

```
alert(document.compatMode=='CSS1Compat'?'Standards Mode':'Quicks Mode')
```
- head 属性

```var head=document.head||document.getElementsByTagName('head'[0])```

11. 3.4 字符集属性

```document.charset;//UTF-8```

11. 3.5自定义数据属性data-

> 目的是为元素提供与渲染无关的信息，或者提供语义信息，可以任意添加随便命名，但要以data-开头。

11 .3.6插入标记

> DOM操作的福音：虽然DOM操作可以实现细致入微的控制，但非常繁琐，使用插入标记
技术直接插入html字符串不仅简单而且高效。但多说浏览器中插入的script脚本并不会
执行（除非指定defer属性且位于（微软所谓的）作用域之后）

- innerHTML
```
document.querySelector('div').innerHTML='<script defer>alert("hi")</script>'
```
- outerHTML
- insertAdjacentHTML()

###  Chapter12 DOM2和DOM3

###  Chapter13 事件处理程序

13. 2.2 DOM0级事件处理程序

> 介绍：传统方式，将一个函数赋值给一个事件处理程序属性。特点：简单，跨
浏览器。首先要取得要操作对象的引用。
DOM0级事件处理程序被称为元素的方法，因此时事件处理程序是在元素作用域中执行的。

```
var btn=document.getElementById('xx');
btn.onclick=functin(){}；
btn.onclick=null;//删除事件处理程序
```
13. 2.3 DOM2级事件处理程序

> DOM2定义了两个方法用于指定和删除处理程序的操作：addEventListener()和removeEventListener()
所有DOM节点都包含这两个方法且接受三个参数：事件名，函数，布尔值（true:捕获，false冒泡）
多数情况下将事件处理程序添加到事件流的冒泡阶段，可以最大限度地兼容各种浏览器。
```
btn.addEventListener('click',function(){
},false);
btn.removeEventListener('click',function(){
},false);
```
13. 2.4IE事件处理程序
```
btn.attach('onclick',function(){});
btn.detach('onclick',function(){});
```
13. 2.5跨浏览器事件处理程序

13. 3事件对象

> 触发DOM上某事件时会产生一个包含与事件相关信息的事件对象。
只有在事件处理程序执行期间，event对象才会存在，否则立即销毁。

```
btn.onclick=function(event){alert(event.type)}
btn.addEventListener('click',function(event){alert(event.type)}
//stopPropagation用于阻止事件在DOM上传播（捕获或冒泡）
var btn = document.getElementById("myBtn");
        btn.onclick = function(event){
            alert("Clicked");
            event.stopPropagation();
        };
        
        document.body.onclick = function(event){
            alert("Body clicked");
        };
```
13. 4事件类型
13. 4.1 UI事件：不一定与用户操作有关的事件。包括DOMActive(非html事件),load,unload,abort
error,select,resize,scroll事件。
```
var isSurpported=document.implementation.hasFeature('HTMLEventts','2.0');
var isSurpported=document.implementation.hasFeature('UIEvent','3.0');
```

13. 4.2 焦点事件：blur,focusIn,focusOut,focus

13. 4.3 鼠标与滚轮事件：click,dbclick,mousedown,mouseenter,mouseleave,mousemove,
mouseout,mouseover,mouseup

13. 4.4键盘与文本事件：keydowm,keyup,keypress

13. 4.5 复合事件

13. 4.6 变动事件

13. 4.7 HTML5事件：contextmenu,beforeunload,DOMContentloaded,readystatechange
pageshow,pagehide,haschange

13. 4.8 设备事件

13. 4.9 触摸与手势事件touchEvents

- 触摸事件
    1. touchstart
    2. touchmove
    3. touchend
    4. touchcancel
- 手势事件
    1. gesturestart
    2. gesturechange
    3. gestureend

13. 5 内存和性能

>每个函数都是对象，会占用内存，内存中对象越多性能越差；必须事先指定所有事件
处理程序而导致的DOM访问次数，会延迟整个页面的交互就绪时间。解决之道是使用事件委托或
移除事件处理程序。

- 事件委托：只需在DOM树中尽量最高的层次上添加一个事件处理程序

```
(function(){
var list = document.getElementById("myLinks");
        EventUtil.addHandler(list, "click", function(event){
            event = EventUtil.getEvent(event);
            var target = EventUtil.getTarget(event);
            switch(target.id){
                case "doSomething":
                    document.title = "I changed the document's title";
                    break;
        
                case "goSomewhere":
                    location.href = "http://www.wrox.com";
                    break;
        
                case "sayHi":
                    alert("hi");
                    break;
            }
        });
    })();
    btn.onclick=function(){
        //过河
        btn.onclick=null;//拆桥
        //...
    }
```

13. 6模拟事件

- DOM中的事件模拟：document.createEvent()方法创建对象。

```
(function(){
        var btn = document.getElementById("myBtn");
        var btn2 = document.getElementById("myBtn2");
        
        EventUtil.addHandler(btn, "click", function(event){
            alert("Clicked!");
            alert(event.screenX);   //100
        });

        EventUtil.addHandler(btn2, "click", function(event){
            //create event object
            var event = document.createEvent("MouseEvents");
            //initialize the event object
            event.initMouseEvent("click", true, true, document.defaultView, 0, 100, 0, 0, 0, false, 
                                 false, false, false, 0, btn2);
            //fire the event
            btn.dispatchEvent(event);
        });
    })();
```

###  Chapter 14 表单脚本

- 自动切换焦点

```
<body>
<form method="post" action="http://www.nczonline.net">
    <p>Enter your telephone number:</p>
    <input type="text" name="tel1" id="txtTel1" size="3" maxlength="3" >
    <input type="text" name="tel2" id="txtTel2" size="3" maxlength="3" >
    <input type="text" name="tel3" id="txtTel3" size="4" maxlength="4" >

    <input type="submit" value="Submit">
</form>
<script type="text/javascript">
(function(){

    function tabForward(event){            
        event = EventUtil.getEvent(event);
        var target = EventUtil.getTarget(event);
        
        if (target.value.length == target.maxLength){
            var form = target.form;
            
            for (var i=0, len=form.elements.length; i < len; i++) {
                if (form.elements[i] == target) {
                    if (form.elements[i+1]){
                        form.elements[i+1].focus();
                    }
                    return;
                }
            }
        }
    }
                
    var textbox1 = document.getElementById("txtTel1"),
        textbox2 = document.getElementById("txtTel2"),
        textbox3 = document.getElementById("txtTel3");
    
    EventUtil.addHandler(textbox1, "keyup", tabForward);        
    EventUtil.addHandler(textbox2, "keyup", tabForward);        
    EventUtil.addHandler(textbox3, "keyup", tabForward);        
        
})();
</script>
```

- 14. 4 表单序列化
```
 function serialize(form){        
    var parts = [],
        field = null,
        i,
        len,
        j,
        optLen,
        option,
        optValue;
    
    for (i=0, len=form.elements.length; i < len; i++){
        field = form.elements[i];
    
        switch(field.type){
            case "select-one":
            case "select-multiple":
            
                if (field.name.length){
                    for (j=0, optLen = field.options.length; j < optLen; j++){
                        option = field.options[j];
                        if (option.selected){
                            optValue = "";
                            if (option.hasAttribute){
                                optValue = (option.hasAttribute("value") ? option.value : option.text);
                            } else {
                                optValue = (option.attributes["value"].specified ? option.value : option.text);
                            }
                            parts.push(encodeURIComponent(field.name) + "=" + encodeURIComponent(optValue));
                        }
                    }
                }
                break;
                
            case undefined:     //fieldset
            case "file":        //file input
            case "submit":      //submit button
            case "reset":       //reset button
            case "button":      //custom button
                break;
                
            case "radio":       //radio button
            case "checkbox":    //checkbox
                if (!field.checked){
                    break;
                }
                /* falls through */
                            
            default:
                //don't include form fields without names
                if (field.name.length){
                    parts.push(encodeURIComponent(field.name) + "=" + encodeURIComponent(field.value));
                }
        }
    }        
    return parts.join("&");
}

var btn = document.getElementById("serialize-btn");
EventUtil.addHandler(btn, "click", function(event){
    var form = document.forms[0];
    alert(serialize(form));
});
```

###  Chapter 15 Canvas

###  Chapter 16 HTML5脚本编程

- 16. 1 cross-document messaging XDM: 在来自不同域的页面间传递消息。
- 16. 2 原生拖放(事件)
    1. dragestart；
    2. drag;
    3. dragend;
- 16. 3 媒体元素
- 16. 4 历史状态管理

###  Chapter17 错误与调试

- 17.2 错误处理
错误类型：
1. Error：基类型，供开发人员抛出自定义错误
2. EvalError：错误会使用eval()函数抛出

` new eval();eval=foo;`

3. RangeError：错误超出相应范围时触发

`var items1=new Array(-20);var items2=new Array(Number.MAX_VALUE);`

4. ReferenceError：找不到对象时抛出

`var obj=x;`

5. SyntaxError：语法错误

`eval("a++b")`

6. TypeError：类型错误，变量中保存意外类型或者访问不存在的方法时。

`var o=new 12;alert('love' in true);Function.prototype.toString.call('name')`
7. URIError：使用encodeURI()或者decodeURI()格式错误时抛出。
```
//想知道错误类型可以如此利用try-catch语句
try{
    someFunction();
}catch(error){
    if(error instanceof TypeError){}
    else if(error instanceof SyntaxError){}
    else{...}
}
```

###  Chapter 18 JS与XML
###  Chapter 19 E4X
###  Chapter 20 JSON

> JSON:Javascript Object Notation,JS对象表示法，JS的利用了JS中的一些
模式来表示结构化数据的一个严格的子集。仅是一种数据格式不是一种语言。
- JSON语法：三种类型
    1. 简单值：与JS相同的语法，可在JSON中表示字符串，布尔值，数值和null（不支持undefined）
    2. 对象：一种无序的键值对的复杂数据结构类型，值可以是简单值也可以是对象。
    3. 数组：一种有序的值的列表的复杂的数据结构类型，通过索引访问元素。
JSON不支持变量，函数或者对象实例，仅仅是一种表示结构化数据的格式，虽与JS中表示数据的
某些语法相同，但并不局限于JS范畴。
- 20. 2解析与序列化
```
JSON.parse();
JSON.stringify();
toJSON();
var book = {
    title: "Professional JavaScript",
    authors: [
        "Nicholas C. Zakas"
    ],
    edition: 3,
    year: 2011,
    toJSON: function(){
        return this.title;
    }
   };
    var jsonText = JSON.stringify(book, ["title", "edition"]);
            var jsonText = JSON.stringify(book, function(key, value){
                switch(key){
                    case "authors":
                        return value.join(",")
                     
                    case "year":
                        return 5000;
                        
                    case "edition":
                        return undefined;
                        
                    default:
                        return value;
                }
            });
    var jsonText=JSON.stringify(book,null,4);
//toJSON()作为函数过滤器的补充，把一个对象传给JSON.stringify()，序列化顺序如下：
//1. (存在toJSON)?调用该方法:返回对象本身；
// 2.若提供了第二个参数则应用该函数过滤器（传入步骤1的值）
//3.对上一步返回的每一个值序列化
//4. 若传入了第三个参数则执行相应格式化
 var book = {
                       "title": "Professional JavaScript",
                        "authors": [
                            "Nicholas C. Zakas"
                        ],
                        edition: 3,
                        year: 2011,
                        releaseDate: new Date(2011, 11, 1)
                   };
           var jsonText = JSON.stringify(book);
        //{"title":"Professional JavaScript","authors":["Nicholas C. Zakas"],"edition":3,"year":2011,"releaseDate":"2011-11-30T16:00:00.000Z"}
   var bookCopy = JSON.parse(jsonText, function(key, value){
       if (key == "releaseDate"){
           //return undefined;
           return new Date(value);
       } else {
           return value;
       }
   });
   console.log("releaseDate" in bookCopy);
   console.log(bookCopy.releaseDate.getFullYear());//2011
```
###  Chapter 21 Ajax与Comet

> Ajax:Asynchronous JS + XML:能够向服务器请求额外的数据而无须刷新页面，带来更好的用户体验。
其核心是XMLHttpRequest(XHR)对象,其为向服务器发送请求和解析服务器响应提供了流畅的接口。能够
以异步方式从服务器取得更多的信息。虽然名字中包含XML，但Ajax通信与数据格式无关；无须刷新整个
页面即可从服务器取得数据，不局限于XML。

```
function createXHR(){
    if (typeof XMLHttpRequest != "undefined"){
        return new XMLHttpRequest();
    } else if (typeof ActiveXObject != "undefined"){
        if (typeof arguments.callee.activeXString != "string"){
            var versions = ["MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0",
                            "MSXML2.XMLHttp"],
                i, len;
    
            for (i=0,len=versions.length; i < len; i++){
                try {
                    new ActiveXObject(versions[i]);
                    arguments.callee.activeXString = versions[i];
                    break;
                } catch (ex){
                    //skip
                }
            }
        }
    
        return new ActiveXObject(arguments.callee.activeXString);
    } else {
        throw new Error("No XHR object available.");
    }
}
//如果XHR对象存在就可以写下边代码了
var xhr = createXHR();
//URL是相对执行代码的当前页面，open方法并非真正发送请求而是启动一个备发送的请求
xhr.open("get", "example.txt", false);
//send()接收一个参数作为请求主题发送数据，颥不需要发送则必须传入null
//responseText:作为响应主题被返回的文本
//responseXML:如响应内容类型是'text/xml'或'application/xml'该属性将保存包含着相应数据
的XML DOM文档
//status:响应的http状态
//statusText：http状态说明
xhr.send(null);
if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304){
    alert(xhr.statusText);
    alert(xhr.responseText);
} else {
    alert("Request was unsuccessful: " + xhr.status);
}
```

###  Chapter22 高级技巧

- 22. 1.4函数绑定bind()
- 22. 1.5函数柯里化（function curring）
- 22. 2防篡改对象
    22. 2.1 不可扩展对象
        1. Object.preventExtensions();
        2. Object.extensible()
    22. 2.2密封对象：Object.seal()
    22. 2.3冻结对象:Object.freeze() 

###  Chapter 23 离线存储与客户端存储

> 开发离线web应用步骤：
1. 确保应用知道设备能否上网；
2. 应用必须能够访问一定的资源；
3. 必须有一块本地空间用于保存数据，能否上网都不妨碍读写。
```
 EventUtil.addHandler(window, "online", function(){
            document.getElementById("status").innerHTML = "Online";
        });
        EventUtil.addHandler(window, "offline", function(){
            document.getElementById("status").innerHTML = "Offline";
        });
```
23. 3数据存储

###  Good Practise

- 可维护性
    1. 可理解性：其他人可以接手代码并理解它的意图和一般途径，无须作者的完整解释
    2. 直观性：一目了然，不管其操作过程多么复杂
    3. 可适应性：以一种数据上的变化不要求完全重写的方法撰写
    4. 可扩展性：架构上已考虑未来允许对核心功能的扩展
    5. 可调试性：当有地方出错时，提供足够信息来尽可能直接反馈问题所在
- 代码约定
    1. 可读性：
        1. 函数和方法：每个要有注释，描述其目的和用于完成任务所可能使用的算法
        2. 大段代码：描述下任务的注释
        3. 复杂算法： 
        4. hack：处理兼容性等问题
    2. 变量和命名
        1. 变量名为名词（car，person）；
        2. 函数名以动词开始（getName）,返回布尔值的以is开头
        3. 使用合乎逻辑的名字
    3. 变量类型透明
        1. 初始化：var found=false,count=-1,name=''
        2. 匈牙利标记法指定变量类型： var bFound,iCount,sName,pPerson
        3. 使用注释
- 松散耦合
    1. 解耦html/JS
    2. 解耦css/JS
    3. 解耦应用逻辑/事件处理程序:分离原则
        1. 勿将event对象传给其它方法，只传给来自event对象中所需的数据；
        2. 任何可以在应用层面的动作都应该可以在不执行任何事件处理程序的情况下进行；
        3. 任何事件处理程序都应该处理事件，然后将处理转交给应用逻辑。
    
```
function validateValue(value){
    value=5*parseInt(value);
    if(value>10){
        //
    }
}
function handleKeyPress(){
    event=EventUtil.getEvent(event);
    if(event.keyCode==13){
        //
    }
}
```
- 编程事件
    1. 尊重对象所有权
        1. 不要为实例或原型添加属性，方法
        2. 不要重定义已存在的方法
    2.  避免全局变量
        1. 单一的全局量的延伸便是命名空间的概念
           YAHOO.util.DOM,YAHOO.util.Event,YAHOO.lang
    3. 避免与null比较
    4. 使用常量：将数据和使用逻辑分离
        1. 重复值：任何在多处用到的值都应是常量；
        2. 用户界面字符串
        3. URLs
        4. 任何可能会改变的值
- 性能
    1. 注意作用域
        1. 避免全局查找
        2. 避免with
    2. 选择正确的方法
        1. 避免没必要的查找
        2. 优化循环
        3. 展开循环
        4. 避免双重解释
        5. 其它注意事项
            1. 原生方法较快：
            2. switch语句较快
            3. 位运算符较快
- 最小化语句树
    1. 多个变量声明
    2. 插入迭代值
    3. 使用数组和对象字面量
- 优化DOM交互
    1. 最小化现场更新
    2. 使用innerHTML
    3. 使用事件代理
    4. 注意HTMLCollection
- 部署
    1. 构建过程
        1. 知识产权问题
        2. 文件大小
        3. 代码组织
    2. 验证
    3. 压缩
        1. 文件压缩
            1. 删除所有空白
            2. 删除所有注释
            3. 缩短变量名
        2.HTTP 压缩
###  Chapter25 新兴API
###  ES Harmony

- 一般性变化
    1. 常量：const
    2. 块级作用域及其它作用域
        let关键字：使用let定义的变量在定义它的代码之外没有定义。
- 函数

1. 剩余参数与分布函数
```
function sum(n1,n2,...ns){
    var result=n1+n2;
    for(let i=0,len=ns.length;i<len;i++){
        result+=n[i]
    }
    return result;
}
var result=sum(...[1,2,3,4,4]);
var result=sum.apply(this,[1,3,4,4,5])
```

2. 默认参数值

```
function sum(n1,n2=0){
    return n1+n2;
}
```
3. 生成器
```
function myNumbers(){
    for(var i=0;i<10;i++){
        yield i*2;
    }
}
var generator=myNumbers()
```
- 数组及其它
1. 迭代器
```
var colors=['yellow','blue','red'];
var iterator=new Iterator(colors)
```
2. 数组领悟
```
var numbers=[1,3,4,4,5];
var duplicate=[i for each (i in numbers) if(i%2==0)]
```
3. 解构赋值
```
var value1=5,value2=10;
[value1,value2]=[value2,value1]
```
- 新对象类型
    1. 代理对象
    ```
    var proxy=Proxy.create(handler);
    var proxy=Proxy.create(handler,myObject);
    ```
    捕捉器7种：
        1. getOwnPropertyDescriptor
        2. getPropertyDescriptor
        3. getOwnPropertyNames
        4. getPropertyName
        5. defineProperty
        6. delete
        7. fix
    派生捕捉器6种
        1. has
        2. hasOwn
        3. get
        4. set
        5. enumerate
        6. keys
    2. 代理函数
    ```
    var proxy=Proxy.createFunction(handler,function(){}.function(){})
    ```
    3. 映射map与集合set
    ```
    var map=new Map();
    map.set('name','Yl');
    map.has('name');//true
    var set=new Set();
    set.add('name');
    set.has('name');//true
    set.delete('name')
    ```
    4. weakMap
    ```
    var key={},map=new WeakMap();
    map.set(key,'hello');
    //解除对键的引用而删除该值
    key=null
    ```
    5. StructType
    6. ArrayType
- 类

```
class Person {
    constructor(name,age){
        public name=name;
        //public age=age;
        private age=age;
        get title(){
            return innerTitle=''
        }
        set title(value){
            innerTitle=value;
        }
    }
    sayName(){
        alert(this.name)
    }
    getOlder(years){
        alert(this.age+=years)
    }
}
```
1. 私有成员
2. getter,setter
3. 继承
```
class Employee extends Person
class Employee prototype basePerson
```
- 模块
```
module MyModule={
    export let myobject={};
    export function hello(){};
    function goodbye(){}
}
import myobject from MyModule
import * from MyModule
//直接使用
console.log(MyModule.hello)
```
外部模块
```
module MyModule from 'a.js'
import myobject from MyModule
```
###  严格模式

```use strit```
- 变量：禁止意外创建全局变量
- 对象：
    1. 为只读属性赋值==>TypeError
    2. 对不可配置的属性使用delete==>TypeError
    3. 为不可扩展的对象添加属性==>TypeError
    4. 使用对象字面量时属性名必须唯一
- 函数
    1. 命名函数的参数必须唯一
    2. 淘汰了arguments.callee,arguments.caller
- eval():在包含上下文中不再创建变量或函数
- eval与arguments：不可作为变量引用
- 抑制this：函数的this始终是指定值，无论指定值是什么
- 其它：禁用with语句
    
    
    
    
    
    
    
    
    
        





















































































































































