# 拾遗
## 组合变量类型
### 常量指针
const char* / char const* (等价)
表明指针指向一个常量
因此指针所指向的值不能更改
但指针变量储存的地址可以改变(==也可进行 + 等操作==)
```cpp
const char* p = "ABC";
p = "CDF";//正确
p++;//正确
```
### 指针常量
char *const
指针为一个常量, 即指针变量储存的地址不能改变
```cpp
char const* p = "ABC";//不正确, 不能将const char*赋给char*
char str[] = "ABC";
char const* p = str;//正确
p++;//不正确
```
#### 常量指针常量
两种类型的组合, 即
const char* const
### 常量引用
const int&
引用关系一旦建立后无法修改, 因此不存在引用常量
常量引用表示对于常量的引用, 无法修改引用指向的值
<div id='clclzzyy'></div>

#### 常量常量指针引用
* 由于 const char* 表示常量指针, 与一般的常量 const int 结构相同, 含义不同
* 因此 char*& 表示char* 指针的引用, 但 const char*& 不具有const int相类似的含义(即char\*储存的地址不能修改)
而是表示对常量指针const char* 的引用, 类型const char* 储存的地址可以更改
* 要达到相同的效果(同一类型)需要使用char const*&
---
# 第九章
## 头文件 P247
### 头文件包含的内容
1. 函数原型
2. #define与const定义的常量
3. 结构, 类, 模板声明
4. 内联函数
### include
1. 包含头文件使用双引号
2. 头文件中使用 #ifndef(注意中间的n) #define 来避免多次包含同一个头文件
==不能忽视, 特别是hpp文件中==
## 变量作用域 P250
### 自动存储持续性
1. 默认情况
2. 代码块中声明的变量将在代码块结束后释放
3. 同名时, 局部变量将隐藏外部变量
``` cpp
int main()
{
    int a = 10;
    int b = 20;
    {
        int a = 30;
        int c = 40;

		cout <<"In Block" << endl;
        cout <<"a: " << a << endl;
		cout <<"b: " << b << endl;
		cout <<"c: " << c << endl;
	}
	cout <<"Out of Block" << endl;
	cout <<"a: " << a << endl;
	cout <<"b: " << b << endl;
	// cout <<"c: " << c << endl;
    return 0;
}
```
### 静态变量
1. 在代码块外定义(外部链接性)
能够被所有代码访问(跨CPP)
2. 在代码块外定义 加上static关键字(内部链接性)
只能在定义该变量的文件中使用
3, 在函数内定义 加上static关键字(无连接性)
只能在定义该变量的代码块中使用
#### 跨文件说明
在编译时, 各个头文件的内容会被扩展到cpp内
此处跨文件指多个同时编译的cpp
### 静态变量初始化
默认为0
### 引用声明 P255
引用来自其他cpp的全局变量时, 需要使用 extern [变量定义]
或者使用 ::[全局变量] 表示使用全局版本的变量
#### extern的说明
通常情况下, 定义一个变量将会为其分配空间
加上extern关键字后, 不会分配空间, 仅表明有此变量, 称为声明定义, 简称声明
对于函数, 函数定义中必有函数体, 因此不需要extern关键字说明函数是定义函数声明
声明可以多次, 定义只能有一次
#### 不使用extern的后果 
1. 直接使用其他文件的全局变量
由于变量未声明直接使用, 将出错
2. 不使用extern
编译器认为要定义一个新的全局变量, 由于变量名重复, 将出错
### 隐藏外部全局变量 P258
不使用外部的全局变量时, 可以用static声明作用域更小的全局变量
此时将优先使用作用域更小的全局变量(隐藏其他cpp的全局变量, 防止变量重复)
### 无连接性的静态变量
在程序启动时进行一次初始化, 之后保持不变
### 其他说明符/限定符 P260
同一个声明不能使用多个说明符(thread_local除外)
1. volatile
表明内存单元没有程序修改也可能发生改变
用于指向硬件位置的指针
2. mutable
指出类变量为const时, 有mutable的成员依然可变
(可用于类中记录调用次数的成员等)
### const说明符
全局const的连接性为内部(同static)
#### 使用内部链接的意义
由于头文件的内容会被扩展到cpp内, 在头文件中定义全局变量必然导致变量名冲突
使用static则可以避免冲突
const的连接性为内部保证const可以定义在头文件中
### 函数的连接性
默认情况下函数为外部链接性
1. 这意味着两个cpp中, 不能有同名函数
2. 可以使用extern访问其他cpp中的函数(extern可省略, 没有函数体的函数即声明)
可以使用static使函数的连接性为内部
#### 内联函数
内联函数不受此规则限制, 这表明内联函数可以定义在头文件内
## 动态存储变量 P262
### new初始化
1. 构造函数 int *p = new int (6);
2. 初始化结构/数组 int *p = new int [4] {1, 2, 3, 4};
### 定位new运算符
在 #include<new> 后, 可以指定new的位置
eg
``` cpp
#include<new>
int buffer[256];
int main()
{
	int *p = new(buffer) int[16];
}
```
此时将从buffer中分配空间给p, 由于空间为静态, 不需要delete
### delete
1. 使用new初始化的指针必须使用delete释放空间
1. 使用new\[\]初始化的指针必须使用delete\[\]释放空间
## 名称空间 P266
### 名称空间中的定义与声明
使用 namespace [名称空间] {}
表示内部的代码使用指定的名称空间
由于名称空间不能在代码块中, 因此名称空间中的变量通常为外连接性
允许多个使用相同名称空间的namespace存在
eg
``` cpp
namespace test
{
	int fun();
}
namespace test
{
	int fun()
	{
		return 1;
	}
}
```
### 全局名称空间
一般的全局变量在全局名称空间中
因此除了使用extern声明全局变量, 也可以使用::[全局变量], 直接访问全局变量
### using声明
1. 语法 using [带有名称空间的变量/函数]
2. 作用 把声明的变量加入全局/局部(代码块)区域中
eg
``` cpp
namespace test
{
	int a;
}
int main()
{
	using test::a; // 将a导入局部区域(不是全局)
	int a;// 由于a已在局部区域存在, 将出错
	return 1;
}
```
### using编译指令
1. 语法 using namespace [名称空间]
2. 将指定的名称空间包含到全局名称空间中
3. 与using声明的区别: 由于只是包含名称空间, 名称空间中的变量连接性不变
eg
``` cpp
namespace test
{
	int a;
}
int main()
{
	using namespace test; // 将a导入全局区域(不是全局)
	int a;// test::a被局部的a覆盖, 不会出错
	return 1;
}
```
### 名称空间其他特性
1. 可以对名称空间嵌套
2. 可以在名称空间中使用using
eg
``` cpp
namespace a
{
	int i;
}
namespace b
{
	using a;
}
```
此时 a::i 与 b::i 等价
3. 可以使用 namespace a = b 给命名空间起别名
4. 匿名命名空间
通过省略命名空间名称创造隐藏命名空间
此时会将此命名空间添加到当前文件的全局空间中, 但在其他文件无法访问
相当于static的内部链接性
5. 老式标准库头文件(iostream.h)没有名称空间
---
# 第十章
## 基础
### class与struct
class中默认访问控制为private
struct中默认访问控制为public
### 内联方法
除了inline, 在函数声明内定义的成员函数默认为内联函数
### 构造函数中防止参数冲突
构造函数的参数不能与类的成员变量相同, 可以使用在成员变量前加上m_前缀或_后缀, 区分成员变量
### 使用构造函数
[class] a = \[class\](参数);
[class] a(参数);
[class] a* = new \[class\](参数);
### 构析函数
1. ==如果类使用了new, 则必须定义对应的构析函数==
2. 构析函数没有参数
### 局部构析函数的调用
构析函数将在代码块结束后调用
```cpp
class a
{
	string name_;
	a(string name)
	{
		name_=name;
	}
	~a()
	{
		cout << name_ << endl;
	}
}
int main()
{
	a t1("t1");
	{
		a t2("t2")
	}
	return 0;
}
```
窗口环境中, 将输出 t2 , 因为t1的构析函数在窗口关闭后调用
### 列表初始化
[class] a = {参数};
[class] a{参数};
### const成员函数
在成员函数的定义与声明后加上const, 可以保证const的类变量可以调用此类成员函数
只要函数不修改成员, 尽量使用const成员函数
### this指针
当需要返回类本身时, 可以使用this指针(*this)
### 类数组
1. 类有默认/无参数的构造函数
同一般数组的定义
2. 类没有默认/无参数的构造函数
必须为每个元素调用构造函数
[class] arr[3] = {
	[class] (参数...),...
}
允许各个元素调用不同的构造函数
### 类作用域
在类中定义的成员函数/变量, 均在类作用域中, 需要通过::访问
### 类中的常量
不能在类声明中定义值(类声明不使用空间), 因此不能再类中直接使用const成员
可以使用static const定义类中的常量
### 类作用域中的枚举
通常情况下的枚举
```cpp
enum a{s1, s2, s3};
enum b{s1, s2, s3};
```
两种枚举类型的枚举量冲突(同一个域中不能有两个相同的枚举量)
在类作用域中的枚举
```cpp
enum class a{s1 = 1, s2 = 2, s3 = 3};
enum class b{s1 = 1, s2 = 2, s3 = 3};
class c
{
public:
	enum pub_sign{s1 = 1, s2 = 2, s3 = 3};//可以作为一种实现类常量/标识的方法
	enum {s7 = 7, s8 = 8, s1 = 1};//匿名枚举量, 此处s1=1错误, 枚举量重复
private:
	enum pri_sign{s4 = 4, s5 = 5, s6 = 6};//外部不可使用这些枚举量
}
```
将不会发生冲突/不会自动转换为int
使用枚举量时需要加上类名与作用域运算符 
定义枚举类型时使用枚举量的名称 a i = a::s1;
定义在类中的枚举类型 c::pub_sign i = c::s1(枚举量);
---
# 第十一章
## 重载运算符
1. [返回值] operator\[运算符\](参数)
2. 重载运算符中必须有自定义的类型
3. 不是所有运算符都可以重载
4. (), [], ->, = 只能作为成员函数重载
5. 重载运算符有多个值时, 参数位置不同, 对应的函数不同
eg. 已经定义 A A::operator+(int), 即A+int, 但 int+A未定义, 将会出错
如果要反转操作数顺序, 可以定义友元函数或A operator+(int i, A j){return j+i}
### 友元函数
在类定义中声明, 加上前缀friend, 是函数可以访问类的private成员
### 重载ostream的<<运算符
1. 将ostream作为第一个参数, 使用非成员函数定义
2. 要返回 &ostream(使cout<<a<<b可以连续)
## 类的类型转换
### 隐式类型转换
1. 
当类有当值的构造函数时, 将作为隐式转换的函数
eg
```
//假设A有无参数与参数为int的构造函数
A a;//使用无参数的构造函数
a = 10;//使用10参数为int的构造函数建立临时变量, 再复制给a
```
可在构造函数前使用explicit关闭此特性
2. 隐式转换的情况(需要类A, 但提供可转换的其他类型)
	1. 初始化类
	2. 赋值
	3. 传参
	4. 返回值
