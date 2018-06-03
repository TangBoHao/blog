---
title: JavaScript原型链示例
date: 2018-05-03 14:30:41
---
##js中原型链的机制
<!-- more -->

```javascript

	<script type="text/javascript">
	//对象的类型
		// function f1(){};
		// var f2=function(){};
		// var f3=new Function('str','console.log(str)');

		// var o3=new f1();
		// var o1={};
		// var o2=new Object();

		// console.log(typeof Object); //function
  //   	console.log(typeof Function); //function
  // 		console.log(typeof o1); //object
  // 		console.log(typeof o2); //object
 	//     console.log(typeof o3); //object
  // 		console.log(typeof f1); //function
 	//     console.log(typeof f2); //function
 	//     console.log(typeof f3); //function 
 	//     console.log('以下是对象的prototype');
 	//     console.log(typeof o1.prototype); //object
  // 		console.log(typeof o2.proto); //object
 	//     console.log(typeof o3.proto); //object
  // 		console.log(typeof f1.prototype); //function
 	//     console.log(typeof f2.prototype); //function
 	//     console.log(typeof f3.prototype); //function 

//原型链生成对象详解
 // var person=function(name){
 // 	this.name=name;
 // }
 // person.prototype.getName=function(){
 // 	return this.name;
 // }
 //  var tbh=new person('tangbohao');
 //  console.log(tbh.__proto__);
 // console.log(tbh.__proto__ === person.prototype);
 // console.log(person.prototype.__proto__ === Object.prototype);
 // var tbh=new person('tangbohao');
 // console.log(Object.prototype.__proto__)
 // document.write(tbh.getName());

//原型链实现继承实例
// function Person(name){
// 	this.name=name;
// 	this.ClassName="person";

// }
// Person.prototype.getClassName=function(){
// 	console.log(this.className);
// }
// function Man(){

// }
// //Man.prototype=new Person();  //new一个父类的示例，赋值给子类的原型,这种方法子类无法通过父类创建私有属性
// Man.prototype=new Person("qwer");
// var man=new Man;

//利用原型链，调用构造函数的方式
// function Person(name){
// 	this.name=name;
// 	this.ClassName="Person";
// }
// Person.prototype.getName=function(){
// 	console.log(this.name);
// }
// function Man(name){
// 	Person.apply(this,arguments);
// }
// var man1=new Man('zhangsan');
// var man2=new Man('lisi');

//es6的继承方式
class Person{
	//static count=0;
	constructor(name){
		this.name=name;
		//this.count++;
	}
	getName(){
	console.log(this.name);
	}
	static test(){
		console.log("静态方法");
	}
}

class Man extends Person{
	constructor(name){
		super(name);
		this.sex="male";
		}
	}

var man=new Man("wangwu");
man.getName();
Man.test();
// var q={
// 	qwe:"123",
// 	asd:"456",
// 	zxc:"789"
// }

	</script>
```
对象的__proto__指向它的构造函数的prototype
