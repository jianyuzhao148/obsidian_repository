- 标量
    
    int +/ 2 147 483 647 之间的整数值 unsigned int 0 和 4 294 967 296 之间的整数值 float +/ 16 777 216 之间的浮点值 double +/ 2 147 483 647 之间的浮点值 long 根据芯片架构的不同，大小从 32 位到 64 位的整数值 long long 64 位整数 char 单个字符，通过整型表示 BOOL 布尔值，YES 或者 NO NSInteger 在 32 位机器上，和 int 相同。在 64 位机器上，范围是+/ 4 294 967 296 NSUInteger 在 32 位机器上和 unsigned int 相同
    
- 方法定义
    
    - (方法的数据返回类型) 方法名: (参数1数据类型) 参数1的变量名 参数2名称: (参数2数据类型) 参数2的变量名
    - 方法名等于参数1名称
- 变量命名
    
    - 指向实例的变量使用“骆驼拼写法（ camel case ）”来命名。以小写字母开头，接下来每个单词的第一个字母使用大写，如 now 、 weightLifter 、 myCurrentLocationo
    - 方法的命名也使用骆驼拼写法，如 date 、b0dyMassIndextimeIntervatSince19700
    - 类的名称以大写字母开头，但是接下来的单词继续使用骆驼拼写法： NSDate 、 person 、CLLocation 、 NSMutabteArrayo。一般来说，类的名称是要加前缀的，这样可以避免相似的类名造成的混乱。通过前缀还可以知道它属于哪个框架。 Foundati on 框架的类使用 NS 前缀，如NSDate 、 NSLoge NS 是 NEXTSTEP 的简写， Foundation 框架就是基于这个平台构建出来的。
    - 很多苹果创建的类型和常量的命名也是前缀大写的骆驼拼写法。例如， NSInteger并不是类，它只是一种整形类型。 NSOKButton 只是一个等于 1 的常量。
    - 按照惯例，实例变量(类的属性)的名字是以下划线”_”开始的。使用下划线的前缀，可以很容易分辨实例变量和局部变量。下划线没有特别的语法意义，它只是实例变量名字的第一个字符。
- #import和#include区别
    
    - #import 指令导入更快更有效率。 #include 指令告诉编译器做呆板的复制粘贴，将包含的内容粘贴到目标文件中来。而 #import 指令则会让编辑器先检查之前是否己经导入过这个文件，或是己经被包含到目标文件中了。
- 消息发送指令
    
    - 必须写在一对方括号中，必须包含接收方和选择器
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/cce47430-8724-46ed-9569-fbb4e13c0528/f2476d66-e2c8-470f-9e0f-1d9a0bc8e7ce/Untitled.png)
    
- 占位符
    
    - %@：输出相应对象的”描述信息”
- alloc和init
    
    - 唯一必须以嵌套的形式连续发送消息的两个方法（alloc和init）
    - 每个类都有一个alloc类方法。它能够创建一个新对象，并返回指向该对象的指针。通过 attoc 创建出来的对象，必须经过初始化才能使用。如果创建出来的新对象没有经过初始化，它会存在于内存里，但是无法接收消息。
    - 每个类也都有一个 init 实例方法。它用来初始化实例。
- 不指向任何对象的指针（值为0的指针）：nil
    
    - 可通过== nil / == 0 判断 nil
    - OC允许向nil发送消息，什么事情也不会发生
- id类型：可以指向任何类型的OC对象指针（注意：用id声明指针时不使用星号，id已经隐含了新号的作用）
    
- NSString
    
    - 以下代码是OC的一个缩写，代表根据给定字符串创建一个NSString对象。称为字面量语法，创建的实例为NSString的字面量实例，或者称为NSString字面量
    
    ```objectivec
    @"..."
    ```
    
    - NSString 实例可以保存任意 Unicode 字符。如果需要插入非 ASCII 码字符，则可以使用\ u ，后面加上该字符的十六进制 Unicode 编码。例如，卡牌中的红心符号的十六进制Unicode 编码是 0x2661 ：
    
    ```objectivec
    NSString *slogan = @"I \\u2661 New York!";
    ```
    
    - 使用stringWithFormat:类方法创建动态字符串
    
    ```objectivec
    NSString *dateString =[NSString stringWithFormat:@"The date is "nowl];
    ```
    
    - 常用方法
        - -(NSUInteger)length; 获取字符串中字符的数量
        - -(BOOL)isEqualToString:(NSString *)other; 判断两个字符串是否相等
        - -(NSString *)uppercaseString; 把一个字符串变成大写形式