3. 
如果只定义了 A(double)
使用 A=100;时, 会先隐式转换100为double
### 转换函数
operator [目标转换类型]()
1. 必须是类方法
2. 不能指定返回类型(即目标类型)
3. 不能有参数
4. 当定义了多个转换函数时, 且有多种转换可能, 将会出错
eg. 如果A定义了 operator int() 与 operator double(), A a; cout << a;将出错, long b = a;也将出错(double与int均可赋给long)
5. 通过在声明中添加explicit, 可以禁止隐式转换
### 类型转换与运算符重载
重载类A的加法有两种方式
1. A A::operator+(const A &b) const;
2. friend A operator+(const A &a, const A &b);
现在要实现A与double的加法, 且可交换次序
* 当A不存在转换为double的方法时, 可以定义
A A::operator+(const double b) const;
friend A A::operator+(const double b, const A &a){a + b};
* 当A存在转换为double的方法时
A::operator double() const;
friend A operator+(const A &a, const A &b);//当参数为double时, 编译器将自动转换, 达到相同的效果
---
# 第十二章
## 复制构造函数
以指向对象的常量引用为参数的构造函数为复制构造函数
在没有定义时, 将使用默认的复制构造函数
### 调用复制构造函数
1. 类之间赋值
2. 使用其他类变量初始化类(包括new)
3. 函数按值传参与返回对象
### 默认的复制构造函数
默认构造函数将逐个赋值成员, 当成员中有指针指向动态内存时, 将直接复制指针(浅拷贝)
当类作为零时变量时, 当退出程序块时将调用构析函数, 此时极可能提前释放动态内存
### 默认的赋值运算符
默认赋值运算符 A& A::operator=(const A&)(与默认复制构造函数不同)
赋值运算符通常用在两个已经定义的类变量, 让后将其中一个赋给另一个
同样是浅拷贝, 将导致与默认复制构造函数相同的问题
与浅拷贝的不同
1. 需要返回引用(通常即赋值被赋值变量的引用*this)
2. 通常被赋值后, 被赋值变量的值将被舍弃, 需要delete, 不能直接覆盖
3. 在使用2后, 应避免将自身赋值给自身, 因为赋值前自身的值已被删除(可在赋值时建立临时变量解决)
4. 自我赋值的处理
```cpp
//自我赋值处理
if (this == &animal) {
    return *this;
}
```
### 类使用动态内存时注意
1. 当类使用动态内存时, 必须定义一个显式复制构造函数, 为新类分配新空间并复制动态内存的内容(深拷贝)
2. 统一使用new/delete与new\[\]/delete\[\]
	由于只有一个构析函数, 一般只能用一种delete, 因此最好也只是用一种new, 保证配对
