# Swift语言基础（四）

### 函数（Function）

Swift 函数是用来完成特定任务的独立的代码块。

> Swift使用一个统一的语法来表示简单的C语言风格的函数到复杂的Objective-C语言风格的方法。在Swift中，函数参数可以提供默认值，以简化函数调用。参数也可以既当做传入参数，也当做传出参数，也就是说，一旦函数执行结束，传入的参数值将被修改。每个函数都有一个由函数的参数值类型和返回值类型组成的类型。你可以把函数类型当做任何其他普通变量类型一样处理，这样就可以更简单地把函数当做别的函数的参数，也可以从其他函数中返回函数。函数的定义可以写在其他函数定义中，这样可以在嵌套函数范围内实现功能封装。

* 函数声明: 告诉编译器函数的名字，返回类型及参数。

* 函数定义: 提供了函数的实体。

Swift 函数包含了参数类型及返回值类型：

#### 函数的定义

*语法格式：*

```swift
func funcname(形参) -> returntype
{
   Statement1
   Statement2
   ……
   Statement N
   return parameters
}
```

1. Swift 定义函数使用关键字 **func**。

2. 每个函数都有一个函数名来描述它的功能。通过函数名以及对应类型的参数值来调用这个函数。函数的参数传递的顺序必须与参数列表相同。

3. 定义函数的时候，可以指定一个或多个形式参数和一个返回值类型。

4. 函数的实参传递的顺序必须与形参列表相同，-> 后定义函数的返回值类型。


```swift
func getMessage(message: String) -> String {
    return String("Welcome, \(message)!")
}
//函数调用
print(getMessage(message: "Leo"))
//打印： Welcome, Leo!
```

#### 函数参数

在 Swift 中，函数参数与返回值是非常灵活的。我们可以定义任何类型的函数，包括从只带一个未名参数的简单函数到复杂的带有表达性参数名和不同参数选项的复杂函数。

***无参函数***

允许函数可以没有参数，但是定义中在函数名后还是需要一对圆括号。当被调用时，也需要在函数名后写一对圆括号。

```swift
func getMessage() -> String {
    return String("Welcome!")
}
print(getMessage())
// Welcome!
```

***多参函数***

允许函数有多种输入参数，这些参数被包含在函数的括号之中，以逗号分隔开。

```swift
func getMessage(name: String, scene: String) -> String {
    return String("Hi, \(name), Welcome to \(scene)!")
}
print(getMessage(name: "Leo", scene: "Game Center"))
//打印： Hi, Leo, Welcome to Game Center!
```

#### 函数返回值

**无返回值函数**

允许函数没有返回值，那么->及返回值类型可以省略不写

```swift
func getMessage(name: String, scene: String) {
    print(String("Hi, \(name), Welcome to \(scene)!"))
}
getMessage(name: "Leo", scene: "Game Center")
//打印： Hi, Leo, Welcome to Game Center!
```
> **注意：**
> 严格上来说，虽然没有返回值被定义，但是getMessage(name: String, scene: String) 函数依然返回了值。没有定义返回类型的函数会返回一个特殊的Void值。它其实是一个空的元组（tuple），没有任何元素，可以写成()。

**多返回值函数**

Swift还允许我们使用元组（tuple）类型来让多个值作为一个复合值从函数中返回。

```swift
func minMax(array: [Int]) -> (min: Int, max: Int) {
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
let bounds = minMax(array: [9, -6, 7, 14, 1, 81])
print("min is \(bounds.min) and max is \(bounds.max)")
// min is -6 and max is 81
```

在以上代码中，声明了一个函数，用来在一个 Int 类型的数组中找出最小值与最大值。函数参数是需要查找的Int型数组，最后会将找到的最大值与最小值组合成一个元祖返回出来。元组中的值被标记为 min 和 max ，以便查询函数的返回值时可以通过名字访问它们。当然，也可以不标记这些值，返回时可以通过索引来访问。

**可选元祖返回值**

如果函数返回的元组类型有可能整个元组都没有值，则我们使用可选的（ optional ） 元组返回类型来表示。你可以通过在元组类型的右括号后放置一个问号来定义一个可选元组。

当我们不确定返回的元组一定不为nil时，那么你可以返回一个可选的元组类型。

```swift
func minMax(array: [Int]) -> (min: Int, max: Int)? {
    if array.isEmpty { return nil }
    var currentMin = array[0]
    var currentMax = array[0]
    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}
if let bounds = minMax(array: [9, -6, 7, 14, 1, 81]) {
    print("min is \(bounds.min) and max is \(bounds.max)")
}
// min is -6 and max is 81
```

> **注意：**
> 可选元组类型如`(Int, Int)?`与元组包含可选类型如`(Int?, Int?)`是不同的。可选的元组类型，整个元组是可选的，而不只是元组中的每个元素值。


