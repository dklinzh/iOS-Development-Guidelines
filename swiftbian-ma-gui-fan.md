# Swift编码规范

---

## **正确性 \(**Correctness**\)**

把警告当做错误处理。这条规则从根本禁止了一些文法使用，如推荐使用**\#selector**文而不是用字符串\(更多请阅读[Swift 3为什么推荐使用\#selector](https://github.com/apple/swift-evolution/blob/master/proposals/0022-objc-selectors.md)\)。

## 命名 \(Naming\)

使用驼峰式的描述性命名方式，为类，方法，变量等命名。类名的首字母应该大写，而方法和变量的首字母使用小写字符。

推荐做法：

```
private let maximumWidgetCount = 100
class WidgetContainer {
  var widgetButton: UIButton
  let widgetHeightPercentage = 0.85
}
```

不推荐做法：

```
let MAX_WIDGET_COUNT = 100
class app_widgetContainer {
  var wBut: UIButton
  let wHeightPct = 0.85
}
```

缩写和简写应该要尽量避免，遵守苹果命名规范，缩写和简写中的所有字符大小写要一致。

推荐：

```
let urlString:  URLString
let userID:  UserID
```

不推荐：

```
let uRLString:  UrlString
let userId:  UserId
```

对于函数和初始化方法，推荐对所有的参数进行有意义的命名，除非上下文已经非常清楚。如果外部参数命名可以使得函数调用更加可读，也应该把外部参数命名包含在内。

```
func dateFromString(dateString: String) -> NSDate
func convertPointAt(#column: Int, #row: Int) -> CGPoint
func timedAction(#delay: NSTimeInterval, perform action: SKAction) -> SKAction!
// 调用方式如下：
dateFromString("2014-03-14")
convertPointAt(column: 42, row: 13)
timedAction(delay: 1.0, perform: someOtherAction)
```

对于方法来说，参照标准的苹果惯例，方法命名含义要引用到第一个参数：

```
class Guideline {
  func combineWithString(incoming: String, options: Dictionary?) { ... }
  func upvoteBy(amount: Int) { ... }
}
```

### 协议 \(Protocol\)

根据苹果接口设计指导准则，协议名称用来描述一些东西是什么的时候是名词，例如：Collection,WidgetFactory。若协议名称用来描述能力应该以-ing, -able, 或 -ible结尾，例如：Equatable,Resizing。

### 枚举 \(Enumerations\)

使用首字母大写的驼峰命名规则来命名枚举值：

```
enum Shape {
  case Rectangle
  case Square
  case Triangle
  case Circle
}
```

### 文章 \(Prose\)

当我们在文章中（教程，图书，注释等）需要引用到函数时，需要从调用者的视角考虑，包含必要的参数命名，或者使用`_`表示不需要命名的参数。

> 从你自身实现的`init`中调用`convertPointAt(column:row:)`。
>
> 如果你调用`dateFromString(_:)`,需要保证你提供的输入字符串格式是”yyyy-MM-dd”。
>
> 如果你需要在`viewDidLoad()`中调用`timedAction(delay:perform:)`，记得提供调整后的延迟值和需要处理的动作。
>
> 你不能直接调用数据源方法`tableView(_:cellForRowAtIndexPath:)`

当你遇到疑问时，可以看看Xcode在`jump bar`中是如何列出方法名的 —— 我们的风格与此匹配。