3. 默认构造函数中, 指针一定要置空(NULL与nullptr均可), 保证正常delete
4. 当类使用动态内存时, 必须显式重载=
### 静态成员函数
1. 静态成员函数不能使用类变量调用
2. 不能使用this
3. 只能访问静态成员
## 返回值设计
### const 引用
1. 效率最高
2. 不能返回函数内的局部变量的引用
### 引用
1. << 与 >>, 配合cout, 使效率最高
2. =, 实现连续赋值
3. [], 模拟数组
### 对象
需要返回局部变量时使用
例如 + 运算符等
### const 对象
如果重载 + 运算符仅返回对象时, 以下语法将通过
``` cpp
if(A1 + A2 = A3);//将A3赋给加法运算返回的临时变量中
``` 
## 初始化列表
```cpp
Classy::Classy(int n, int m):men1(n), men2(m), arr{n, m}
{...}
```
1. 只能用于构造函数
2. 必须使用初始化列表初始化const成员
3. 必须使用初始化列表初始化引用成员
4. 初始化顺序与类中声明的顺序相同
5. 必须使用初始化列表初始化没有默认构造函数的类成员
---
# 第十三章
## 派生类
### 特性
Class Child : public Base{};
派生类具有特征
1. 储存基类的成员
2. 可以使用基类的方法
### 构造函数
派生类不能访问基类的私有成员, 只能通过基类的方法访问
因此派生类必须使用基类的构造函数
当使用基类的非默认构造函数时, 需要在初始化列表中调用
Child::Child(int i, int j): Base(i), men(j){};
### 构造与构析顺序
先构造基类, 最后构析基类
### 基类与派生类的关系
1. 将派生类的地址赋给基类指针, 可以使用此指针调用基类函数
2. 以基类引用/指针为参数的函数, 可以使用派生类
3. 可以使用派生类初始化基类变量(隐式调用复制构造函数const&)
4. 可以将派生类的变量赋给基类变量
### 公有继承的使用
用于is-a关系
### 不能被继承的成员
构造函数, 构析函数, ==operator\===, 不会被派生类继承, 需要重新定义
## 多态继承
### 虚函数
在基类与派生类(不一定)的有关函数声明前加上virtual关键字, 表明函数为虚函数
使用指针/引用调用成员函数时, 程序将根据指向的类的类型选择调用的函数
### 调用基类的方法
1. 在派生类的函数定义中
由于虚函数的特性, 直接调用虚成员函数, 不能明确是基类或派生类的
如果要在派生类中调用基类的方法, 可以使用在方法前加上基类的域解析运算符 eg.Base::function()
调用基类的重载运算符需要显式使用Base::operator=(..)
在为派生类定义新的operator=与复制构造函数等时, 不要忘记先调用基类的对应函数(不会自动调用)
2. 派生类的友元函数
在派生类中调用基类的方法只可通过强制类型转换, 且不可访问基类的private成员
eg. (Base &) A.baseFun();
### 虚函数注意
1. 基类的构析函数必须为虚函数
当基类的指针指向派生类时, 如果构析函数不是虚函数, 将导致不能调用相应的构析函数, 导致内存泄漏
2. 构造函数没有虚函数, 没意义
3. 定义一个与基类相同的函数不是重载, 而是重新定义, 将隐藏基类的函数(包括虚函数)(即直接使用派生类无法调用这些函数, 只能使用派生类的指针/引用调用)
	1. 定义的虚函数应与基类参数应相同, 返回值也要相同, (不用于参数)例外 如果返回基类函数返回基类引用/指针, 则派生类函数可返回派生类(可以被转换)
	2. 如果基类被重载, 派生类应重新定义基类所有的版本