#### 函数参数标签和参数名称

每个函数参数都有一个参数标签( argument label )以及一个参数名称( parameter name )。***参数标签在调用函数的时候使用；***调用的时候需要将函数的参数标签写在对应的参数前面。***参数名称在函数的实现中使用***。默认情况下，函数参数使用参数名称来作为它们的参数标签。

***指定参数标签***
参数标签的使用能够让一个函数在调用时更有表达力，更类似自然语言，并且仍保持了函数内部的可读性以及清晰的意图。我们可以在参数名称前指定它的参数标签，中间以空格分隔：

```swift
func someFunction(argumentLabel parameterName: String) {
    // argumentLabel是参数标签，在外部函数调用时使用
    // parameterName是参数名称，在函数体内使用
    
    print(argumentLabel)
    //报错，在函数体内不能使用参数标签
    print(parameterName)
}
someFunction(argumentLabel: "函数标签与函数名称")
someFunction(parameterName: "函数标签与函数名称")
//报错，调用函数时，不能使用参数名称
```


```swift
func greet(person: String, from hometown: String) -> String {

    return "Hello \(person)! Glad you could visit from \(hometown)."
}
print(greet(person: "Bill", from: "Cupertino"))
// 打印 "Hello Bill! Glad you could visit from Cupertino."
```

***忽略参数标签***

当然，我们也可以在不需要使用参数标签时，通过下划线`_`来替代参数标签，这时候就可以忽略参数标签了：


```swift
func greet(_ person: String, from hometown: String) -> String {
    
    return "Hello \(person)! Glad you could visit from \(hometown)."
}
print(greet("Bill", from: Cupertino))
```
在以上案例中，我们通过下划线忽略了第一个参数的参数标签，那么我们在调用函数时就不显示参数标签了。

#### 参数默认值

在Swift当中，我们还可以为函数的参数指定一个默认值。在定义函数时，可以直接给函数参数赋值来实现默认值的指定。

```swift
func greet(_ person: String, from hometown: String = "China") -> String {
    
    return "Hello \(person)! Glad you could visit from \(hometown)."
}
print(greet("Bill"))
// Hello Bill! Glad you could visit from China.
print(greet("Bill", from: "Cupertino"))
// Hello Bill! Glad you could visit from Cupertino.
```
在给参数设置默认值后，当我们未传参数时，函数会使用参数的默认值；当我们传入参数后，传入的参数会替代默认值。

> **Tips：**
> **建议将不带有默认值的参数放在函数参数列表的最前。** 一般来说，没有默认值的参数更加的重要，将不带默认值的参数放在最前保证在函数调用时，非默认参数的顺序是一致的，同时也使得相同的函数在不同情况下调用时显得更为清晰。

#### 可变参数

Swift还允许我们使用可变参数，一个可变参数（variadic parameter）可以接受**零个或多个值**。函数调用时，你可以用可变参数来指定函数参数可以被传入**不确定数量**的输入值。通过在变量类型名后面加入（...）的方式来定义可变参数。一旦我们将参数声明为可变参数，那么接收到传入值的可变参数**变为对应类型的数组**。

> **注意：**
> 一个函数最多只能拥有一个可变参数。

```swift
func getSum(nums: Int..., operation: String) -> Int{
    var sum: Int = 0
    for i in nums {
        sum += i
    }
    print(operation)
    return sum
}
print(getSum(nums: 10, 40, 63, 50, 86, operation: "Get Sum"))
// Get Sum
// 203
```

#### 输入输出参数


**函数参数默认是常量**。试图在函数体中更改参数值将会导致编译错误(compile-time error)。如果你想要一个函数可以修改参数的值，并且想要在这些修改**在函数调用结束后仍然存在**，那么就应该把这个参数定义为**输入输出参数（In-Out Parameters）**。

定义一个输入输出参数时，在参数定义前加 ***inout*** 关键字。一个输入输出参数有传入函数的值，这个值被函数修改，然后被传出函数，替换原来的值。


```swift
func changeMessage(oldMessage: inout String ){
    oldMessage = oldMessage + "-iflysse"
}
var msg = "Study Platform"
changeMessage(oldMessage: &msg)
print(msg)
// TrainningPlatform-iflysse

func swapNums(a: inout Int, b: inout Int) {
    let temp = a
    a = b
    b = temp
}
var a = 7
var b = 70
swap(&a, &b)
print("a=\(a)\nb=\(b)")
// a=70
// b=7
```

#### 函数类型

每个函数都有种特定的函数类型，函数的类型由函数的参数类型和返回类型组成。

在之前的例子中，函数`func changeMessage(oldMessage: inout String )`的函数类型是`（inout Int）->Void`，再如函数`func printHelloWorld() ->String`，那么他的函数类型是`() ->String`

在Swift中，可以将函数类型当成常规的类型来使用：

