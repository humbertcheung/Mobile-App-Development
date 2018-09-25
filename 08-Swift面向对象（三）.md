# Swift面向对象（三）

### 构造器代理规则

构造器代理规则，可以简单地理解为类的指定构造器和便利构造器间的相互调用规则。

**规则 1**

指定构造器必须调用其直接父类的的指定构造器。

**规则 2**

便利构造器必须调用同类中定义的其它构造器。

**规则 3**

便利构造器必须最终导致一个指定构造器被调用。


> **Tips：**
> 构造器代理规则可以简单的记忆为：

> * 指定构造器必须总是向上代理
> * 便利构造器必须总是横向代理


```swift
class Person {
    var name: String = "张三"
    var isMale: Bool = true
    var birthYear = 2000
    var age: Int = 1
    
    init(name: String, isMale: Bool, birthYear: Int, age: Int) {
        self.name = name
        self.isMale = isMale
        self.birthYear = birthYear
        self.age = age
    }
    // 指定构造器
    convenience init(name: String) {
        self.init(name: name, isMale: true, birthYear: 2018, age: 1)
        // 调用同类中的指定构造器
    }
    // 便利构造器
}

class Student: Person {
    var stuNo = "00001"
    init(stuNo: String) {
        super.init(name: "Tim", isMale: true, birthYear: 1999, age: 1)
        // 调用直接父类的指定构造器
        self.stuNo = stuNo
    }
}

var stu = Student(stuNo: "100001")
print(stu.stuNo)
```
当我们调用`Student(stuNo:)`时，首先会通过`super`调用父类的`init(name:isMale:birthYear:age:)`指定构造器来初始化Person父类，完成属性的初始化工作。当父类的初始化工作完成后，就可以初始化子类的属性、定制化之类的相关属性。

## 两段式构造过程

Swift 中类的构造过程包含两个阶段:

**第一阶段，**类中的索引入的每个存储型属性会被指定一个初始值，确保每个属性都有初始值。
**第二阶段**在每个存储型属性的初始值被确定后，它给每个类一次机会，在新实例准备使用之前进一步定制它们的存储属性。

两段式构造过程可以防止属性值在初始化之前被访问，也可以防止属性被另外一个构造器意外地赋予不同的值。


### 安全检查

Swift 编译器将执行 4 种有效的安全检查，以确保两段式构造过程能不出错地完成：

***安全检查 1***
指定构造器必须保证它所在类引入的所有属性都必须先初始化完成，之后才能将其它构造任务向上代理给父类中的构造器，即**必须确保子类所引入的所有属性都有初始值，然后才能调用父类的指定构造函数，否则编译器会报错。**

```swift
class Person {
    var name: String = "张三"
    var isMale: Bool = true
    var birthYear = 2000
    var age: Int = 1
    
    init(name: String, isMale: Bool, birthYear: Int, age: Int) {
        self.name = name
        self.isMale = isMale
        self.birthYear = birthYear
        self.age = age
    }
    // 指定构造器
    convenience init(name: String) {
        self.init(name: name, isMale: true, birthYear: 2018, age: 1)
        // 调用同类中的指定构造器
    }
    // 便利构造器
}

class Student: Person {
    var stuNo: String
    init(stuNo: String) {
        super.init(name: "Tim", isMale: true, birthYear: 1999, age: 1)
        // 调用直接父类的指定构造器
        self.stuNo = stuNo
    }
}
var stu = Student(stuNo: "100001")
print(stu.stuNo)
```

以上代码会报错，因为当子类Student在调用父类的指定构造函数时，子类引入了stuNo属性，但是stuNo属性没有初始值，所以需要先对stuNo属性进行初始化，然后才能调用父类指定构造函数。

***安全检查 2***
指定构造器必须先向上代理调用父类构造器，然后才能为继承的属性设置新值。如果没这么做，指定构造器赋予的新值将被父类中的构造器所覆盖，所以**指定构造器必须要调用父类的指定构造器，否则编译器会报错。**

```swift
class Student: Person {
    var stuNo: String = "1001"
    init(stuNo: String) {
        //super.init(name: "Tim", isMale: true, birthYear: 1999, age: 1)
        // 调用直接父类的指定构造器
        self.stuNo = stuNo
    }
}
// 代码会报错：'super.init' isn't called on all paths before returning from initializer 
// 需要调用父类的指定构造器
```

***安全检查 3***
便利构造器必须先代理调用同一类中的其它构造器，然后再为任意属性赋新值。如果没这么做，便利构造器赋予的新值将被同一类中其它指定构造器所覆盖。所以**便利构造器必须要调用同类中的其他构造器，否则编译器会报错。**

```swift
class Person {
    var name: String = "张三"
    var isMale: Bool = true
    var birthYear = 2000
    var age: Int = 1
    
    init(name: String, isMale: Bool, birthYear: Int, age: Int) {
        self.name = name
        self.isMale = isMale
        self.birthYear = birthYear
        self.age = age
    }
    // 指定构造器
    convenience init(name: String) {
        //self.init(name: name, isMale: true, birthYear: 2018, age: 1)
        //self.name = name
    }
    // 便利构造器
}
// 代码报错：'self.init' isn't called on all paths before returning from initializer
// 便利构造器需要调用同类中的其他构造器
```