- NSArray
    
    - 创建NSArray
    
    ```objectivec
    NSArray *defaultList = [NSArray arrayWithObjects:now,now2,now3];
    ```
    
    - NSArray 也可以用字面量语法创建实例。数组的内容写在方括号里，使用逗号分隔，前方带有@符 号，不必另外发送创建实例的消息。
    
    ```objectivec
    NSArray *list = @[obj1,obj2,obj3];
    ```
    
    - NSArray 的实例是无法改变的。一旦 NSArray 实例被创建后，就无法添加或删除数组里的指针，也无法改变数组的指针顺序。
    - 只能存放OC对象
    - 除了普通for遍历外还支持快速枚举
    
    ```objectivec
    for(NSDate *d in dateList){
            NSLog(@"2Here is a date:%@",d);
    }
    ```
    
- NSMutableArray
    
    ```objectivec
    NSMutableArray *mArray = [NSMutableArray array];//初始化空数组
    [mArray addObject:now];//添加对象
    [mArray insertObject:now3 atIndex:0];//在指定位置添加对象
    [mArray removeObjectAtIndex:0];//删除指定位置对象
    ```
    
    - NSMutabteArray 实例和 NSArray 实例类似，但是可以添加、删除或对指针重新进仃排序。 (NSMutabteArray 是 NSArray 的子类）
    - 使用快速枚举遍历 NSMutabteArray 时，不能在枚举过程中增加或者删除数组中的指针。如果遍历时需要添加或删除指针，则需要使用标准的 for 循环。
- 类
    
    - 头文件以@interface开始，以@end 结束
    - 注意要在花括号里面声明实例变量。
    - 为什么要在 Foundation.h 外加上尖括号，在 BNRPerson.h 外加上双引号呢？尖括号告诉编译器 Foundation/Foundation.h 可以在苹果库中找到，是预编译的头文件。双引号则告诉编译器在当前的项目中寻找 BNRPerson.h文件。
    - 属性存取方法
        - 为BNRPerson声明存取方法的时候，需要遵循Objective-C语言的命名规范。取方法的名字和相应的实例变量一样，但要去掉实例变量开头的下划线。
        - 存方法以set开头，后跟上去掉下划线的实例变量名。需要注意的是，存方法的命名是采用骆驼拼写法的。所以 set 后的第一个字母要大写。
    - Objective-C的方法都包含一个隐含的局部变量self，self是指针，指向运行当前方法的对象。当某个对象要向自己发送消息时，就需要使用 self。
    - Objective-c没有命名空间（ namespace ）。这也就是说，如果你写了一个称为 Person的类，而它连接到另一个库，其中有其他人声明的 Person 类，编译器无法区别这两个类，就会出现编译器错误。为了避免这样的名字冲突。苹果公司推荐使用三个或三个以上字母作为类的前缀，让类的名字独一无二，就不会和其他人的类名起冲突。大部分开发者都是用他们公司或项目的首字母缩写作为前缀。在本书中，我们使用 BNR （译者注： Big Nerd Ranch 的首字母缩写）作为类前缀。
    - 属性的声明 以@property 开始，然后是属性的类型和名称。声明属性的时候，编译器不仅会帮你声明存取方法，还会根据属性的声明实现存取方法。
        - 那实例变量怎么样了？虽然编译器创建了名为 heightInMeters 和weightInKilos 的实例变量，但是在代码中却没有看到这些变量，这是因为你不再需要自己写存取方法了。使用属性的时候，很少需要直接使用实例变量，你可以直接使用编译器创建的存取方法。
    - 属性的声明可以有一个或多个属性特性（ property attributes ）。属性特性会告诉编译器关于属性行为的更多信息。属性特性会显示 在@property 接口后的括号中， @propert接口语句以逗号分隔。
        - 属性要么是原子性（ atomic) ，要么是非原子性。区别和多线程运行有关
        - 有时一个类需要只读 (readonly) 属性，也就是说，该属性的值只能读取，不能改写。这样的属性需要拥有取方法但是不能有存方法。通过在属性特性处添加一个 readonly ，可以指导编译器只创建一个取方法
        - 属性要么是只读属性，要么是读/写（ readwrite) 属性。
        - 属性默认就是读/写属性
        - 属性默认就是原子性，非原子性需要声明（nonatomic）
        - 还有另外一个特性是可拷贝（ copy ）。实际上，每当声明一个指向 NSString 或 NSArray对象的属性时，都需要设置 copy 属性。
        - 编写 Objective-c 程序时，需要经常调用存取方法。苹果公司提供了一种快捷语法 — dot notation 以方便存取方法的调用。dot notation 和获取结构的成员变量使用的 notation 类似。但是有一点一定要记住，使用 dot nonation 的时候是在发送消息（需要属性有getter setting方法）。
            - mikey.wexghtInK1tos= 96; // 存
            - float w = mikey.wexghtInK1tos; // 取