[![](https://github.com/raywenderlich/swift-style-guide/raw/master/screens/xcode-jump-bar.png "Methods in Xcode jump bar")](https://github.com/raywenderlich/swift-style-guide/raw/master/screens/xcode-jump-bar.png)

Methods in Xcode jump bar

### 类的前缀 \(Class Prefixes\)

Swift类型自动被模块名设置了名称空间，所以你不需要加一个类的前缀。如果两个来自不同模块的命名冲突了，你可以附加一个模块名到类型命名的前面来消除冲突。

```
import SomeModule
let myClass = MyModule.UsefulClass()
```

### **委托 \(Delegate\)**

在定义委托方法时，第一个未命名参数应是委托数据源。 \([为了保持参数声明的一致性](https://github.com/apple/swift-evolution/blob/e4328889a9643100177aef19f6f428855c5d0cf2/proposals/0046-first-label.md)[在Swift3引入的](https://github.com/apple/swift-evolution/blob/e4328889a9643100177aef19f6f428855c5d0cf2/proposals/0046-first-label.md)\)

推荐：

```
func namePickerView(_ namePickerView: NamePickerView, didSelectName name: String)
func namePickerViewShouldReload(_ namePickerView: NamePickerView) -> Bool
```

不推荐：

```
func didSelectName(namePicker: NamePickerViewController, name: String)
func namePickerShouldReload() -> Bool
```

### 泛型 \(Generics\)

泛型类参数应具有描述性，遵守“大骆驼命名法”。如果一个参数名没有具体的含义，可以使用传统单大写字符，如T,  U, 或V等。

推荐：

```
struct Stack<Element> { ... }
func write<Target: OutputStream>(to target: inout Target)
func swap<T>(_ a: inout T, _ b: inout T)
```

不推荐：

```
struct Stack<T> { ... }
func write<target: OutputStream>(to target: inout target)
func swap<Thing>(_ a: inout Thing, _ b: inout Thing)
```

### 语言 \(Language\)

使用美式英语拼音符合Apple API的标准。

推荐做法：

```
let color = "red"
```

不推荐做法：

```
let colour = "red"
```

## 代码组织结构 \(Code Organization\)

使用`extension`来组织你的功能逻辑块中的代码结构。每个extension应该使用注释`// MARK: -`分割以保持代码的良好组织。

### 协议遵守（Protocol Conformance）

当我们对一个类添加协议时，推荐使用一个单独的类扩展`extension`来实现协议的方法。这可以保持协议相关的方法聚合在一起，同时也可以简单的标识出一个协议对应类中需要实现哪些对应的方法。

同时，别忘了添加`// MARK:`，注释可以使得代码组织的更好！

推荐做法：

```
class MyViewcontroller: UIViewController {
  // class stuff here
}
// MARK: - UITableViewDataSource
extension MyViewcontroller: UITableViewDataSource {
  // table view data source methods
}
// MARK: - UIScrollViewDelegate
extension MyViewcontroller: UIScrollViewDelegate {
  // scroll view delegate methods
}
```

不推荐做法：

```
class MyViewcontroller: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
  // all methods
}
```

对于UIKit view controllers, 建议用`extension`定义不同的类，按照生命周期，自定义方法，IBAction分组。

### **无用代码 \(**Unused Code**\)**

无用的代码，包括Xcode生成的模板代码和占位符注释应该删除，除非是有目的性的保留这些代码。

一些方法内只是简单地调用了父类里面的方法也需要删除，包括UIApplicationDelegate内的空方法和无用方法。

推荐：

```
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  return Database.contacts.count
}
```

不推荐：

```
override func didReceiveMemoryWarning() {
  super.didReceiveMemoryWarning()
  // Dispose of any resources that can be recreated.
}

override func numberOfSections(in tableView: UITableView) -> Int {
  // #warning Incomplete implementation, return the number of sections
  return 1
}

override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  // #warning Incomplete implementation, return the number of rows
  return Database.contacts.count
}
```

### 最少引入\(Minimal Imports\)

减少不必要的引入，例如引入`Foundation`能满足的情况下不用引入`UIKit`。

## 空格（Spacing）

* 使用2个空格的缩进比使用tabs更好，可以减少占用空间和帮助防止多次换行。确保在Xcode进行了下图的偏好设置：

[![](https://github.com/raywenderlich/swift-style-guide/raw/master/screens/indentation.png "Xcode indent settings")](https://github.com/raywenderlich/swift-style-guide/raw/master/screens/indentation.png)

Xcode indent settings

* 方法定义的大括号或者其他大括号（`if`/`else`/`switch`/`while`等）—— 般都放在定义名称的同一行，并且使用一个新的行来结束。

* 提示：你可以通过以下方法重新进行缩进：选择一些代码（或者使用⌘A选择所有），然后按Control-I\(或者点击菜单栏 Editor\Structure\Re-Indent）。一些Xcode模板代码使用的缩进是4个空格，所以这种方法可以很好的修复缩进。

推荐做法：

```
if user.isHappy {
  // Do something
} else {
  // Do something else
}
```

不推荐做法：

```
if user.isHappy
{
    // Do something
}
else {
    // Do something else
}
```

* 应该在方法之间空出一行，从视觉上有更好的区分和组织。方法内的空白行隔开不同的功能，但是当一个方法中有很多段落时，也意味着你应该将该方法重构成几个方法。
* 冒号总是在左边不留空格，右边留有一个空格。例外有三元运算符`? :`, 空字典`[:]`和`#selector`语法中的未命名参数`(_:)`

推荐：

```
class TestDatabase: Database {
  var data: [String: CGFloat] = ["A": 1.2, "B": 3.2]
}
```

不推荐：

```
class TestDatabase : Database {
  var data :[String:CGFloat] = ["A" : 1.2, "B":3.2]
}
```

* 长行代码应该被限制在大约70个字符内。硬限制是故意不指定的。

* 避免在每行的末尾留有空格。

* 在每个文件的末尾添加一个换行符。

## 注释（Comments）

当你需要时，使用注释来解释一段特定的代码段的作用。注释必须保证更新或者及时删除。

避免在代码中使用块注释，代码尽可能自己能表达含义。以下情况除外：当使用注释来生成文档时。

## 类和结构体（Classes and Structures）

### 选择使用谁？（Which one to use?）

请记住，结构体是[值类型](https://developer.apple.com/library/mac/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13-XID_144)。使用结构体并没有一个标识。一个数组包含`[a, b, c]`和另外一个数组同样包含`[a, b, c]`是完全一样的，它们完全可以交换使用。使用第一个还是使用第二个无关紧要，因为它们代表的是同一个东西。这就是为什么数组是结构体。

类是[引用类型](https://developer.apple.com/library/mac/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13-XID_145)。使用类是有一个标识或者有一个特定的生命周期。你需要对一个人类建模为一个类，因为两个不同的人的实例，是两个不同的东西。只是因为两个人有同样的名字和生日，也不能断定这两个人是一样的。但是人的生日是一个结构体，因为日期1950-3-3和另外一个日期1950-3-3是相同的。日期不需要一个标识。

有时，一些事物应该定义为结构体，但是需要兼容AnyObject或者已经在以前的历史版本中定义为类（`NSDate`，`NSSet`）。尽可能的尝试遵守这些规则。

### 定义的案例（Example definition）

以下是一个风格很好的类定义:

```
class Circle: Shape {
  var x: Int, y: Int
  var radius: Double
  var diameter: Double {
    get {
      return radius * 2
    }
    set {
      radius = newValue / 2
    }
  }
  init(x: Int, y: Int, radius: Double) {
    self.x = x
    self.y = y
    self.radius = radius
  }
  convenience init(x: Int, y: Int, diameter: Double) {
    self.init(x: x, y: y, radius: diameter / 2)
  }
  func describe() -> String {
    return "I am a circle at \(centerString()) with an area of \(computeArea())"
  }
  override func computeArea() -> Double {
    return M_PI * radius * radius
  }
  private func centerString() -> String {
    return "(\(x),\(y))"
  }
}
```

以上例子遵循了以下风格规范：

* 指定属性，变量，常量，参数定义或者其他定义的类型，在冒号后面，紧跟着一个空格，而不是把空格放在冒号前面。比如：`x: Int`和`Circle: Shape`。
* 如果能表示相同的目的和上下文，可以在同一行定义多个变量和结构体。
* 缩进getter，setter的定义和属性观察器的定义。
* 不需要添加`internal`这样的默认的修饰符。同样的，不需要在重写一个方法时添加访问修饰符。
* 在扩展中组织额外的功能（如打印）
* 隐藏非共享的实现细节，如在扩展中的`centerString`内使用`private`访问控制。

### Self的使用（Use of Self）

为了保持简洁，避免使用 self 关键词，Swift 不需要使用`self`来访问对象属性和调用对象方法。

必须使用`self`来区分构造器中属性命名和参数命名，还有在闭包表达式中引用属性值\(编译器需要区分\):

```
class BoardLocation {
  let row: Int, column: Int
  init(row: Int, column: Int) {
    self.row = row
    self.column = column
    let closure = {
      println(self.row)
    }
  }
}
```

### 计算属性（Computed Properties）

为了保持简洁，如果一个计算属性是只读的，请忽略掉get语句。只有在需要定义set语句的时候，才提供get语句。

推荐做法：

```
var diameter: Double {
  return radius * 2
}
```

不推荐做法：

```
var diameter: Double {
  get {
    return radius * 2
  }
}
```

### Final

给那些不打算被继承的类使用`final` 修饰符， 例如:

```
// Turn any generic type into a reference type using this Box class.
final class Box<T> {
  let value: T
  init(_ value: T) {
    self.value = value
  }
}
```

## 函数声明（Function Declarations）

保证短的函数定义在同一行中，并且包含左大括号：

```
func reticulateSplines(spline: [Double]) -> Bool {
  // reticulate code goes here
}
```

在一个长的函数定义时，在适当的地方进行换行，同时在下一行中添加一个额外的缩进：

```
func reticulateSplines(spline: [Double], adjustmentFactor: Double,
    translateConstant: Int, comment: String) -> Bool {
  // reticulate code goes here
}
```

## 闭包表达式（Closure Expressions）

如果闭包表达式参数在参数列表中的最后一个时，使用尾部闭包表达式。给定闭包参数一个描述性的命名。

推荐做法：

```
UIView.animateWithDuration(1.0) {
  self.myView.alpha = 0
}
UIView.animateWithDuration(1.0,
  animations: {
    self.myView.alpha = 0
  },
  completion: { finished in
    self.myView.removeFromSuperview()
  }
)
```

不推荐做法：

```
UIView.animateWithDuration(1.0, animations: {
  self.myView.alpha = 0
})
UIView.animateWithDuration(1.0,
  animations: {
    self.myView.alpha = 0
  }) { f in
    self.myView.removeFromSuperview()
}
```

当单个闭包表达式上下文清晰时，使用隐式的返回值：

```
attendeeList.sort { a, b in
  a > b
}
```

链式方法使用尾随闭包会更清晰易读，至于如何使用空格，换行，还是使用命名和匿名参数不做具体要求。

```
let value = numbers.map { $0 * 2 }.filter { $0 % 3 == 0 }.index(of: 90)

let value = numbers
  .map {$0 * 2}
  .filter {$0 > 50}
  .map {$0 + 10}
```

## 类型（Types）

尽可能使用 Swift 原生类型。Swift 提供到 Objective-C 类型的桥接，所以你仍然可以使用许多需要的方法。

推荐做法：

```
let width = 120.0                                    // Double
let widthString = (width as NSNumber).stringValue    // String
```

不推荐做法：

```
let width: NSNumber = 120.0                          // NSNumber
let widthString: NSString = width.stringValue        // NSString
```

在 Sprite Kit 代码中，使用 `CGFloat` 可以使得代码更加简明，避免很多转换。

### 常量（Constants）

常量定义使用`let`关键字，变量定义使用`var`关键字，如果变量的值不需要改变，请尽量使用`let`关键字。

提示：一个好的技巧是，使用`let`定义任何东西，只有在编译器告诉我们值需要改变的时候才改成`var`定义。

你可以使用[类型属性](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Properties.html)来定义类型常量而不是实例常量，使用static let 可以定义类型属性常量。 这样方式定义类别属性整体上优于全局常量，因为更容易区分于实例属性. 比如:

推荐：

```
enum Math {
  static let e = 2.718281828459045235360287
  static let root2 = 1.41421356237309504880168872
}

let hypotenuse = side * Math.root2
```

注意**: **使用枚举的好处是变量不会被无意初始化，且全局有效。  
不推荐：

```
let e = 2.718281828459045235360287  // pollutes global namespace
let root2 = 1.41421356237309504880168872

let hypotenuse = side * root2 // what is root2?
```

### 静态方法和变量[类型属性](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Properties.html) \(Static Methods and Variable Type Properties\)

静态方法和类型属性的工作原理类似于全局方法和全局属性，应该节制使用。它们的使用场景在于如果某些功能局限于特别的类型或和Objective-C 互相调用。

### 可选类型（Optionals）

当nil值是可以接受的时候时，定义变量和函数返回值为可选类型`?`。

当你确认变量在使用前已经被初始化时，使用`!`来显式的拆包类型，比如在`viewDidLoad`中会初始化`subviews`。

当你访问一个可选值时，如果只需要访问一次或者在可选值链中有多个可选值时，请使用可选值链：

```
self.textContainer?.textLabel?.setNeedsDisplay()
```

当需要很方便的一次性拆包或者添加附加的操作时，请使用可选值绑定：

```
if let textContainer = self.textContainer {
  // do many things with textContainer
}
```

当我们命名一个可选变量和属性时，避免使用诸如`optionalString`和`maybeView`这样的命名，因为可选值的表达已经在类型定义中了。

在可选值绑定中，直接映射原始的命名比使用诸如`unwrappedView`和`actualLabel`要好。

推荐做法：

```
var subview: UIView?
var volume: Double?
// later on...
if let subview = subview, volume = volume {
  // do something with unwrapped subview and volume
}
```

不推荐做法：

```
var optionalSubview: UIView?
var volume: Double?
if let unwrappedSubview = optionalSubview {
  if let realVolume = volume {
    // do something with unwrappedSubview and realVolume
  }
}
```

### **延迟初始化 \(**Lazy Initialization**\)**

延迟初始化用来细致地控制对象的生命周期，这对于想实现延迟加载视图的UIViewController特别有用，你可以使用即时被调用闭包或私有构造方法：

```
lazy var locationManager: CLLocationManager = self.makeLocationManager()

private func makeLocationManager() -> CLLocationManager {
  let manager = CLLocationManager()
  manager.desiredAccuracy = kCLLocationAccuracyBest
  manager.delegate = self
  manager.requestAlwaysAuthorization()
  return manager
}
```

**注意**：

* `[unowned self]`在这里不是必须的，应为没有产生引用循环。

* Location manager 的负面效果会弹出对话框要求用户提供权限，这是做延时加载的原因。

### 结构体构造器（Struct Initializers）

使用原生的 Swift 结构体构造器，比老式的几何类（CGGeometry）的构造器要好。

推荐做法：

```
let bounds = CGRect(x: 40, y: 20, width: 120, height: 80)
let centerPoint = CGPoint(x: 96, y: 42)
```

不推荐做法：

```
let bounds = CGRectMake(40, 20, 120, 80)
let centerPoint = CGPointMake(96, 42)
```

推荐使用结构体限定的常量`CGRect.infiniteRect`,`CGRect.nullRect`等，来替代全局常量`CGRectInfinite`,`CGRectNull`等。对于已经存在的变量，可以直接简写成`.zeroRect`。

### 类型推断（Type Inference）

推荐使用更加紧凑的代码，让编译器能够推断出常量和变量的类型。除非你需要定义一个特定的类型\(比如`CGFloat`和`Int16`\)，而不是默认的类型。

推荐做法：

```
let message = "Click the button"
let currentBounds = computeViewBounds()
var names = ["Mic", "Sam", "Christine"]
let maximumWidth: CGFloat = 106.5
```

不推荐做法：

```
let message: String = "Click the button"
let currentBounds: CGRect = computeViewBounds()
let names = [String]()
```

#### **类型注解对空的数组和字典**

对空的数据和字典，使用类型注解。

推荐：

```
var names: [String] = []
var lookup: [String: Int] = [:]
```

不推荐：

```
var names = [String]()
var lookup = [String: Int]()
```

**注意：**遵守这条规则意味选择描述性命名比之前变得更加重要。

### 语法糖（Syntactic Sugar）

推荐使用类型定义简洁的版本，而不是全称通用语法。

推荐做法：

```
var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?
```

不推荐做法：

```
var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>
```

## 函数 vs 方法 \(Functions vs Methods\)

自由函数不依附于任何类或类型，应该节制地使用。如果可能优先使用方法而不是自由函数，这有助于代码的可读性和易发现性。

自由函数使用的场景是当不确定它和具体的类别或实例相关。

推荐:

```
let sorted = items.mergeSorted()  // easily discoverable
rocket.launch()  // acts on the model
```

不推荐：

```
let sorted = mergeSort(items)  // hard to discover
launch(&rocket)
```

自由函数：

```
let tuples = zip(a, b)  // feels natural as a free function (symmetry)
let value = max(x, y, z)  // another free function that feels natural
```

## 内存管理 \(Memory Management\)

代码应避免指针循环引用，分析对象图谱，使用弱引用`weak`和未知引用`unowned`避免强引用循环。另外使用值类型\(`struct`和`enum`\)可以避免循环引用。

#### 延长对象生命周期

延长对象生命周期习惯上使用`[weak self]` 和 `guard let strongSelf = self else { return }`。 `[weak self]`优于`[unowned self]`因为前者更更能明显地`self` 生命周期长于闭包块。 显式延长生命周期优先于可选性拆包。

**推荐：**

```
resource.request().onComplete { [weak self] response in
  guard let strongSelf = self else {
    return
  }
  let model = strongSelf.updateModel(response)
  strongSelf.updateUI(model)
}
```

不推荐：

```
// might crash if self is released before response returns
resource.request().onComplete { [unowned self] response in
  let model = self.updateModel(response)
  self.updateUI(model)
}
```

不推荐：

```
// deallocate could happen between updating the model and updating UI
resource.request().onComplete { [weak self] response in
  let model = self?.updateModel(response)
  self?.updateUI(model)
}
```

## 访问控制 \(Access Control\)

合理的使用`private` 和  `fileprivate`, 推荐使用`private`，在使用`extension`时可使用`fileprivate`。

访问控制符一般放在属性修饰符的最前面. 除非需要使用 `static` 修饰符 ,`@IBAction`,  `@IBOutlet`或 `@discardableResult`。

推荐：

```
private let message = "Great Scott!"

class TimeMachine {  
  fileprivate dynamic lazy var fluxCapacitor = FluxCapacitor()
}
```

不推荐：

```
fileprivate let message = "Great Scott!"

class TimeMachine {  
  lazy dynamic fileprivate var fluxCapacitor = FluxCapacitor()
}
```

## 控制流（Control Flow）

推荐循环使用`for-in`表达式，而不使用`for-condition-increment`表达式。

推荐做法：

```
for _ in 0..<3 {
  print("Hello three times")
}

for (index, person) in attendeeList.enumerated() {
  print("\(person) is at position #\(index)")
}

for index in stride(from: 0, to: items.count, by: 2) {
  print(index)
}

for index in (0...3).reversed() {
  print(index)
}
```

不推荐做法：

```
var i = 0
while i < 3 {
  print("Hello three times")
  i += 1
}


var i = 0
while i < attendeeList.count {
  let person = attendeeList[i]
  print("\(person) is at position #\(i)")
  i += 1
}
```

## **黄金路径 \(**Golden Path**\)**

当编码遇到条件判断时，左边的距离是黄金路径或幸福路径，因为路径越短，速度越快。不要嵌套`if`循环，多个返回语句是可以的。`guard` 就为此而生的。  
推荐：

```
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {

  guard let context = context else {
    throw FFTError.noContext
  }
  guard let inputData = inputData else {
    throw FFTError.noInputData
  }

  // use context and input to compute the frequencies
  return frequencies
}
```

不推荐：

```
func computeFFT(context: Context?, inputData: InputData?) throws -> Frequencies {

  if let context = context {
    if let inputData = inputData {
      // use context and input to compute the frequencies

      return frequencies
    } else {
      throw FFTError.noInputData
    }
  } else {
    throw FFTError.noContext
  }
}
```

当有多个条件需要用 `guard` 或 `if let` 解包，可用复合语句避免嵌套。

推荐：

```
guard let number1 = number1,
      let number2 = number2,
      let number3 = number3 else {
  fatalError("impossible")
}
// do something with numbers
```

不推荐：

```
if let number1 = number1 {
  if let number2 = number2 {
    if let number3 = number3 {
      // do something with numbers
    } else {
      fatalError("impossible")
    }
  } else {
    fatalError("impossible")
  }
} else {
  fatalError("impossible")
}
```

#### 失败防护

防护语句的退出有很多方式，一般都是单行语句，如 `return`,`throw`,`break`,`continue` 和`fatalError()`等。 避免出现大的代码块，如果清理代码需要多个退出点，可以用 `defer` 模块避免重复清理代码。

## 分号（Semicolons）

Swift 不需要在你代码中的每一句表达式之后添加分号。只有在你需要在一行中连接多个表达式中，使用分号来区隔。

不要在同一行编写多个使用分号区隔的表达式。

唯一的例外是在使用`for-conditional-increment`架构。然而，尽可能使用`for-in`架构来替代它。

推荐做法：

```
let swift = "not a scripting language"
```

不推荐做法：

```
let swift = "not a scripting language";
```

注意：Swift与JavaScript有很大的不同，JavaScript认为忽略分号通常认为是[不安全](http://stackoverflow.com/questions/444080/do-you-recommend-using-semicolons-after-every-statement-in-javascript)的。

## 圆括号 \(Parentheses\)

条件判断时圆括号不是必须的，建议省略。

推荐：

```
if name == "Hello" {
  print("World")
}
```

不推荐：

```
if (name == "Hello") {
  print("World")
}
```

在较大的表达式中，可选的括号有时可以使代码更清楚。

```
let playerMark = (player == current ? "X" : "O")
```

## 组织和包标识符 \(Organization and Bundle Identifier\)

## 版权声明（Copyright Statement）

以下的版权声明应该被包含在所有源文件的顶部：

/\*

* Copyright \(c\) 2017 `Your Organization`
* 
* Permission is hereby granted, free of charge, to any person obtaining a copy
* of this software and associated documentation files \(the “Software”\), to deal
* in the Software without restriction, including without limitation the rights
* to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
* copies of the Software, and to permit persons to whom the Software is
* furnished to do so, subject to the following conditions:
* 
* The above copyright notice and this permission notice shall be included in
* all copies or substantial portions of the Software.
* 
* THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
* IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
* FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
* AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
* LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
* OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
* THE SOFTWARE.

\*/

