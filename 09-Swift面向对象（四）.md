# Swift面向对象（四）

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

##### 添加方法

扩展可以为已有类型添加新的实例方法和类型方法：

```swift
class Student {
    var no: String
    var name: String
    var isMale: Bool
    var age: Int
    init(stuNo: String, stuName: String, sex: Bool, stuAge: Int) {
        no = stuNo
        name = stuName
        isMale = sex
        age = stuAge
    }
}

extension Student {
    
    func information() {
        print("No.:\(no)--Name:\(name)--Sex:\(isMale)--Age:\(age)")
    }
}

let Stu1 = Student(stuNo: "10001", stuName: "Tim", sex: false, stuAge: 10)
Stu1.information()
let Stu2 = Student(stuNo: "10002", stuName: "Ben", sex: true, stuAge: 1)
Stu2.information()
// No.:10001--Name:Tim--Sex:false--Age:10
// No.:10002--Name:Ben--Sex:true--Age:1
```

##### 添加构造器

扩展可以为已有类型添加新的构造器。这可以让你扩展其它类型，将你自己的定制类型作为其构造器参数，或者提供该类型的原始实现中未提供的额外初始化选项。**扩展能为类添加新的便利构造器，但是它们不能为类添加新的指定构造器或析构器。**指定构造器和析构器必须总是由原始的类实现来提供。


```swift
class Student {
    var no: String
    var name: String
    var isMale: Bool
    var age: Int
    init(stuNo: String, stuName: String, sex: Bool, stuAge: Int) {
        no = stuNo
        name = stuName
        isMale = sex
        age = stuAge
    }
}

extension Student {
    func information() {
        print("No.:\(no)--Name:\(name)--Sex:\(isMale)--Age:\(age)")
    }
    convenience init(stuNo: String, stuName: String) {
        self.init(stuNo: stuNo, stuName: stuName, sex: true, stuAge: 1)
    }
}

let Stu1 = Student(stuNo: "10001", stuName: "Tim", sex: false, stuAge: 10)
Stu1.information()
let Stu2 = Student(stuNo: "10002", stuName: "Ben")
Stu2.information()
// No.:10001--Name:Tim--Sex:false--Age:10
// No.:10002--Name:Ben--Sex:true--Age:1
```

##### 添加嵌套类型
扩展可以为已有的类、结构体和枚举添加新的嵌套类型，如添加枚举等类型：


```swift
class Student {
    var no: String
    var name: String
    var isMale: Bool
    var age: Int
    init(stuNo: String, stuName: String, sex: Bool, stuAge: Int) {
        no = stuNo
        name = stuName
        isMale = sex
        age = stuAge
    }
}
extension Student {
    func information() {
        print("No.:\(no)--Name:\(name)--Sex:\(isMale)--Age:\(age)")
    }
    convenience init(stuNo: String, stuName: String) {
        self.init(stuNo: stuNo, stuName: stuName, sex: true, stuAge: 1)
    }
    func getBloodGroup(blood: String) {
        print(blood.bloodGroup)
    }
}
extension String {
    
    enum BloodGroup {
        case A型血
        case B型血
        case AB型血
        case O型血
        case Other血型
    }
    var bloodGroup: BloodGroup {
        
        switch self {
        case "A":
            return BloodGroup.A型血
        case "B":
            return BloodGroup.B型血
        case "AB":
            return BloodGroup.AB型血
        case "O":
            return BloodGroup.O型血
        default:
            return BloodGroup.Other血型
        }
    }
}

let Stu1 = Student(stuNo: "10001", stuName: "Tim", sex: false, stuAge: 10)
Stu1.information()
Stu1.getBloodGroup(blood: "O")
// No.:10001--Name:Tim--Sex:false--Age:10
// O型血
```

## 协议
**协议规定了用来实现某一特定功能所必需的方法和属性。**某个类型能够满足某个协议的要求，就可以说该类型遵循这个协议。类、结构体或枚举都可以遵循协议，并为协议定义的这些要求提供具体实现。

> **Tips:**
> 除了遵循协议的类型必须实现的要求外，还可以对协议进行扩展，通过扩展来实现一部分要求或者实现一些附加功能，这样遵循协议的类型就能够使用这些功能。

### 语法格式

**1.定义协议**

协议的定义方式与类、结构体和枚举的定义非常相似：

```swift
protocol SomeProtocol {
    // 这里是协议的定义部分
}
```

**2.遵循协议**

要让自定义类型遵循某个协议，**在定义类型时，需要在类型名称后加上协议名称，中间以冒号`:`分隔，语法与继承语法类似**。遵循多个协议时，各协议之间用逗号`,`分隔：

```swift
class SomeClass: FirstProtocol, AnotherProtocol {
    // 这里是结构体的定义部分
}
```

> **注意：**
> 拥有父类的类在遵循协议时，应该将**父类名放在协议名之前，以逗号分隔，即父类放在第一个位置**：

> ```swift
> class SomeClass: SomeSuperClass, FirstProtocol, AnotherProtocol {
>     // 这里是类的定义部分
> }
> ```

### 属性要求

**协议可以要求遵循协议的类型提供特定名称和类型的属性。**协议不指定属性是存储型属性还是计算型属性，它只指定属性的名称和类型。此外，**协议还须指定属性是可读的还是可读可写的**。