- 函数、对象方法、类方法区别
    
    |函数|对象方法|类方法|
    |---|---|---|
    |void test()|-(void)test|+(void)test|
    |-|-号开头|+号开头|
    |-|类型用()括起来|类型用()括起来|
    |可以写在文件中的任意位置|声明必须写在@interface-@end之间，实现必须写在@implementation-@end之间|声明必须写在@interface-@end之间，实现必须写在@implementation-@end之间|
    |-|只能由对象来调用|只能由类来调用|
    |不可以直接访问成员变量|可以直接访问成员变量|不可以直接访问成员变量|
    
- 继承
    
    - 子类的实例可以毫无问题的代替父类的实例，因为他继承父类了所有的东西
        
    - 覆盖方法的时候只能改变方法的实现，而无法改变它的声明方式，方法的名称、返回类型及实参类型等都必须保持相同。
        
    - 覆盖一个方法的时候，子类可以利用父类的实现，而不是整个替换。使用super关键字调用父类方法
        
    - NSObject：只有一个实例变量”isa”指针：
        
        - isa 指针。任何一个对象的isa指针都会指向创建该对象的类。如果创建一个 BNRPerson 实例，那么它就是一个BNRPerson，如果创建一个NSString 实例，那么它就“是一个NSString
        - 给对象发送消息的时候，对象就会查询是否有该消息名的方法。搜索会通过 isa 指针找到该对象的类并查询“是否有名为消息名的实例方法？”如果没有，就会继续查询它的父类。依此类推，对象会沿着继承链向上查询，直到找到名为消息名的方法，或者到达继承链的顶端 (NS0bject) 为止。
        - 但是如果使用 super 指令，就等于告诉对象，“运行指定的方法，但是从对象的父类开始查找与之匹配的实现。”
    - 我们使用了格式说明符%@让对象描述自己。处理时，程序会先向相应的指针变量所指的对象发送 description 消息。description 方法会返回一个描述类实例的字符串。 description 是一个 NS0bject方法，所以所有的对象都有这个方法。默认的 NS0bject 实现会以字符串的形式返回该对象在内存上的地址。
        
    - 当类被释放时，执行dealloc方法
        
    - 类扩展(声明私有变量)
        
        - 类扩展 以@interface 开始， @end 结束。实际上，类扩展和头文件看上去很像，它们都是“接口（ interface ）”。然而，在类扩展中，和头文件中使用冒号和父类的名称不同，它使用的是一对空的括号。
            
            ```bash
            @interface BNREmployee ()
            @property (nonatomic) unsigned int officeAlarmCode;
            @end
            ```
            
    - OC的私有变量通过类扩展来实现，私有方法通过在类实现文件中编写方法，但不在类中声明实现
        
    - 子类无法获取父类的类扩展
        
    - 在类的头文件声明属性的时候，其他对象只能看到属性的存取方法:父类声明@property (nonatomic) NSMutabteArray *friends;在子类中无法使用_friends只能使用self.friends
        
- Set
    
    - NSSet 对象中的对象是无序的，所以不能通过索引来访问，只能向 NSSet 对象查询某个对象是否存在。可以使用下面的方法来查询某个对象是否存在：(BOOL) containsObject:(id)x;
    - NSSet:不可变
    - NSMutableSet:可变