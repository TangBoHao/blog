---
title: php面向对象知识点
date: 2018-05-02 21:23:23
---
##php面向对象基本用法的总结
<!-- more -->
```php
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
<head>
	<meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
	<title>test</title>
</head>
<body>
<?php
//interface关键字用于定义接口
//接口也可以使用extends关键字来继承接口 当类实现子接口时，父接口定义的方法也需要在这个类里面具体实现
//类的方法必须实现，接口的方法必须为空
//因为接口的实现方法可以有很多，所以对于接口里面定义的方法的具体的实现是多种多样的，这种特性我们称为多态
//相同的一行代码，对于传入不同的接口的实现的对象的时候，表现是不同的，这就是多态
interface iamliving{
	//接口里面的方法不需要有实现的方法
	public function live();
}


//abstract关键字用于定义抽象类
abstract class animal{
	//abstract关键字表示抽象方法，不需要实现
	//继承抽象类的子类需要实现抽象类中的抽象类方法
	abstract public function eat($food);
}

//类的定义以关键词class开始，后面跟名称，通常第一个字母大写，以中括号开始和结束
//implements关键字用于表示类实现某个接口
class Man implements iamliving{
	//定义属性
	public $name="alex";
	public $age=25;
	public $sex="male";
	//静态属性定义时在访问控制关键字后加static关键字
	public static $president="god";
	public static function changePresident($newPresident){
		//类中使用静态成员时，用self关键字后面跟着::操作符，后面需要跟$符号  parent关键字访问父类的静态属性
		//静态方法中不能访问非静态数据
		self::$president=$newPresident;
	}

	//定义方法
	//实现了某个接口之后，必须提供接口中定义的方法的具体实现
	public function live(){
		echo "i am living";
	}


	public function run(){
		echo "running\n";
	}

	//final关键字能够让这个方法不能在子类中被重写   final加在class前不允许类被继承
	final public function eat($food){
		echo $this->name."was eating".$food.'<br/>';
	}

	//构造函数在对象被实例化的时候调用
	function __construct($name,$age,$sex){
		echo "构造函数被调用";
		//$this是php中伪变量，表示对象自身，可以通过$this->的方式访问对象的属性和方法
		$this->name=$name;
		$this->age=$age;
		$this->sex=$sex;
	}
	//析构函数，在程序执行结束的时候会自动调用
	//通常用于清理程序使用的资源，比如程序使用了打印机，那么可以在析构函数里面释放打印机资源
	function __destruct(){
		echo $this->name."的析构函数被调用";
	}
}
//类的实例化使用关键词new，之后跟类的名称和括号，用类名后面的参数列表初始化参数
$xiaoming=new Man("xiaoming",16,"male");


//类定义外部访问静态属性，可以用类名加::操作符的方法来访问类的静态成员
Man::changePresident("fo");
echo Man::$president."<br/>";

echo $xiaoming->name;
var_dump($xiaoming);
//$xiaoming=null 手动触发析构函数
//当对象不会再使用时，会触发析构函数

//extends关键字表示类的继承，只能单继承
class Student extends Man{
	public $grade;
}

//可以用instanceof关键字来判断某个对象是否实现了某个接口
var_dump($xiaoming instanceof iamliving);

/*
数据访问
parent::关键字可以访问父类中被子类重写的方法
self::可以访问自身的成员方法，也可用于访问自身的静态成员和类常量；不能用于访问类自身的属性；使用常量的时候不需要在常量名称前添加$符号
static关键字用于访问类自身定义的静态成员，访问静态属性的时候需要在属性面前添加$符号
*/


//魔术方法
//__tostring() 当对象被当作string使用时，这个方法会被自动调用。 echo $obj
//__invoke() 当对象被当成方法调用时，这个方法会被自动调用  $obj(4)
//__call() 当对象访问不存在的方法名称时，该方法会被自动调用
//__callStatic() 当对象访问不存在的静态方法名称时，该方法会被自动调用 这两个方法在php里面称为方法的重载
/*
__get() __set() __isset() __unset
在给不可访问的属性赋值时，__set()会被调用
读取不可访问的的属性的值时，__get()会被调用
当对不可访问属性调用isset()或empty()时，__isset()会被调用
当对不可访问属性调用unset()时，__unset()会被调用
所谓不可访问属性，实际上就是在调用某个属性时发现这个属性没有被定义，这时候不同的操纵会触发不同的魔术方法
这几个方法也被称为属性重载的魔术方法


*/


class MagicTest{
	public function __tostring(){
		return "this is the class MagicTest";
	}
	public function __invoke($x){
		echo "__invoke called with parameter".$x;
	}
	//方法的第一个参数是调用方法的名称，第二个是方法调用的参数组成的数组
	public function __call($name,$argumengts){
		echo "Calling".$name."with parameter:".implode(",",$argumengts);
	}
	public static function __callStatic($name,$argumengts){
		echo "Calling".$name."with parameter:".implode(",",$argumengts);
	}
	public function __get($name){
		return "getting the property ".$name;
	}
	public function __set($name,$value){
		echo "setting the property ".$name." to value ".$value."<br/>";
	}
	public function __isset($name){
		echo "__isset invoked";
		return true;
	}
	public function __unset($name){
		echo "unsetting property ".$name."<br/>";
	}

}
$obj= new MagicTest();
echo $obj;
echo "<br/>";
$obj(8);
echo "<br/>";
$obj->runtest("one","two"."three");
echo "<br/>";
MagicTest::runtest("one","two"."three");
echo "<br/>";
echo $obj->classname;
$obj->classname="magicx";
isset($obj->classname);
unset($obj->classname);


class Person{
	public $name;
	function __clone(){
		$this->name="cloned";
	}
}
$kate =new Person();
$kate->name="kate";
$kate2=clone $kate;
echo "<br/>";
echo $kate->name;
echo $kate2->name;
?>
</body>
</html>
```