> **Tips：**
> 1. 协议中的通常用var来声明变量属性，在类型声明后加上{ set get }来表示属性是可读可写的，只读属性则用{ get }来表示。
> 2. 如果协议要求属性是可读可写的，那么该属性不能是常量属性或只读的计算型属性。如果协议只要求属性是可读的，那么该属性不仅可以是可读的，如果代码需要的话，还可以是可写的。


```swift
protocol SomeProtocol {
    var mustBeSettable: Int { get set }
    var doesNotNeedToBeSettable: Int { get }
}


// 1.定义协议
protocol StudentProtocol {
    var name: String { get set}
    var no: String { get }
    var sex: Bool { get set }
}
// 2.遵守协议
class Monitor: StudentProtocol {
   
}
```

以上定义的了StudentProtocol协议，要求遵循协议的类型必须要提供读写属性name、 sex和只读属性no。同时有定义了一个Monitor类，并让它遵循StudentProtocol协议，但是Monitor并未提供协议中要求的三个属性，不满足协议的要求，所以编译器会报错，错误信息如下：
> Type 'Monitor' does not conform to protocol 'StudentProtocol'
因此，我们需要在Monitor类中提供所要求的三个属性：

```swift
protocol StudentProtocol {
    var name: String { get set}
    var no: String { get }
    var sex: Bool { get set }
}

class Monitor: StudentProtocol {
    var name: String = "Tim"
    var no: String = "10001"
    var sex: Bool = true
}
```

**计算属性**

一个类在遵循协议后，可以将协议所要求的属性实现为计算属性：

```swift
protocol StudentProtocol {
    var name: String { get set}
    var no: String { get }
    var sex: Bool { get set }
    var birthYear: Int { get set }
    var age: Int { get }
}

class Monitor: StudentProtocol {
    var name: String = "Tim"
    var no: String = "10001"
    var sex: Bool = true
    var birthYear: Int = 2018
    var age: Int {
        get{
            let date = Date()
            let calendar = NSCalendar.current
            let year = calendar.component(.year, from: date)
            return Int(year) - birthYear
        }
    }
}

var mon = Monitor()
mon.name = "Terry"
mon.no = "10002"
mon.sex = false
mon.birthYear = 2000
print("Name:\(mon.name)\nNo.:\(mon.no)\nSex:\(mon.sex)\nAge:\(mon.age)")
// Name:Terry
// No.:10002
// Sex:false
// Age:18
```

### 方法要求

**协议可以要求遵循协议的类型实现某些指定的方法**。这些方法作为协议的一部分，像普通方法一样放在协议的定义中，但是不需要大括号和方法体。可以在协议中定义具有可变参数的方法，和普通方法的定义方式相同。**但是，不支持为协议中的方法的参数提供默认值。**


```swift
protocol StudentProtocol {
    var name: String { get set}
    var no: String { get }
    var sex: Bool { get set }
    var birthYear: Int { get set }
    var age: Int { get }
    
    func chooseCourse(course: String) -> String
}

class Monitor: StudentProtocol {
    
    var name: String = "Tim"
    var no: String = "10001"
    var sex: Bool = true
    var birthYear: Int = 2018
    var age: Int {
        get{
            let date = Date()
            let calendar = NSCalendar.current
            let year = calendar.component(.year, from: date)
            return Int(year) - birthYear
        }
    }
    
    func chooseCourse(course: String) -> String {
        return "Course \(course) has been chosen by \(name)"
    }
}

var mon = Monitor()
print(mon.chooseCourse(course: "Swift"))
```

### 构造器要求

**协议可以要求遵循协议的类型实现指定的构造器**。我们可以像编写普通构造器那样，在协议的定义里写下构造器的声明，但不需要写花括号和构造器的实体：

```swift
protocol SomeProtocol {
    init(someParameter: Int)
}
```

我们可以在遵循协议的类中实现构造器，无论是作为指定构造器，还是作为便利构造器。无论哪种情况，我们都**必须为构造器实现标上 required 修饰符**，因为确保所有子类也必须提供此构造器实现，如下案例：

```swift
protocol VIP {
    var name: String { get set}
    var level: Int { get set }
    var points: Int { get set}
    
    init(name: String, level: Int, points: Int)
}

class Member: VIP {
    var name: String
    var level: Int
    var points: Int{
        get{
            return level * 500
        }
        set{
            self.level = newValue
        }
    }
    required init(name: String, level: Int, points: Int) {
        self.name = name
        self.level = level
    }
}
// 使用 required 修饰符可以确保所有子类也必须提供此构造器实现，从而也能符合协议。
```

> **注意：**
> 如果类已经被标记为 final，那么不需要在协议构造器的实现中使用 required 修饰符，因为 final 类不能有子类。关于 final 修饰符的更多内容，请参见防止重写。


```swift
protocol VIP {
    var name: String { get set}
    var level: Int { get set }
    var points: Int { get set}
    
    init(name: String, level: Int, points: Int)
}


class Member: VIP {
    var name: String = "Member"
    var level: Int = 1
    var points: Int{
        get{
            return level * 500
        }
        set{
            self.level = newValue
        }
    }
    required init(name: String, level: Int, points: Int) {
        self.name = name
        self.level = level
    }
    
}

class RegularMember: Member {
    var discount: Double = 1
    convenience init(discount: Double) {
        self.init(name: "Regular Member", level: 2, points: 100)
        self.discount = discount
    }

    required init(name: String, level: Int, points: Int) {
        super.init(name: name, level: level, points: points)
    }
}

var regular = RegularMember(discount: 0.7)
print(regular.points)
```