***安全检查 4***
构造器在第一阶段构造完成之前，不能调用任何实例方法，不能读取任何实例属性的值，不能引用self作为一个值。

类实例在第一阶段结束以前并不是完全有效的。只有第一阶段完成后，该实例才会成为有效实例，才能访问属性和调用方法。


```swift
class Student: Person {
    var stuNo: String = "1001"
    init(stuNo: String) {
        self.name = "Terry"
        super.init(name: "Tim", isMale: true, birthYear: 1999, age: 1)
        // 调用直接父类的指定构造器
        self.stuNo = stuNo
    }
}
// 代码报错：'self' used in property access 'name' before 'super.init' call

```

以下是两段式构造过程中基于上述安全检查的构造流程展示：

> ***阶段 1***

> * 某个指定构造器或便利构造器被调用。
> * 为新实例分配内存，但此时实例的内存还没有被初始化。
> * 指定构造器确保其所在类引入的所有存储属性都已赋初值，存储属性所属的内存完成初始化。
> * 指定构造器将调用父类的构造器，完成父类属性的初始化。
> * 这个调用父类构造器的过程沿着构造器链一直往上执行，直到到达构造器链的最顶部。
> * 当到达了构造器链最顶部，且已确保所有实例包含的存储型属性都已经赋值，这个实例的内存被认为已经完全初始化。此时阶段 1 完成。

> ***阶段 2***

> * 从顶部构造器链一直往下，**每个构造器链中类的指定构造器都有机会进一步定制实例**。构造器此时可以访问self、修改它的属性，甚至可以调用实例方法。
> * 最终，任意构造器链中的**便利构造器**可以**有机会定制实例和使用self**。


### 构造器的继承和重写

跟 Objective-C 中的子类不同，Swift 中的**子类默认情况下不会继承父类的构造器**。Swift 的这种机制可以防止一个父类的简单构造器被一个更精细的子类继承，并被错误地用来创建子类的实例。

> **注意：**
> 父类的构造器默认不会被子类继承，仅会在安全和适当的情况下被继承，即当满足自动继承条件时才会。

不过，我们时可以重写父类的指定构造函数的，只要在函数证明前加上override即可，但是根据构造器的代理规则，子类是不能直接调用父类的便利构造函数，我们在“重写”父类便利构造函数时，实际上并不是真正意义上的重写，所以在“重写”父类便利构造函数时并不需要添加上override关键字：

```swift
class Person {
    var name: String = "张三"
    var isMale: Bool = true
    var birthYear = 2000
    var age: Int = 1
    
    init(name: String, isMale: Bool, birthYear: Int, age: Int) {
        self.name = name
        self.isMale = isMale
        self.birthYear = birthYear
        self.age = age
    }
    // 指定构造器
    convenience init(name: String) {
        self.init(name: name, isMale: true, birthYear: 2018, age: 1)
        self.name = name
    }
    // 便利构造器
}

class Student: Person {
    var stuNo: String = "1001"
    init(stuNo: String) {
        super.init(name: "Tim", isMale: true, birthYear: 1999, age: 1)
        self.stuNo = stuNo
    }
    convenience init(name: String){
        self.init(stuNo: "1001")
        self.name = name
    }
}
// 子类Student中的init(stuNo:)虽然与父类的便利构造函数同名类似于重写，但严格意义上来说并不是重写，所以不需要添加override关键字。
```

接下去再来看另外一种情况，我们对以上的案例进行简化，如下：

```swift
class Person {
    var name: String = "张三"
    var description: String{
        return "Name: \(name)"
    }
}
// Person没有定义构造器，那么编译器会给它提供一个默认构造器init(),并且成为了Person的指定构造器，它可以用来创建属性name为“张三”的Person对象。

class Student: Person {
    override init() {
        super.init()
        self.name = "李四"
    }
}
// 1、在子类Student中定义了一个构造器init()。这个指定构造器与父类的指定构造器init()相匹配，那么会被编译器认定为是父类指定构造器的重写版本，所以需要带上override关键字
// 2、在子类当中，因为我们重写了父类的指定构造函数init()，那么根据构造器代理规则，需要调用父类的指定构造器，即：super.init()它所继承的属性name也可以被父类初始化
// 3、在在调用super.init()之后，我们可以定制自己的属性所以name的原值被新值“李四”替换掉。

let Per = Person()
print(Per.description)
let Stu = Student()
print(Stu.description)
// Name: 张三
// Name: 李四
```

> **注意：**
> 1、自动获得的默认构造器总会成为该类中的指定构造器；
> 2、子类可以在初始化时修改继承来的变量属性，但是不能修改继承来的常量属性。

### 构造器的自动继承

