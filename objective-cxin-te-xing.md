# Objective-C新特性

---

## Modules {#Modules}

新的编译符号`@import`

```
@import UIKit.UIView;
```

编译优化，避免编译时重复引用，增加编译速度。

## Nullability {#Nullability}

常规用法

```
@property (nonatomic, strong, nonnull) Sark *sark;
@property (nonatomic, copy, readonly, nullable) NSArray *friends;
+ (nullable NSString *)friendWithName:(nonnull NSString *)name;
```

修饰变量，前面需要加下划线，比如 block 内用法：

```
- (void)startWithCompletionBlock:(nullable void (^)(NSError * _Nullable error))block;
```

setter 用法，参见 UIViewController 中的 view 属性，它可以被设成 nil，但是调用 getter 时会触发 -loadView 从而创建并返回一个非 nil 的 view。

```
@property (null_resettable, nonatomic, strong) UIView *view;
```

宏的用法（包在宏里面的对象默认加 `nonnull` 修饰符，只需要把 `nullable` 的指出来就行）：

```
NS_ASSUME_NONNULL_BEGIN
@interface Sark : NSObject
@property (nonatomic, copy, nullable) NSString *workingCompany;
@property (nonatomic, copy) NSArray *friends;
- (nullable NSString *)gayFriend;
@end
NS_ASSUME_NONNULL_END
```

[Nullability](https://developer.apple.com/swift/blog/?id=25) 主要作用是在编译器层面提供了空值的类型检查，在类型不符时给出 warning，方便开发者第一时间发现潜在问题。

* 对于属性、方法返回值、方法参数的修饰，使用：`nonnull`/`nullable`；
* 对于 C 函数的参数、Block 的参数、Block 返回值的修饰，使用：`_Nonnull`/`_Nullable`，建议弃用~~ `__nonnull`/`__nullable`~~；
* 通过 `typedef` 定义的类型的 nullability 特性通常依赖于上下文，即使是在 Audited Regions 中，也不能假定它为 `nonnull`；
* 对于复杂的指针类型（如 `id *`）必须显式去指定是 `nonnull` 还是 `nullable`。例如，指定一个指向 `nullable` 对象的 `nonnull` 指针，可以使用` __nullable id * __nonnull`；
* 我们经常使用的 `NSError **` 通常是被假定为一个指向 `nullable` NSError 对象的`nullable` 指针。

## \_\_kindof {#kindof}

主要作用还是编译器层面的类型检查

```
//UIView 的写法
@property (nonatomic, readonly, copy) NSArray<__kindof UIView *> *subviews;

// UITableView 的写法
- (nullable __kindof UITableViewCell *)dequeueReusableCellWithIdentifier:(NSString *)identifier;
```

## 泛型 {#轻量级的泛型}

### 带泛型的容器 {#带泛型的容器}

```
NSArray<NSString *> *strings = @[@"sun", @"yuan"];
NSDictionary<NSString *, NSNumber *> *mapping = @{@"a": @1, @"b": @2};
```

### 自定义泛型 {#自定义泛型}

```
@interface Stack<ObjectType> : NSObject
- (void)pushObject:(ObjectType)object;
- (ObjectType)popObject;
@property (nonatomic, readonly) NSArray<ObjectType> *allObjects;
@end
```

还可以增加限制

```
// 只接受 NSNumber * 的泛型
@interface Stack<ObjectType: NSNumber *> : NSObject
// 只接受满足 NSCopying 协议的泛型
@interface Stack<ObjectType: id<NSCopying>> : NSObject
```

## covariant && contravariant

* \_\_covariant : 子类型可以强转到父类型（里氏替换原则）
* \_\_contravariant : 父类型可以强转到子类型

参考 NSArray 和 NSMutableArray 的定义

```
// NSArray
@interface NSArray<__covariant ObjectType> : NSObject <NSCopying, NSMutableCopying, NSSecureCoding, NSFastEnumeration>

@property (readonly) NSUInteger count;
- (ObjectType)objectAtIndex:(NSUInteger)index;
- (instancetype)init NS_DESIGNATED_INITIALIZER;
- (instancetype)initWithObjects:(const ObjectType [])objects count:(NSUInteger)cnt NS_DESIGNATED_INITIALIZER;
- (nullable instancetype)initWithCoder:(NSCoder *)aDecoder NS_DESIGNATED_INITIALIZER;

@end

// NSMutableArray
@interface NSMutableArray<ObjectType> : NSArray<ObjectType>

- (void)addObject:(ObjectType)anObject;
- (void)insertObject:(ObjectType)anObject atIndex:(NSUInteger)index;
- (void)removeLastObject;
- (void)removeObjectAtIndex:(NSUInteger)index;
- (void)replaceObjectAtIndex:(NSUInteger)index withObject:(ObjectType)anObject;
- (instancetype)init NS_DESIGNATED_INITIALIZER;
- (instancetype)initWithCapacity:(NSUInteger)numItems NS_DESIGNATED_INITIALIZER;
- (nullable instancetype)initWithCoder:(NSCoder *)aDecoder NS_DESIGNATED_INITIALIZER;

@end
```

## Designated Initializer {#Designated-Initializer}

Objective-C 中主要通过`NS_DESIGNATED_INITIALIZER`宏来实现指定构造器的。

参考 UIViewController 的两个指定构造器：

```
- (instancetype)initWithNibName:(nullable NSString *)nibNameOrNil bundle:(nullable NSBundle *)nibBundleOrNil NS_DESIGNATED_INITIALIZER;
- (nullable instancetype)initWithCoder:(NSCoder *)aDecoder NS_DESIGNATED_INITIALIZER;
```

类似 swift 的指定初始化，原则如下（类比 swift 的构造构成）：

* 每个类的正确初始化过程应当是按照从子类到父类的顺序，依次调用每个类的Designated Initializer。并且用父类的Designated Initializer初始化一个子类对象，也需要遵从这个过程。
* 如果子类指定了新的初始化器，那么在这个初始化器内部必须调用父类的Designated Initializer。并且需要重写父类的Designated Initializer，将其指向子类新的初始化器。
* 你可以不自定义Designated Initializer，也可以重写父类的Designated Initializer，但需要调用直接父类的Designated Initializer。
* 如果有多个Secondary initializers\(次要初始化器\)，它们之间可以任意调用，但最后必须指向Designated Initializer。在Secondary initializers内不能直接调用父类的初始化器。
* 如果有多个不同数据源的Designated Initializer，那么不同数据源下的Designated Initializer应该调用相应的\[super \(designated initializer\)\]。如果父类没有实现相应的方法，则需要根据实际情况来决定是给父类补充一个新的方法还是调用父类其他数据源的Designated Initializer。比如UIView的initWithCoder调用的是NSObject的init。
* 需要注意不同数据源下添加额外初始化动作的时机。



