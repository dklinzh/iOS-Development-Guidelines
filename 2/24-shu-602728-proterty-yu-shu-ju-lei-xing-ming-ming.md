# **属性(Property)与数据类型命名** {#属性与数据类型命名}

---

这一节描述属性，实例变量，常量，异常以及通知的命名约定。

## **属性声明与实例变量** {#属性声明与实例变量}

属性声明的命名大体上和访问方法的命名是一致的。假如属性是动词或名词，格式如下

```
@property (...) typenounOrVerb;

```

例如：

```
@property (strong) NSString *title;
@property (assign) BOOL showsAlpha;

```

如果属性是形容词，名字去掉"is"前缀，但是要特别说明一下符合规范的get访问方法，例如

```
@property (assign, getter=isEditable) BOOL editable;

```

多数情况下，你声明了一个属性，那么就会自动生成对应的实例变量。

确保实例变量名简明扼要地描述了它所代表的属性。通常，你应该使用访问方法，而不是直接访问实例变量（除了在init或者dealloc方法里）。为了便于标识实例变量，在名字前面加个下划线"\_"，例如:

```
@implementation MyClass {
    BOOL _showsTitle;
}

```

在为类添加实例变量是要注意:

* 避免创建 public 实例变量
* 使用 @private,@protected 显式限定实例变量的访问权限
* 如果实例变量别设计为可被访问的,确保编写了访问方法

## **常量** {#常量}

常量命名规则根据常量创建的方式不同而大不同。

### **枚举常量** {#枚举常量}

* 使用枚举来定义一组相关的整数常量
* ￼￼￼￼枚举常量与其 typedef 命名遵守函数命名规则。如:来自 NSMatrix.h 中的例子

```
typedef enum _NSMatrixMode {
    NSRadioModeMatrix        = 0,
    NSHighlightModeMatrix    = 1,
    NSListModeMatrix         = 2,
    NSTrackModeMatrix        = 3,
} NSMatrixMode;

```

* 位掩码常量可以使用不具名枚举。如:

```
enum {
    NSBorderlessWindowMask      = 0,
    NSTitledWindowMask          = 1 
<
<
 0,
    NSClosableWindowMask        = 1 
<
<
 1,
    NSMiniaturizableWindowMask  = 1 
<
<
 2,
    NSResizableWindowMask       = 1 
<
<
 3
};

```

### **const常量** {#const常量}

* 尽量用const来修饰浮点数常数，以及彼此没有关联的整数常量（否则使用枚举）
* const常量命名范例：

```
const float NSLightGray;

```

枚举常量命名规则与函数命名规则相同。

### **其他常量** {#其他常量}

* 通常不使用 \#define 来创建常量。如上面所述，整数常量请使用枚举，浮点数常量请使用const
* 使用大写字母来定义预处理编译宏。如：

```
#ifdef  DEBUG

```

* 编译器定义的宏名首尾都有双下划线。 如：

```
__MACH__

```

* 为 notification 名及 dictionary key 定义字符串常量,从而能够利用编译器的拼写检查,减少书写错误。Cocoa 框架提供了很多这样的范例:

```
APPKIT_EXTERN NSString *NSPrintCopies;

```

实际的字符串值在实现文件中赋予。\(注意: APPKIT\_EXTERN 宏等价于 Objective-C 中 extern\)

## **异常与通知** {#异常与通知}

异常与通知的命名遵循相似的规则,但是它们有各自推荐的使用模式。

### **异常** {#异常}

虽然你可以处于任何目的而使用异常\(由 NSException 类及相关类实现\),Cocoa 通常不使用异常来处 理常规的,可预料的错误。在这些情形下,使用诸如 nil, NULL, NO 或错误代码之类的返回值。异常的典 型应用类似数组越界之类的编程错误。

异常由具有如下形式的全局 NSString 对象标识:

```
[Prefix] + UniquePartOfName + Exception

```

UniquePartOfName 部分是有连续的首字符大写的单词组成。例如:

```
NSColorListIOException
NSColorListNotEditableException
NSDraggingException
NSFontUnavailableException
NSIllegalSelectorException

```

### **通知** {#通知}

如果一个类有委托,那它的大部分通知可能由其委托的委托方法来处理。这些通知的名称应该能够反应其 响应的委托方法。比如,当应用程序提交 NSApplicationDidBecomeActiveNotification 通知时,全局 NSApplication 对象的委托会注册从而能够接收 applicaitonDidBecomeActive: 消息。

通知由具有如下形式的全局 NSString 对象标识:

```
[相关联类的名称] + [Did 或 Will] + [UniquePartOfName] + Notification

```

例如:

```
NSApplicationDidBecomeActiveNotification
NSWindowDidMiniaturizeNotification
NSTextViewDidChangeSelectionNotification
NSColorPanelColorDidChangeNotification
```



