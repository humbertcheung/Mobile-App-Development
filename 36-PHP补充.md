# PHP补充

## 面向对象

### 成员访问

我们在创建一个类时可以为其添加成员属性和成员方法，其中成员属性有可以分为类变量（动态）和类常量（静态）。

#### 类变量

需要使用var 或者访问控制修饰符声明，在定义时可以为空。

```php
<?php
class Student{
		//类变量
		var $stuNo;
		var $stuName = "Elizabeth";
		var $courses = array();
		
		function showMessage() {
			$this->courses[0] = "English";
			echo $this->courses[0];
		}
	}
	
	$stu = new Student();
	echo $stu->stuName . "<br />";
?>
```

在调用时，需要使用成员访问符`->`来调用。

#### 类常量

定义类常量使用 `const` 关键字来定义，并且在定义时不可为空，必须要赋值。常量不能使用访问控制修饰符，默认是public的，可继承也可被子类重写。

```php
<?php
class Student{
		//类变量
		var $stuNo = "1001";
		var $stuName = "Elizabeth";
		var $courses = array();
		
		//类常量
		const school = "AIIT";
		
		function showMessage() {
			$this->courses[0] = "English";
			echo $this->courses[0];
		}
	}
	
	$stu = new Student();
	echo $stu->stuName . "<br />";
	echo Student::school;
	echo $stu::school;
?>
```

类常量在调用时，需要使用双冒号`::`，可以使用**类名或类的实例（对象）来访问**，因为是常量，所以名称不能使用表示变量的符号`$`

> 注意：
> 类的常量是一个值，在代码编译器常量名被替换为相应的值，**在运行期不可修改**，因此类的常量与类本身无关，在类实例化对象之前就已经存在，因此类的常量可以直接使用类名访问