如前面所说，子类在默认情况下不会继承父类的构造器。但是如果满足特定条件，父类构造器是可以被自动继承的。在实践中，这意味着对于许多常见场景我们不必重写父类的构造器，并且可以在安全的情况下以最小的代价继承父类的构造器。

#### 自动继承规则

**规则 1**

如果子类**没有定义任何指定构造器**，它将**自动继承所有父类的指定构造器**。

**规则 2**

如果子类**提供了所有父类指定构造器的实现**（不管是通过规则 1 继承过来的，还是提供了自定义实现），那么它将**自动继承所有父类的便利构造器**。

即使我们在子类中添加了更多的便利构造器，这两条规则仍然适用。

```swift
class Person {
    var name: String
    var description: String
    init() {
        name = "张三"
        description = "Person：" + name
    }
    
    convenience init(name: String) {
        self.init(name: name, desc: "Person：" + name)
    }
    
    convenience init(desc: String) {
        self.init(name: "张三", desc: desc)
    }
    
    init(name: String, desc: String) {
        self.name = name
        description = desc
    }
}
class Student: Person {
    var stuNo = "1001"
    override init() {
        super.init()
        self.name = "李四"
        self.description = "Student：" + name
    }
    override init(name: String, desc: String) {
        super.init(name: name, desc: desc)
        self.name = "李四"
        self.description = "Student：" + name
    }
    convenience init(no: String) {
        self.init()
        stuNo = no
    }
}
let Per = Person()
print(Per.description)
let Stu = Student()
print(Stu.description)
// Person：张三
// Student：李四
```

### 必要构造器

在类的构造器前添加`required`修饰符表明所有该类的子类都必须实现该构造器，在重写父类中必要的指定构造器时，不需要添加override修饰符：

```swift
class Person {
    var name: String
    var description: String
    init() {
        name = "张三"
        description = "Person：" + name
    }
    
    convenience init(name: String) {
        self.init(name: name, desc: "Person：" + name)
    }
    
    convenience init(desc: String) {
        self.init(name: "张三", desc: desc)
    }
    
    required init(name: String, desc: String) {
        self.name = name
        description = desc
    }
}
class Student: Person {
    var stuNo = "1001"
    override init() {
        super.init()
        self.name = "李四"
        self.description = "Student：" + name
    }
    required init(name: String, desc: String) {
        super.init(name: name, desc: desc)
        self.name = "李四"
        self.description = "Student：" + name
    }
    convenience init(no: String) {
        self.init()
        stuNo = no
    }
}
let Per = Person()
print(Per.description)
let Stu = Student()
print(Stu.description)
```

## 扩展

扩展就是**为一个已有的类、结构体、枚举类型或者协议类型添加新功能**。这包括在没有权限获取原始源代码的情况下扩展类型的能力（即 逆向建模 ）。扩展和 Objective-C 中的分类类似。（与 Objective-C 不同的是，Swift 的扩展没有名字。）

Swift 中的扩展可以：

* 添加计算型属性和计算型类型属性
* 定义实例方法和类型方法
* 提供新的构造器
* 定义下标
* 定义和使用新的嵌套类型
* 使一个已有类型符合某个协议

> **注意：**
> 扩展可以为一个类型添加新的功能，但是不能重写已有的功能。

#### 语法格式

使用关键字`extension`来声明扩展：


```swift
extension SomeType {
    // 为 SomeType 添加的新功能写到这里
}
```

可以通过扩展来扩展一个已有类型，使其采纳一个或多个协议。在这种情况下，无论是类还是结构体，协议名字的书写方式完全一样：

```swift
extension SomeType: SomeProtocol, AnotherProctocol {
    // 协议实现写到这里
}
```

> **Tips：**
> 如果我们通过扩展为一个已有类型添加新功能，那么**新功能对该类型的所有已有实例都是可用的**，即使它们是在这个扩展定义之前创建的。


##### 添加计算属性

扩展可以为已有类型添加计算属性。如下案例，为 Swift 的内建 Double 类型添加了几个计算型实例属性，从而提供与距离单位协作的基本支持：


```swift
extension Double {
    var km: Double { return self * 1000.0 }
    var m : Double { return self }
    var cm: Double { return self / 100.0}
    var mm: Double { return self / 1000.0  }
}

let Ten_km = 10.0.km
let Seven_m = 7.0.m
let Seven_cm = 7.0.cm
let aMarathon = 42.km + 195.m

print("Ten km = \(Ten_km)m")
print("Seven m = \(Seven_m)m")
print("Seven cm = \(Seven_cm)m")
print("Marathon = \(aMarathon)m")
// Ten km = 10000.0m
// Seven m = 7.0m
// Seven cm = 0.07m
// Marathon = 42195.0m
```

这些计算型属性表达的含义是把一个 Double 值看作是某单位下的长度值。即使它们被实现为计算型属性，但这些属性的名字仍可紧接一个浮点型字面值，从而通过点语法来使用，并以此实现距离转换。

> **注意：**
> 扩展可以添加新的计算型属性，但是不可以添加存储型属性，也不可以为已有属性添加属性观察器。