4. 虚函数未定义则使用最新版本的函数(多重继承中)
### protected成员
在公有继承后, 派生类不能访问基类的private成员, 但可以访问protected成员
可通过protect方法来为派生类提供操作基类的private成员的方法, 并保证不被直接修改
### 纯虚函数
基类作为高度抽象的共性, 可能无法表明对特定函数的功能, 只能说明有着一种共性
此时可以通过定义纯虚函数, 表明基类为抽象基类, 此时基类不可以被直接创建
在虚函数声明末尾加上 = 0, 表明函数为纯虚函数, 类为抽象基类(ABC Abstract Base Class)
纯虚函数也强制要求派生类必须提供纯虚函数的定义, 保证派生类的多态性
### 异形赋值
当有多个派生类, 在使用指向派生类的指针时, 应避免使用 *A(childA) = *B(childB) 赋值
如果childA有重载的operator=(base), 将会先把B转化为base再赋值, 导致B的数据丢失
可通过在ABC中, 将operator=设为protected禁止此行为
### 类设计要求 P427
---
# 代码重用
## 私有继承
默认的继承方式
基类的公有与保护成员变为派生类的私有成员
可以将基类作为派生类的私有成员达到同样的效果(has-a关系)
### 访问基类
1. 派生类内访问基类的公有函数(同共有继承)
使用域解析运算符访问基类 Base::fun();
2. 访问基类本身(基类共有成员同共有派生, 可直接访问)
强制转换 *this, 使其变为基类的引用 (const Base&) *this
3. 访问基类的友元函数 / 使用基类为参数的函数 / 派生类外访问基类的公有成员
私有继承时, 派生类将不会自动转换为基类, 需要强制类型转换为基类的引用/指针
### 使用私有继承的情况
大多数时候可将基类作为一个成员以达到相同效果
使用以下特性时则要使用私有继承
1. 访问protected成员
私有继承下, 派生类可以访问基类的protected成员
2. 使用虚函数
私有继承中可以重新定义虚函数, 但只能在类内使用(或强制转换为基类)
### 保护继承
同私有继承
但基类的公有与保护成员变为派生类的保护成员
使其可以在第三代派生类中继续访问基类的公有与保护成员
## 多重继承
对于继承结构
``` cpp
class Worker{};
class Singer: public Worker{};
class Waiter: public Worker{};
class SingWaiter: public Waiter, public Singer{};
```
由于Singer与Waiter均有一个Worker组件, SingWaiter将包含两个Worker组件
### 防止多态的二义性
``` cpp
SingWaiter sw;
Worker* w1 = &sw;//错误, 无法明确使用哪一个的Worker组件
Worker* w2 = (Singer*)sw;//指针
Worker* w3 = (Waiter*)sw;//指针
```
### 虚基类
实际上, SingWaiter不应该有多个Worker组件, 需要使用虚基类解决
``` cpp
class SingerV: virtual public Worker{};
class WaiterV: virtual public Worker{};
class SingWaiterV: public Waiter, public Singer{};
```
此时SingWaiter中的Singer与Waiter将共享同一个Worker组件
### 构造函数新规则
#### 使用SingerWaiter
对于非虚继承, 只能调用上一级基类的构造函数
因此SingerWaiter只能调用Waiter与Singer的构造函数以初始化基类
#### 使用SingerWaiterV
对于虚基类, 可以调用调用虚基类的构造函数与上级基类的构造函数
且调用虚基类构造函数将覆盖上级基类的构造函数中使用虚基类的部分
当虚基类有默认构造函数且类中没有调用虚基类的构造函数, 将自动调用默认构造函数并覆盖上一级基类中使用虚基类的部分
### 指明继承自基类的方法
在多重继承的两个基类中, 如果有同名函数, 将导致二义性
1. 通过域运算符指定 C c; c.A::fun(); c.B::fun();
2. 重新定义 C::fun(){A::fun();B::fun();}
### 混合使用虚继承与继承
``` cpp
class example: public A, public B, virtual public C, virtual public D
{};
```
当A, B, C, D均来自同一个基类base时, A 与 B 将有一个独立的base, C 与 D 将共享一个base
## 函数模板
### 定义模板
``` cpp
//模板函数的定义
template<typename Type>
void fun3(T)
```
### 显式具体化
默认情况下, 编译器会根据提供的类型替换模板, 生成具体的函数
但可能部分类型下函数不能很好的运行需要专门定义, 即显示具体化
eg.
```cpp
template<typename T>
bool fun(T& a, T& b){return a > b}
```
对于char*类型可能不适用
因此需要显式具体化
```cpp
template<> bool fun<type>(type a, type b){...}
```
type 为具体化的类型, template<> 为具体化的必要前缀
fun后的\<type\> 可以省略, 编译器将自动识别
具体化的函数参数必须与模板一致

