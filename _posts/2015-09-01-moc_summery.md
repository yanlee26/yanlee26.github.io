---
date: 2015-09-01
layout: post
title: JS深入浅出学习分析总结
thread: 9
categories: JS
tags: [BIT]
excerpt: JS深入浅出学习分析总结.
---

# JS深入浅出学习分析总结

## 六种数据类型

    弱类型：

```
var num=32;num='str';32+32;//64
32+'32' //64;
'32'-32 //0
```
- 数据类型
    - 原始类型(五种)：number,string,boolean,null,undefined
    - 对象类型object:Function,Array,Date...
- 隐式转换
    - 类型相同，同===
    - 类型不同，尝试类型转换并比较

```
null==undefined;
number==string转number
boolean==?转number 1==true //true
object==number|string尝试对象转为基本类型 new String('hi')=='hi' //true,
其它：false
```

- 包装对象：string,number,boolean

```
var str='12';//string
str.length ;//2
str.a=10;//10
str.a;//undefined,临时对象创建后立即销毁
var newStr=new String('12');//Object
```
- 类型转换
    - typeof：基本类型及function类型（null失效）
    - instanceof:对象类型，适合自定义对象及原生对象（不同iframe和window间失效）
    - Object.prototype：[[Class]]适合内置对象和基元类型（null和undefined失效；IE678返回【object Object】）
    - constructor
    - duck type

## 表达式和运算符

###表达式

- 原始表达式：
    - 常量，直接量：3.14，‘a’
    - 关键字:null,this,true
    - 变量:i,j,k