```swift
func changeMessage(oldMessage: inout String ){
    oldMessage = oldMessage + "-iflysse"
}
var changeMSG: (inout String) -> Void = changeMessage
var msg = "Trainning-Platform"
changeMSG(&msg)
print(msg)
```

这段代码可以被解读为：定义了一个叫做 changeMSG 的变量，类型是“有一个 String 型的参数并且无返回值的函数”，并让这个新变量指向 changeMessage 函数。因为changeMSG和changeMessage有同样的类型，所以这个赋值过程在 Swift 类型检查(type-check)中是允许的。


**函数类型当作参数类型**

在swift中，既然函数类型可以当成常规类型来使用，那么我们也可以将函数类型当作参数类型来使用。


```swift
func changeMessage(oldMessage: String ) -> String{
    return "\(oldMessage)-iflysse"
}

func showMessage(times: Int, oldMsg: String, changing: (String) -> String){ 
    for _ in 0..<times {
        print(changing(oldMsg))
    }
}
var msg = "Aiit"
let changeMsg = changeMessage
showMessage(times: 5, oldMsg: msg, changing: changeMsg)
// Aiit-iflysse
// Aiit-iflysse
// Aiit-iflysse
// Aiit-iflysse
// Aiit-iflysse
```

**函数类型当作返回值类型**

swift还允许我们将函数类型作为另一个函数的返回类型。我们需要做的是在返回箭头（->）后写一个完整的函数类型。


```swift
func plus(a: Int, b: Int) -> Int{
    return a + b
}

func minus(a: Int, b: Int) -> Int{
    return a - b
}

func counting(isPlus: Bool) -> (Int, Int) -> Int {
    return isPlus ? plus : minus
}

var operation = counting(isPlus: true)
print(operation(70, 7))

```

#### 嵌套函数

到目前为止，我们文件中直接创建的函数是全局函数（global functions），它们是被定义在全局域中的。我们也可以把函数定义在别的函数体中，称作**嵌套函数（nested functions）**。

我么可以将上个例子中的两个函数，改写成嵌套函数：

```swift
func counting(isPlus: Bool) -> (Int, Int) -> Int {
    func plus(a: Int, b: Int) -> Int{
        return a + b
    }
    func minus(a: Int, b: Int) -> Int{
        return a - b
    }
    return isPlus ? plus : minus
}

var operation = counting(isPlus: true)
print(operation(70, 7))
```

### 闭包

闭包是自包含的函数代码块，**可以在代码中被传递和使用**。Swift 中的闭包与 C 和 Objective-C 中的代码块（blocks）以及其他一些编程语言中的匿名函数比较相似。

闭包有以下几种形式：

![](media/15362785280131/15365373886292.jpg)


Swift 的闭包表达式拥有简洁的风格，并鼓励在常见场景中进行语法优化，主要优化如下：

* 利用上下文推断参数和返回值类型
* 隐式返回单表达式闭包，即单表达式闭包可以省略 return 关键字
* 参数名称缩写，简化参数名
* 尾随闭包语法

**语法格式**

```swift
{(parameters) -> return type in
   statements
}
// 闭包的函数体部分由关键字in引入。该关键字表示闭包的参数和返回值类型定义已经完成，闭包函数体即将开始。

// 如下：
{(Int, Int) -> Bool in
   Statement 1
   Statement 2
    ---
   Statement n
}

var plus = {(a: Int, b: Int) ->Int in
    
    return (a + b)
}
let p = plus(10, 7)
print(p)
```
闭包的应用：

```swift
let names = [40, 27, 63, 15, 75, 42]
var sort = {(a: Int, b: Int) -> Bool in
    return a > b
}
let sortedNames = names.sorted(by: sort)
print(sortedNames)
// [75, 63, 42, 40, 27, 15]
```


#### 闭包表达式

闭包表达式是一种利用简洁语法构建内联闭包的方式。闭包表达式提供了一些语法优化，使得撰写闭包变得简单明了。

***sorted()***
Swift 标准库提供了名为 sorted(by:) 的方法，它会根据你所提供的用于排序的闭包函数将已知类型数组中的值进行排序。一旦排序完成，sorted(by:) 方法会返回一个与原数组大小相同，包含同类型元素且元素已正确排序的新数组。原数组不会被 sorted(by:) 方法修改。

sorted(by:) 方法接收一个闭包，该闭包函数需要传入与数组元素类型相同的两个值，并返回一个布尔类型值来表明当排序结束后传入的第一个参数排在第二个参数前面还是后面。**如果第一个参数值出现在第二个参数值前面，排序闭包函数需要返回true，反之返回false**。当该方法被整型数组调用是，参数必须是：(Int, Int) -> Bool等类型的函数；当该方法被字符串数组调用，参数必须是 (String, String) -> Bool 类型的函数。