函数模板是没有部分具体化的
### 具体化规则
允许函数同时存在 非模板版本, 具体化版本, 模板版本
编译器调用优先级 非模板版本(同名的普通函数)(未指定模板类型时) > 具体化版本 > 模板版本
### 显式实例化
```cpp
template bool fun<type>(type a, type b){...}
```
默认情况下编译器会自动实例化, 也可手动显示实例化, 此时不可省略\<type\>
且同一个文件中(cpp以及引用)不能同时有同一个函数的实例化与具体化
### 重载解析策略
1. 匹配参数
	1. 完全匹配(函数参数完全一致)
	2. 提升转换(short->int, float->double)
	3. 标准转换(long->double, int->char)
	4. 用户自定义转换
2. 最具体
非模板版本(未指定模板类型时) > 具体化版本 > 模板版本
3. 指定使用模板版本
当函数有非模板版本时, 在函数调用前加上\<\>或\<type\>将强制使用模板版本
### inline函数模板
```cpp
//正确写法
template<typename T>inline T min(const T&,const T&)
 
//错误写法
inline template<typename T>T min(const T&,const T&)
```
## 类模板
### 定义模板
在将要定义为模板的函数或类的定义/声明前加上 

template\<typename(模板参数类型 此处指类型) Type(参数名, 不同于变量)\>

