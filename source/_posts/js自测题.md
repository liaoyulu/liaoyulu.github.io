### 1
```js
var a = function(){}//定义一个构造函数
a.b = 1
a.prototype.b = 2//原型对象的b属性为2
a.prototype.c = 3
a.prototype.d = 4
console.log(a.b)// 1 对象a的b属性为1 
console.log(new a().b)//2 构造函数返回对象实例继承原型b属性为2

var foo = new a()//创建一个对象实例
foo.c = 5
console.log(foo.c)//5 
console.log(foo.d)//4
```
### 2  
```js
var Foo = function(){
  this.a = 1//this指向新创建的对象
  return {
    a:2
  }
}
var bar = new Foo()
console.log(bar.a)//2 如果返回值是一个对象，那么this指向的就是那个返回的对象，如果返回值不是一个对象那么this还是指向函数的实例。
```
### 3
```js
var map = Object.create(null);//使用null原型创建原型对象 原型污染无法影响这样的对象。 
console.log("toString" in map);//false null原型没有toString方法 空对象没有原型连
var map = Object.create({a:1});//{a:1}新创建对象的原型对象。
console.log("toString" in map); //true  toString是Object的原型办法
console.log("a" in map); //true
```
### 4
``` js
function foo(obj){
  return Object.prototype.toString.call(obj).slice(8,-1)
}
```
请说明函数foo的作用  
[参考](https://www.cnblogs.com/SallyShan/p/11530619.html)  
foo作用：准确判断数据类型。  
toString :把一个逻辑值转换成字符串并返回结果
call(): 改变this指向    
slice():返回一个新的数组，包含从 start 到 end （不包括该元素）的 arrayObject 中的元素。
### 5
``` js
var a = {}
a.bar = 2

Object.defineProperty(a, "foo",
                      { value: "hi"});

console.log(delete a.foo)//False configurable默认False，不能直接删除当前的属性
console.log(delete a.bar)//true  

a.foo = "world"
console.log(a.foo)//hi 数据描述符vale和writable writable默认false，不能改变value

for (var key in a){
  console.log(key);//没有输出 enumerable描述属性是否出现在for in或者object.keys()的遍历中 foo默认false bar被delete
}

console.log("foo" in a);//true
console.log("bar" in a);//false
```
> object.defineProperty(obj.prop.desc)
作用：直接在一个对象上定义一个新属性，或者修改一个已存在的属性。

1. obj 需要定义属性的当前对象
2. prop 当前需要定义的属性名
3. desc 属性描述符
### 6
```js
var a = 1;

function test() {

    a = 0;

    console.log(a);

    console.log(this.a);

    var a;//变量声明提升

    console.log(a);

}
test(); //0 |1:this最终指向的是调用它的对象,这里的函数是被windows调用出来的指向windows。| 0
new test();//0 |undefined:构造函数中的this是指向实例的 |0
```
### 7
```js
function foo(){
    f = function(){
        console.log('1');
    }
    return this;
}
foo.f = function(){
    console.log('2');
}
foo.prototype.f = function(){
    console.log('3');
}
var f = function(){
    console.log('4');
}// 变量声明会“被提前”了，但是他的赋值，并不会被提前。
function f(){
    console.log('5');//函数声明和赋值都会被提前到作用域的顶部
}//函数声明提升
//写出下面的结果
foo.f();//2执行了foo对象的f()方法
f();// 4 声明前置

foo().f();//1 foo()是函数的调用，foo()对f进行了重新赋值,return this是window，在window中找f(),此时f()别重新赋值了。
new foo.f();//2  一个foo.f的实例
new foo().f();//3 (new foo()).f() 一个foo的实例，接着执行Foo的实例上的 f函数，那么 先从实例上查找f函数，发现找不到（Foo上有一个静态的bar函数，但是不是实例的方法，只能通过构造函数调用），然后接着从原型链上查找到f方法，调用。
new new foo().f();//3 new new foo().f() 创建foo实例，创建foo.f(=foo.prototype.f)实例
```
使用关键字new创建新实例对象经过了以下几步：  

1.    创建一个新对象
2.    将新对象的_proto_指向构造函数的prototype对象
3.    将构造函数的作用域赋值给新对象 （也就是this指向新对象）
4.    执行构造函数中的代码（为这个新对象添加属性）
5.    返回新的对象
  
<!--  #### Foo().getName()

输出结果为：1
原理：Foo()是函数调用，Foo执行体中对getName进行了重新赋值（全局的getName，根据作用域链向上查找），返回的this为window(Foo执行时没有调用，默认为全局)，然后.getName()调用了全局的getName（也就是被Foo执行体重写的getName），所以结果为:1
#### getName()

输出结果为：1 原理：因为Foo执行体中对全局getName重写了，这时调用的又是全局的getName。所以结果为:1 -->