> **Tips:**
> 闭包表达式参数,可以是 inout 参数，但**不能设定默认值**。也可以使用可变参数,但是如果可变参数不放在参数列表的最后一位的话，调用闭包时编译器将报错。元组也可以作为参数和返回值。


```swift
//闭包表达式1：内联闭包
let names = [40, 27, 63, 15, 75, 42]
let sortedNames = names.sorted(by: {(a: Int, b: Int) -> Bool in
    return a > b
})
print(sortedNames)
```

**根据上下文推断**

因为排序闭包函数是作为 sorted(by:) 方法的参数传入的，Swift具有类型推断的机制，所以可以推断其参数和返回值的类。

```swift
let names = [40, 27, 63, 15, 75, 42]
let sortedNames = names.sorted(by: {a, b in return a > b })
print(sortedNames)
```

所以说，通过内联闭包表达式构造的闭包作为参数传递给函数或方法时，swift是可以推断出闭包的参数和返回值类型。这意味着闭包作为函数或者方法的参数时，我们几乎可以不需要完整的格式就可以构造内联闭包。但是为了保证代码的可读性，建议大家采用完整格式的闭包。


**单表达式闭包隐式返回**

单行表达式闭包可以通过省略 return 关键字来隐式返回单行表达式的结果，如上版本的例子可以改写为：


```swift
let names = [40, 27, 63, 15, 75, 42]
let sortedNames = names.sorted(by: {a, b in a > b })
print(sortedNames)
```

在这个例子中，sorted(by:) 方法的参数类型明确了闭包必须返回一个 Bool 类型值。因为闭包函数体只包含了一个单一表达式（a > b），该表达式返回 Bool 类型值，因此这里没有歧义，return 关键字可以省略。

**参数名称缩写**

Swift 自动为内联闭包提供了参数名称缩写功能，我们可以直接通过 `$0`，`$1`，`$2` 来顺序调用闭包的参数，以此类推。

如果我们在闭包表达式中**使用参数名称缩写**，你可以在闭包定义中**省略参数列表**，并且**对应参数名称缩写的类型会通过函数类型进行推断**。**in关键字也同样可以被省略**，因为此时闭包表达式完全由闭包函数体构成：


```swift
let names = [40, 27, 63, 15, 75, 42]
let sortedNames = names.sorted(by: { $0 > $1 })
print(sortedNames)
// $0和$1表示闭包中第一个和第二个 Int 类型的参数
```

#### 尾随闭包

当我们需要将一个很长的闭包表达式作为最后一个参数传递给函数，可以使用尾随闭包来增强函数的可读性。**尾随闭包是一个书写在函数括号之后的闭包表达式，函数支持将其作为最后一个参数调用。在使用尾随闭包时，你不用写出它的参数标签**：


```swift
func someFunctionThatTakesAClosure(closure: () -> Void) {
    // 函数体部分
}

// 以下是不使用尾随闭包进行函数调用
someFunctionThatTakesAClosure(closure: {
    // 闭包主体部分
})

// 以下是使用尾随闭包进行函数调用
someFunctionThatTakesAClosure() {
    // 闭包主体部分
}
```

所以在上一节当中的 sorted(by:) 方法参数的字符串排序闭包可以改写为：


```swift
let names = [40, 27, 63, 15, 75, 42]
let sortedNames = names.sorted(){ $0 > $1 }
print(sortedNames)
```

> **Tips:**

> 如果闭包表达式是函数或方法的唯一参数，则当你使用尾随闭包时，你甚至可以把 () 省略掉：
> 
```swift
let names = [40, 27, 63, 15, 75, 42]
let sortedNames = names.sorted{ $0 > $1 }
print(sortedNames)
```

### 结构体

Swift 结构体是构建代码所用的一种通用且灵活的构造体。我们可以为结构体**定义属性（常量、变量）和添加方法**，从而扩展结构体的功能。

结构体的特点：

* 结构体不需要包含实现文件和接口。
* 结构体允许我们创建一个单一文件，且系统会自动生成面向其它代码的外部接口。

> **Tips：**
> Swift 中类和结构体有很多共同点。共同处在于：

> * 定义属性用于存储值
> * 定义方法用于提供功能
> * 定义下标操作使得可以通过下标语法来访问实例所包含的值
> * 定义构造器用于生成初始化值
> * 通过扩展以增加默认实现的功能
> * 实现协议以提供某种标准功能

#### 语法格式


```swift
struct nameStruct { 
   Definition 1
   Definition 2
   ……
   Definition N
}
```

结构体的应用

```swift
struct Cube {
    var height = 10
    var width = 10
    var length = 10
}
func getCube(aCube: Cube) {
    print(aCube.height * aCube.width * aCube.length)
}
var cube = Cube()
cube.width = 5
cube.height = 5
cube.length = 5
getCube(aCube: cube)
// 125
```