``` cpp
//类声明 example.h
template<typename Type>
class example
{
	void fun1(T);
	void fun2(T)
	{
		...//类内定义不需要而额外 template<typename Type>
	}
};
//成员函数定义
template<typename Type>
void example<Type>::fun1(T){}

//模板函数的定义
template<typename Type>
void fun3(T)
```
由于模板不能被编译, 因此模板不能单独编译, 必须与实例化请求一起使用(即模板类与成员函数需要放在同一个头文件中)
### 使用模板
1. 实例化使用模板的类时必须使用\<Type\>指定所需的类型
2. 实例化使用模板的函数时不需要指定所需的类型, 编译器将根据函数参数的类型自动识别
3. 可以设置模板参数的默认值
eg. template\<typename a = int\>
### 非类型参数
除了typename, 模板中也可使用 int等作为参数类型, 称为表达式参数
规定表达式参数可以是整形, 指针, 枚举或引用, 不可以是double
在 template\<int a\> 中, a不属于变量, 不能修改参数的值, 也不能使用参数的地址 
### 模板实例化
编译器将自动实例化使用到的模板
也可显式实例化
```cpp
template class example<type>;
```
### 模板具体化
```cpp
template<> class example<type>{...};
...
void example<type>::fun(){}//类外定义成员函数不需要template<>
```
### 部分具体化
当类有多个模板参数时, 可以只针对其中几个参数具体化
```cpp
template<typename targ1, int targ2, ...> class example<targ1, targ2, ..., arg1, arg2, ...>{...};
```
1. template<> 内为没有被具体化的参数, 需要包含类型, 当template<>内为空即具体化整个模板
2. example<> 内为具体参数(模板中的所有参数), 可以是template中的未具体化的参数
eg
```cpp
template<T1, T2> class example<T1, T2, T2>{};
```
此类具体化未T2与T3相同时的具体化
3. 当有多个模板可选时, 将选择具体化程度最高的模板
### 成员函数具体化
可以单独具体化成员
```cpp
template<> void example<type>::fun();
```
当具体化成员函数时, 允许分离具体化函数的声明与定义(不同于一般的模板
)
### 关于具体化常量引用类型参数的模板
前提见 <a href='#clclzzyy'>常量常量指针引用</a>
```cpp
template<typename T>
//const T& obj要求obj所引用的值不能修改
void fun(const T& obj){...}

//指针指向const char*, 但指针本身是可以被修改的
template<> void fun<char*>(const char * & obj){...}//错误
//指针指向char*, 可以修改指针指向的内容, 但指针本身不能被修改的
template<> void fun<char*>(char *const & obj){...}//正确
template<> void fun<const char*>(const char *const & obj){...}//正确
```
### 类内模板
cpp允许成员函数或嵌套类为模板
```cpp
template<typename T>
class base
{
	template<typename U>
	class hold
	{U member;};

	hold<int> a;

	template<typename V>
	V fun(V obj){return obj;};
};
```
类外定义时注意
```cpp
template<typename T>
	template<typename U>//嵌套类的模板需要独立
	class base<T>::hold//使用带模板参数的类名与域解析运算符
	{...};

template<typename T>
	template<typename V>
	V base<T>::fun(V obj)
	{...} 
```
### 以模板类型为参数
对于定义
```cpp
template<template<typename T> typename TT, typename U, typename V>
class exam
{
	TT<U> a;
	TT<V> b;
	TT<int> c;
};
```
此时要求类型 TT 需要是一个参数为 typename 的模板类型
且可以在 exam 内指定 TT 的模板参数
### 模板类的友元
设模板类
```cpp
template<typename T>
class exam{...};
```
1. 非模板友元
	* 可以直接使用 friend void fun();
	* 也可以指定特定的重载函数 friend void fun(example<T>&);
	注意此处 fun 不是模板函数, 需要有对应参数的重载的 fun 才能为友元