- 复合表达式：原始表达式&原始表达式
- 初始化表达式：[1,2]===new Array(1,2);
- 函数表达式：var foo=function(){};(function(){})()
- 属性访问表达式：var o={x:1};o.x;o[x']
- 调用表达式：foo()
- 对象创建表达式：new Func(1);new Object //无参数可以省略’（）‘;

### 运算符（表达式之间）

- 操作数：
    - 一元：+num;
    - 二元：a+b;
    - 三元：c?a:b
- 功能：
    - 赋值：a+=1
    -比较：a==b
    - 算数：a-b;
    - 位：a|b;
    - 逻辑：exp1&&exp2
    -字符串：‘a’+'b'
    - 特殊：
            -delete obj.x //Object.defineProperty(obj,'x',                      {configurable:false,value:1});
        - ,:var a,b;
        - in: var i in item
         - instanceof ,typeof
        - new :obj.hasOwnProperty('x');obj.__proto__.hasOwnProperty('x') //true
        - this
        - void:void 0

### 运算符优先级

## 语句

- 块语句（js无块及级作用域）和var语句
- try catch语句
- function,switch,循环（for in(1.顺序不定，2.enumberable为false时不可用；3.受原型链影响),do-while），with
- 严格模式strict
> 一种特殊的运行模式，修复了部分语言上的不足，提供了更强的错误检查并增强安全性

- 不允许使用with    
- 所有变量必须先声明
- eval中代码不能创建eval所在作用域下的变量函数，而是为eval单独创建一个作用域并在eval返回时丢弃
- 函数中的特殊对象arguments是静态副本（不像非严格模式时修改arguments或者参数变量会相互影响）
- 删除configurable=false的属性会报错而非忽略
- 禁止八进制字面量（010）
- eval,arguments变量为关键字，不可作为变量名函数名
- 一般函数调用时（非对象方法调用，不使用apply/call/bind等修改this）this指向null而非全局对象，若使用apply/call当传入null或undefined时this指向null或者undefined而非全局。
- 试图修改writable=false，在不可扩展对象上添加属性时包TypeError而非忽略
- arguments.caller,arguments.callee禁用

## 对象

- 对象结构（原型链）

![属性的属性][1]

- 创建对象方法：
    - 对象字面量
    - new/原型链

![原型链的继承][2]
    - Object.create()
![Object.create][3]

- 属性的操作
    - 读写

>注意for-in操作符可以把对象原型上属性也遍历出来
    - 读写异常

```var obj={x:1};var yz;if(obj.y){yz=obj.y.z};
var yz=obj&&obj.y&&obj.y.z;
```

- 属性删除

```
var descriptor=Object.getOwnPropertyDescriptor(Object,'prototype');
descriptor.configurable;//false,是否可配置
```
![图片描述][4]

> node环境中最后一个也是false

- 属性检测

```
hasOwnProperty();
propertyIsEnumerable()
```

![图片描述][5]

- 属性枚举

```
var o={x:1,y:2,z:3};
'toString' in o;//true
o.propertyIsEnumerable('toString');//false
var key;
for(key in o){console.log(key)};x,y,z
var obj=Object.create(o);
obj.a=4;
var key;
for(key in obj){console.log(key)};a,x,y,z
for(key in obj){if(obj.hasOwnProperty(key){console.log(key)})};a
```

- get/set方法

```
var man={
    name:'yl',
    get age(){return new Date.getFullYear()-1990},
    set age(val){console.log('np'+val)}
}
man.age;//27
man.age=12;//np12
```

- 属性标签

```
Object.getOwnPropertyDescriptor({pro:true},'pro');
//Object{value:true,writable:true,enumerable:true,configurable:true}
Object.getOwnPropertyDescriptor({pro:true},'a');//undefined
var person={};
Object.defineProperty(person,'name',{
    configurable:false,
    writable:false,
    enumerable:true,
    value:'yl'
});
person.name;//yl
person.name=1;
person.name;//yl
delete person.name;//false
Object.defineProperty(person,'type',{
    configurable:true,
    writable:true,
    enumerable:false,
    value:'Object'
});
Object.keys(person);//['name']
//多个属性
Object.defineProperties(person,{
    title1:{value:'fe1',enumerable:true},
    title2:{value:'fe2',enumerable:true},
    title3:{value:'fe3',enumerable:true,writable:true}
});
Object.getOwnPropertyDescriptor(person,'title3');
//Object{value:'fe3',writable:true,enumerable:true,configurable:false}
Object.getOwnPropertyDescriptor(person,'title1');
//Object{value:'fe3',writable:false,enumerable:true,configurable:false}
```

- 对象标签，序列

```
var toString=Object.prototype.toString;
function getType(o){return toString.call(o).slice(8,-1)}
getType(true)
//"Boolean"
toString.call(null)
//"[object Null]"
```

- extensible扩展性

```
var obj={x:1,y:2};
Object.isExtensible(obj);//true
Object.preventExtensions(obj);
Object.isExtensible(obj);//false
obj.z=1;
obj.z;//undefined,add new property failed
Object.getOwnPropertyDescriptor(obj,'x');
//Object{value:1,writable:true,enumerable:true,configurable:true}
Object.seal(obj);
Object.getOwnPropertyDescriptor(obj,'x');
//Object{value:1,writable:true,enumerable:true,configurable:false}
Object.isSealed(obj);//true
Object.freeze(obj);
Object.getOwnPropertyDescriptor(obj,'x');
//Object{value:1,writable:false,enumerable:true,configurable:false}
Object.isFrozen(obj);//true
//caution :not affects prototype chain!!!
```

- 序列化其他，其它对象方法

```
//序列化JSON.stringify&JSON.parse
var obj={x:1,y:true,z:[1,2,3],nullVal:null};
JSON.stringify(obj);//"{"x":1,"y":true,"z":[1,2,3],"nullVal":null}"
obj={x:undefined,y:NaN,z:Infinity,nullVal:new Date()};
JSON.stringify(obj);//"{"y":null,"z":null,"nullVal":"2017-02-07T23:10:33.107Z"}",注意undefined和NaN
o=JSON.parse('{"x":"1"}');
o.x;//1
//其它对象方法toString()&valueOf()
```

##数组

- 创建与操作

> 数组是值的有序集合，每个值叫做元素，每个元素在数组中都有数字位置
编号，即索引。js中数组是弱类型的，其中可以含有不同类型元素。

```var arr=[1,true,null,undefined,{},[]]        ```

- 创建数组字面量：[]
- new Array()
- 元素读写

```arr.length;arr[i];delete arr[j];//delete 操作不改变原数组    ```

- 元素增删

```push,pop,shift,unshift,delete```

- 数组迭代

```for-in for```

### 二维数组+稀疏数组

- 稀疏数组：并不含有从0开始的连续索引，一般length属性值比实际元素个数多

### 数组方法

```
//{}=>Object.prototype;[]=>Array.prototype
Array.prototype.join;
Array.prototype.reverse;
Array.prototype.sort;
Array.prototype.concat;
Array.prototype.slice;
Array.prototype.splice;//左闭右开
Array.prototype.forEach;//ES5
Array.prototype.map;//ES5
Array.prototype.map;//ES5
Array.prototype.filter;//ES5
Array.prototype.every;//ES5
Array.prototype.some;//ES5
Array.prototype.reduce/reduceRight;//ES5
Array.prototype.indexOf/lastIndexOf;//ES5
Array.isArray;//ES5；[] instanceof Array;[].constructor===Array;({}).toString.apply([])==='[object Array]'
var arr=[1,2,3];
var sum=arr.reduce(function(x,y){return x+y},0);//6
var max=arr.reduce(function(x,y){return x>y?x:y});//3
```

###小结

- 数组vs一般对象
同：都可以继承，数组是对象对象不一定是数组，数组拥有对象的一切属性和方法
异：数组自动更新length，按索引访问数组相对对象属性访问迅速。数组对象集成Array.prototype上的大量数组操作方法
- 字符串vs数组
字符串可以堪称类数组

```
var str='hello';
str.charAt(0);//'h'
str[1];//e
Array.prptotype.join.call(str,'_');//'h_e_l_l_o'
```
## 函数和作用域

> 一块js代码，定义一次但可以多次调用。js中函数也是对象

- 函数=函数名+参数列表+函数体

> 返回值：

1. 如果函数没有return语句或者return后边是基本类型=>返回this
2. 如果返回一个对象=>返回以此对象作为构造器的一个实例

### 重点

- this
- arguments
- 作用域
- 调用：
    - 直接调用：foo()
    - 对象方法：o.method()
    - 构造器：new Foo()
    - call/apply/bind:foo.call
- 创建
    - 函数声明：function a(){};会被前置
    - 函数表达式:
```
//function variable
var a=function(){}
//IEF(Immediately Executed Function)
(function(){})()
//first-class function
return function(){}
//NFE(Named Function Expression)
var a=function a(){}
//FC(Function Constructor)
```
||函数声明|函数表达式|函数构造器|
|---|---|---|
|前置|Y|N|N|
|允许匿名|N|Y|Y|
|立即调用|N|Y|Y|
|在定义该函数的作用域通过函数名访问|Y|N|N|
|没有函数名|N|N|Y|

### this

- 全局的this(浏览器)
- 一般函数的this
```
function a(){return this}
 a()===window;//true ,global,object
//严格模式下：a()===undefined
```
- 作为对象方法的this
```
var o={
    prop:23,
f:function(){return this.prop}
}
o.f();//23
function g(){return this.prop}
o.f=g;
o.f();//23
```
- 对象原型链上的this
  ```
var o={
f:function(){return this.a+this.b}
}
var p=Object.create(o);
p.a=1;p.b=2;
p.f();//3
```
- get/set方法
```
function modulus(){
return Math.sqrt(this.re*this.re+this.im*this.im)
}
var o={
re:1,im:-1,
get phase(){
return Math.atan2(this.im,this.re)
}
}
Object.defineProperty(o,'modulus',{
    get:modulus,emumerable:true,configurable:true
})
console.log(o.phase,o.modulus);//-.78,1.142
```
- 构造器中的this
```
function MyClass(){this.a=22}
var o=new MyClass();
o.a;//22
function C2(){this.a=23;return {a:38}}
o=new C2()
o.a;//23
```
- call/apply方法与this
```
function add(a,b){return this.a+this.b+c+d}
var o={a:1,b:3}
add.call(o,5,7);//1+3+5+7=16
add.apply(o,[10,20]);//1+3+10+20=34
function bar(){
console.log(Object.prototype.toString.call(this))
}
bar.call(7);//"[object Number]"
```
- bind方法
```
function f(){return this.a}
var g=f.bind({a:"test"})
g();//test
```
### 属性和方法

- arguments属性
```
function foo(x,y,z){
'use strict';
arguments.length;//2
arguments[0];//1
arguments[0]=20;
x;//changed to 20,严格模式下仍是1
arguments[2]=100;
z;//still undefined !!! 未传参数失去绑定关系
arguments.callee===foo;//true,严格模式下不可用
}
foo(1,2);//foo.name=>函数名
foo.length;//3 ，foo.length=>形参个数,arguments.length=>实参个数
```
- call/apply方法
```
function foo(x,y){console.log(x,y,this)}
foo.call(100,1,2);//1,2,Number(100)
foo.apply(true,[3,4]);//3,4,Boolean(true)
foo.apply(null);//undefined,undefined,window(严格模式下null)
foo.apply(undefined);//undefined,undefined,window（严格模式下undefined）
```
- bind方法（IE9以上）
- bind与currying（把整个函数拆成多个单元）
```
function add(a,b,c){return a+b+c}
var func=add.bind(undefined,100);
func(1,2);//103
var func2=func.bind(undefined,200)
func2(10);//310
```
- bind与new
```
function foo(){this.b=100;return this.a}
var func=foo.bind({a:1})
func();//1
new func();//{b:100}，bind被忽略？？？
```

## 闭包作用域

- 闭包

```
function outer(){
    var localVal=30;
    return function(){
        return localVal;
    }
}
var func=outer();
func();//30
```
- 封装
```
(function (){
    var _userId=123;
    var _typeId="Item";
    var export={};
    function converter(userId){
        return +userId;
}
export.getUserId=function(){
    return converter(_userId);
}
export.getTypeId=function(){
return _typedId;
}
window.export=export;
})()
```
> 好处：灵活方便，封装；坏处：空间浪费，内存泄漏，性能消耗
### 作用域
- 类型
    - 全局
    - 函数
    - eval
- 作用于链

### ES3的执行上下文（EC）

```
console.log('EC0'0);
function funcEC1(){
console.log('EC1');
    var funcEC2=function (){
        console.log('EC2');
        var funcEC3=function(){
            console.log('EC3');
        };
        funcEC3();
};
    funcEC2();
};
funcEC1();
//EC0 EC1 EC2 EC3
```

- 概念：变量对象（Variable Object）,一个抽象概念中的对象，用于存储执行上下文中的变量，函数声明，函数参数。

- EC与VO
```
activeExecutionContext={
    VO:{
    data_var,data_func_declaration,data_func_arguments
}
}
GlobalContextVO   VO===this===global
//demo
var a=10;
function test(x){
    var b=20;
}
test(30);
//=>
VO(gC)={
    a:10,
    test:<ref to function>
}
VO(test functionContext)={
    x:30,b:20
}
```

- 全局执行上下文GEC

```
VO(globalContext)===[[global]];
[[global]]={
    Math:<...>,
    String:<>,
    isNaN:function (){Native Code},
    ...
    window:global//applied by browser(host)
};
GlobalContextVO  (VO===this===global)
String(10);//[[global]].String(10);
window.a=10;//[[global]].window.a=10
this.b=20;//[[global]].b=20
```

- 函数中的激活对象

```
VO(functionContext)===AO;
AO={
    arguments:<Arg0>//函数调用时生成
};
arguments={
    callee,length,properties-indexes
}
```

- 变量初始化阶段

> VO填充顺序：

1. 函数参数（arguments=arguments||undefined）;
2. 函数声明（命名冲突则覆盖,函数表达式不影响VO）；
3. 变量声明（初始化为undefined，命名冲突则忽略）

```
function test(a,b){
var c=10;
function d(){}
var e=function _e(){};
(function x(){})();
b=20;
}
test(10);
//AO
AO(test)={
a:10,b:undefined,c:undefined,d:<ref to func 'd'>,e:undefined
}
```






###demo

```
function test(a,b){
var c=10;
function d(){}
var e=function _e(){};
(function x(){})
b=30;
}

```
- 变量初始化阶段

```
test(10);
AO(test)={
a:10,b:undefined,
c:undefined,
d:<ref to func 'd'>
e:undefined
}
```
- 代码执行阶段
```
AO(test)={
a:10,b:20,
c:10,
d:<ref to FunctionDeclaration 'd'>
e:function _e{}
}
```
    测试
```
alert(x);
var x=10;
alert(x);x=20;
function x(){}
alert(x);
if(true){var a=1}else{var b=true}
alert(a);alert(b)
//function x(){} 10 20 1 undefined
```
## OOP（封装+继承+多态+抽象）

> OOP:一种程序设计范型，同时也是一种程序开发方法。对象指的是类的实例。它将对象作为程序的基本单元，将程序和数据封装其中，以提高软件的重用性，灵活性和扩展性。

```
function Person(name,age){this.name=name;this.age=age;}

Person.prototype.hi=function(){console.log('Hi Iam'+this.name+'I am'+this.age+'years old now')}

Person.prototype.LEGS_NUM=2;
Person.prototype.ARMS_NUM=2;
Person.prototype.walk=function(){console.log(this.nama+'is walking')}
function Student(name,age,className{Person.call(this,name,age);this.className=className}

Student.prototype=Object.create(Person.prototype);//
Student.prototype.constructor=Student;
Student.prototype.hi=function (){
    console.log("Hi"+this.name+',I am'+this.age+'years old ,and from'+this.className+'.')
}
Student.prototype.learn=function(subject){console.log('my major is'+subject)}
//test
var tom=new Student('Tom',22,'Class 3');
tom.hi();
tom.LEGS_NUM;
tom.walk();
tom.learn();
```

![再谈原型链][6]

### prototype属性

```
Student.prototype.x=101;
tom.x;//101

Student.prototype={y:3};
tom.y;//undefined
tom.x;//101

var jack=new Student('Jack',22,'Class 1');
jack.x;//undefined
jack.y;//2
```
- Thus:

1. 动态修改prototype对象的属性, 会影响创建及已创建的实例
2. 修改整个prototype对象，只影响后续创建的实例

- 内置构造器的prototype

```
Object.prototype.x=1;
var obj={};
obj.x;//1
for(var key in obj){console.log(key)}//x
//ES5
Object.defineProperty(Object.prototype,{
writable:true,value:1
})
var obj={};
obj.x;//1
for(var key in obj){console.log(key)}//nothing
```

### 实现继承的方式

```
function Person(){}
function Student(){}
Student.prototype=Person.prototype;//1 bad
Student.prototype=new Person;//2===4
Student.prototype=Object.create(Person.prototype);//3,ES5
//check
if(!Object.create){
    Object.create=function(proto){
    function F(){}
    F.prototype=proto;
    return new F;
}    
}
Student.prototype.constructor=Person;//4
```

### 模拟重载，链式调用，模块化

- 模拟重载

```
function Person(){
    var args=arguments;
    if(typeof args[0]==='object'&&args[0]){
        if(args[0].name){
            this.name=args[0].name        
        }
         if(args[0].age){
            this.age=args[0].age        
        }else{
            if(args[0]){
                this.name=args[0]
            }
            if(args[1]){
                this.age=args[1]
            }
        }
    }
}
Person.prototype.toString=function(){
    return 'name='+this.name+',age'+this.age;
}
var tom=new Person('tom',28);
var jack=new Person({name:'Jack',age:23})
```

- 调用子类的方法

```
function Person(name){this.name=name;}
function Student(name,className){this.className=className;Person.call(this,name);}
var tom=new Student('tom','NT');

Person.prototype.init=function(){}；
Student.prototypt.init=function(){Person.prototype.init.apply(this,arguments)};
```

- 链式调用

```
function ClassManager(){
    ClassManager.prototype.addClass=function(str){
        console.log('Class'+str+'added');
        return this;//实现链式调用
    }
}
var manager=new ClassManager();
manager.addClass('classA').addClass('classB').addClass('classC')
```

- 抽象类

```
function DetectorBase(){
    throw new Error('Abstract class can not be invoked directly!')
}
DetectorBase.detect=function(){console.log('Detector starting...')};
DetectorBase.stop=function(){console.log('Detector stopped.')};
DetectorBase.init=function(){throw new Error('Error')};

function LinkDetector(){}
LinkDetector.prototype=Object.create(Detector.prototype);
LinkDetector.prototype.constructor=LinkDetector;
//...add methods to LinkDetector...
```
- defineProperty(ES5)

```
function Person(name){
    Obect.defineProperty(this,'name',{value:name,enumerable:true});
}
Object.defineProperty(Person,"ARMS_NUM",{value:2,enumerable:true});
Object.seal(Person.prototype);
Object.seal(Person);
function Student(name,className){
    this.className=className;
Person.call(this,name)
}
Student.prototype=Object.create(Person.prototype);
Student.prototype.constructor=Student;
```
- 模块化

```
var moduleA;
moduleA=function(){
    var prop=1;
    function func(){
        return{
        func:func,
        prop:prop
        }
    }
}

var moduleA;
moduleA=new function(){
    var prop=1;
    function func(){}
    this.func=func;
    this.prop=prop;
}

```

- 实践（探测器）

```
!function(global){
    function DetectorBase(configs){
        if(!this instanceof DetectorBase){
            throw new Error('Do not invoke without new')
        }
        this.configs=configs;
        this.analyze();
    }
    DetectorBase.prototype.detect=function(){
        throw new Error('Not implemented')
    }
    DetectorBase.prototype.analyze=function(){
    console.log('analyzing...');
    this.data='###data###';
    }
}
function LinkDetector(links){
    if(!this instanceof LinkDetector){
        throw new Error('Do not invoke without new');
    }
    this.links=links;
    DetectorBase.aply(this,arguments);
}
function ContainerDetector(containers){
    if(!this instanceof ContainerDetector){
        throw new Error('Do not invoke without new')
    }
    this.containers=containers;
    DetectorBase.apply(this,arguments);
}
//inherit first
inherit(LinkDetector,DetectorBase);
inherit(ContainerDetector,DetectorBase);
//
```

[1]: http://img.mukewang.com/5899cf7300011e1c08280441.jpg
[2]: http://img.mukewang.com/5899cfd80001d6cb05290393.jpg
[3]: http://img.mukewang.com/5899d0410001527005980329.jpg
[4]: http://img.mukewang.com/5899d324000108a606410289.jpg
[5]: http://img.mukewang.com/5899d52c00013d8206160360.jpg
[6]: http://img.mukewang.com/589babcb0001243408190462.jpg