2. 约束模板友元
	当友元为模板时, 友元模板的参数与类的模板参数有关
	eg.
	```cpp
	template<typename T>
	class exam
	{
		//将模板参数传递到友元的模板中
		friend void fun1<T>();
		//将模板参数传递到友元的函数参数中
		friend void fun2<>(exam<T>&);
		//fun2的等价写法
		//编译器能够自动识别模板参数
		friend void fun2<exam<T> >(exam<T>&);
	};
	```
3. 非约束模板友元
	当友元为模板时, 友元模板的参数与类的模板参数无关
	eg.
	```cpp
	template<typename T>
	class exam
	{
		// 所有fun1<U> 均为 exam<T> 的友元
		template<typename U> friend void fun1<U>();
	};
	```
### 模板别名
#### using =
cpp11 特性, 添加 using = 语法, 实现模板别名
eg.
```cpp
//模板别名
template<typename T>
	using arr12 = std::array<T, 12>;
//通常的 using= 等价于typedef
using strp = char*;
```
#### typedef 嵌套类型
结合模板类与嵌套类型的特性实现模板别名
```cpp
template<typename T>
class root
{
	class leaf;//仅声明

	//部分编译器需要关键则typename, 明确后面的部分为类型
	typedef typename std::map<leaf, T> arr;
};

template<typename T>
class root<T>::leaf
{
	arr a_;//可以直接使用别名不需要再指定类型
};
```
---
# 特性
## 友元
### 定义友元
定义一个友元时, 只要在类内使用 friend [友元定义];
可以在任何位置使用
### 前向声明
由于定义友元时, 类/函数可能未定义/循环定义, 需要前向声明
eg.
```cpp
//前向声明
class exam;

class HasFrined
{
	friend class exam;
}

class exam
{
	//exam中使用了HasFriend, 不能定义在HasFriend之前
	void fun(HasFriend&);
}
```
### 友元成员函数
有的时候不需要整个类均为友元, 仅需要部分成员函数作为友元
可以使用友元成员函数
```cpp
friend void exam::fun();
```
## 嵌套类
定义在类内的类为嵌套类
只有声明在 public 中的嵌套类才能包含类外使用
使用嵌套类需要使用域解析运算符
### 嵌套类的作用域
对于在私有部分声明的嵌套类, 在类的外部是不知道这个类的存在的
及类外不能使用嵌套类及其指针
### 嵌套类的访问控制
包含类不能访问嵌套类的私有元素
但是嵌套类可以访问包含类的私有元素

## 异常处理
### 提前结束程序
1. abort() 位于 cstdlib 中, 调用后将向cerr输出错误信息
2. exit() 仅提前结束程序
### 捕获异常
try: try 块中的代码标识将被激活的特定异常。它后面通常跟着一个或多个 catch 块。
catch: 在您想要处理问题的地方，通过异常处理程序捕获异常。catch 关键字用于捕获异常。
如果 try 块在不同的情境下会抛出不同的异常，这个时候可以尝试罗列多个 catch 语句，用于捕获不同类型的异常。
```cpp
try
{
   // 保护代码
}catch( [异常类型] [变量名称] )
{
   // catch 块
}catch(...)//表示任何异常
{
   // catch 块
}
```
也允许捕获异常类型的引用
catch(...){} 表示
### 抛出异常
通过关键字 throw 抛出异常, 抛出的异常允许是任何类型
当使用throw抛出异常后, 将会立即停止程序/函数(仍会执行==异常所在代码块中的构析函数==)
并开始进行栈解退(不断退出调用序列, 找到第一个能捕捉对应异常的try模块), 在栈解退的过程中, 会逐个释放中间函数所创建的临时变量并调用相应的析构函数
如果没有try捕捉异常, 或对应类型的catch, 将导致程序终止
否则将执行对应catch内的程序
### 实例
```cpp
#include<iostream>
using namespace std;

double fun(double a, double b)
{
	if(b == 0)
	{
		throw "Can not div by 0";
	}
	return a/b;
}

int main()
{
	try
	{
		fun(1.2, 0);
	}
	catch(const char* str)
	{
		cout << str << endl;
	}
}
```
### 异常规格说明
(几乎弃用的特性)
```cpp
double fun(double a, double b) throw(const char*)
{
	if(b == 0)
	{
		throw "Can not div by 0";
	}
	return a/b;
}
```
throw(const char*) 表明抛出的异常类型为 const char*
异常规格说明中允许有多种类型
不使用异常规格说明则表明可能为任意类型的异常
使用 throw() 表示不抛出异常
使用 noexcept 表示不抛出异常, 一旦使用 throw 将终止程序
### 捕获异常类的引用
通常会对异常进行派生, 从而表现出不同种类与大类的异常
利用基类引用可以指向派生类的特点, 捕获基类引用也可达到同时捕获派生类引用的效果
### cpp 标准的异常
一种继承自 std::exception 的类
定义于头文件 stdexcept 中
包含一个虚成员函数
```cpp
const char * what () const throw ()
```
实例
```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

struct MyException : public exception
{
	MyException(const string& s)
	{
		...
	}
	const char * what () const throw ()
	{
		return "cpp Exception";
	}
};
 
int main()
{
	try
	{
		throw MyException();
	}
	catch(MyException& e)
	{
		std::cout << "MyException caught" << std::endl;
		std::cout << e.what() << std::endl;
	}
	catch(std::exception& e)
	{
		//其他的错误
	}
}
```
<a href='https://www.runoob.com/cplusplus/cpp-exceptions-handling.html'>其他标准异常</a>

### 设计异常
1. 一个接收错误原因的构造函数
2. 构造函数为 explicit
3. 一个输出错误原因的 .what()
4. 从对应类型的标准异常派生
5. 作为有关类的嵌套类(可以自动成为友元)
### 管理异常
当一个异常没有被捕获时, 将会调用函数 terminate()
通常 terminate() 将直接调用 abort()
可以使用 set_terminate(f) 设置 terminate 的行为
f 为一个没有参数, 返回值为 void 的函数
### 抛出异常导致的内存泄漏
```cpp
void fun()
{
	double *p = new double[100];
	throw "err";//此时 delete 不会执行, 将导致内存泄漏
	delete[] p;
}
```

析构函数中更要特别注意
通过使用智能指针管理内存以避免此问题
或见 effect cpp 有关章节
## RTTI
运行阶段类型识别
### dynamic_cast
用于含虚函数的, 有派生关系的类
检查是否可以安全的将对象地址赋给特定类型的指针
```cpp
class child : public base
{...};
class grand : public child
{...};
grand *gr = new grand;
base *ba = new base;
//直接使用强制类型转换时, 编译器将不会检查问题
grand* p1 = dynamic_cast<grand*>(ba); //不安全, 使用 dynamic_cast 将返回空指针
grand* p2 = dynamic_cast<grand*>(gr); //安全, 返回 gr
base* p3 = dynamic_cast<base*>(gr); //安全, 返回 gr
```

#### 实例应用
```cpp
child* p4 = NULL;
base* parr[10]// 假设 parr 随机指向 base, child 与 grand
// 在一个循环中运行 child 中的成员函数 inChild
for(int i = 0; i < 10; i++)
{
	//通过此方法, 在将指针正确赋值的同时, 判断是否能够运行 inChild
	if(p4 = dynamic_cast<child*>(parr[i]))
	{
		p4->inChild();
	}
}
```

#### 对引用使用
```cpp
//注意赋值时的类型
try
{
	child& cr = dynamic_cast<base&>(ba);
}
catch(bad_cast& err)
{
...
}

```
对引用使用 dynamic_cast 时, 将不会返回特殊值, 而是抛出异常 bad_cast, 需要使用 try 捕捉

### typeid 与 type_info 类
typeid 用于判断变量的类型
返回一个 type_info 的引用
#### 使用方法
```cpp
if(typeid(a) === typeid(b))
{
	...
}
```
使用 type_info 类, 需要引用头文件 typeinfo
有成员函数 name(), 通常是类型名称, 但不一定, 不应直接与类型名比较

大部分情况下, dynamic_cast 可以完全取代 typeid
typeid 最好仅用于调试

### 类型转换运算符
#### const_cast
用于将一个常量指针转化为普通指针
```cpp
const int* cpi = new int(10);
const double* cpd = new double(3.14);

int* pi = const_cast<int*>(cpi);// 允许, pi 可以修改 cpi 指向的值
int* pi2 = const_cast<int*>(cpd);// 不允许, const_cast 不能改变类型 
```
#### static_cast
即存在对应操作符重载函数的转换运算
#### reinterpret_cast
直接读取内存的强制类型转换