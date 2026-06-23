/* 不涉及界面编程或大型应用程序编程的不用看C++ */

====
1、C++中为什么用模板类?
答：
  (1) 可用来创建动态增长和减小的数据结构
 （2）它是类型无关的，因此具有很高的可复用性。
 （3）它在编译时而不是运行时检查数据类型，保证了类型安全
 （4）它是平台无关的，可移植性
 （5）可用于基本数据类型 
权重：中
备注：理解模板和实例化。

====
2、引用与指针有什么区别？
      1) 引用必须被初始化，指针不必。
      2) 引用初始化以后不能被改变，指针可以改变所指的对象。
      3) 不存在指向空值的引用，但是存在指向空值的指针。
权重：高

====
3、C和C++的不同
 c和c++的一些不同点（从语言本身的角度）：
 1）c++源于c，c++最重要的特性就是引入了面向对象机制，class关键字。
 2）c++中，变量可以在任何地方声明；c中，局部变量只能在函数开头声明（对于现在的编辑器都是C/C++同时支持，所以C中也可以在函数中间定义，没关系的，只是不太符合编程规范而已）。
 3）c++中，const型常量是编译时常量；c中，const常量只是只读的变量。
 4）c++有&引用;c没有
 5）c++的struct声明自动将结构类型名typedef；c中struct的名字只在结构标签名字空间中，不是作为一种类型出现
 6）c语言的main函数可以递归调用;c++中则不可以
 7）c中，void *可以隐式转换成其他指针类型；c++中要求显式转换，否则编译通不过（这也是C的一大妙用，实际编程经常会把不同类型转来转去）
 8）c++中有许多c中无法使用，但是很实用的库函数，如链表、排序、查找等。（C的队列链表循环缓存这些都需要自己写）
权重：较高

====
13.C++中什么数据分配在栈或堆中，New分配数据是在近堆还是远堆中？
答：栈: 存放局部变量，函数调用参数,函数返回值，函数返回地址。由系统管理
堆: 程序运行时动态申请，new 和　malloc申请的内存就在堆上
权重：高

====
7. 请问C++的类和C里面的struct有什么区别？
答:c++中的类具有成员保护功能，并且具有继承，多态这类特点，而c里的struct没有 
c里面的struct没有成员函数,不能继承,派生等等.
权重：中

====
8. 请讲一讲析构函数和虚函数的用法和作用？
答:析构函数也是特殊的类成员函数，它没有返回类型，没有参数，不能随意调用，也没有重载。只是在类对象生命期结束的时候，由系统自动调用释放在构造函数中分配的资源。这种在运行时，能依据其类型确认调用那个函数的能力称为多态性，或称迟后联编。另： 析构函数一般在对象撤消前做收尾工作，比如回收内存等工作，
虚拟函数的功能是使子类可以用同名的函数对父类函数进行覆盖，并且在调用时自动调用子类覆盖函数，如果是纯虚函数，则纯粹是为了在子类覆盖时有个统一的命名而已。
注意:子类重新定义父类的虚函数的做法叫覆盖,override,而不是overload(重载),重载的概念不属于面向对象编程,重载指的是存在多个同名函数,这些函数的参数表不同..重载是在编译期间就决定了的,是静态的,因此,重载与多态无关.与面向对象编程无关.
含有纯虚函数的类称为抽象类,不能实例化对象,主要用作接口类
权重：中

====
再次更新C++相关题集 
1. 以下三条输出语句分别输出什么？[C易] 
char str1[] = "abc"; 
char str2[] = "abc"; 
const char str3[] = "abc"; 
const char str4[] = "abc"; 
const char* str5 = "abc"; 
const char* str6 = "abc"; 
cout << boolalpha << ( str1==str2 ) << endl; // 输出什么？ 
cout << boolalpha << ( str3==str4 ) << endl; // 输出什么？ 
cout << boolalpha << ( str5==str6 ) << endl; // 输出什么？ 

====
13. 非C++内建型别 A 和 B，在哪几种情况下B能隐式转化为A？[C++中等] 
答： 
a. class B : public A { ……} // B公有继承自A，可以是间接继承的 
b. class B { operator A( ); } // B实现了隐式转化为A的转化 
c. class A { A( const B& ); } // A实现了non-explicit的参数为B（可以有其他带默认值的参数）构造函数 
d. A& operator= ( const A& ); // 赋值操作，虽不是正宗的隐式类型转换，但也可以勉强算一个 

====
12. 以下代码中的两个sizeof用法有问题吗？[C易] 
void UpperCase( char str[] ) // 将 str 中的小写字母转换成大写字母 
{ for( size_t i=0; i<sizeof(str)/sizeof(str[0]); ++i ) 
if( 'a'<=str[i] && str[i]<='z' ) 
str[i] -= ('a'-'A' ); 
} char str[] = "aBcDe"; 
cout << "str字符长度为: " << sizeof(str)/sizeof(str[0]) << endl; 
UpperCase( str ); 
cout << str << endl; 

====
7. 以下代码有什么问题？[C难] 
void char2Hex( char c ) // 将字符以16进制表示 
{ char ch = c/0x10 + '0'; if( ch > '9' ) ch += ('A'-'9'-1); 
char cl = c%0x10 + '0'; if( cl > '9' ) cl += ('A'-'9'-1); 
cout << ch << cl << ' '; 
} char str[] = "I love 中国"; 
for( size_t i=0; i<strlen(str); ++i ) 
char2Hex( str[i] ); 
cout << endl; 

====
4. 以下代码有什么问题？[C++易] 
struct Test 
{ Test( int ) {} 
Test() {} 
void fun() {} 
}; 
void main( void ) 
{ Test a(1); 
a.fun(); 
Test b(); 
b.fun(); 
} 

====
5. 以下代码有什么问题？[C++易] 
cout << (true?1:"1") << endl; 

====
8. 以下代码能够编译通过吗，为什么？[C++易] 
unsigned int const size1 = 2; 
char str1[ size1 ]; 
unsigned int temp = 0; 
cin >> temp; 
unsigned int const size2 = temp; 
char str2[ size2 ]; 

====
9. 以下代码中的输出语句输出0吗，为什么？[C++易] 
struct CLS 
{ int m_i; 
CLS( int i ) : m_i(i) {} 
CLS() 
{ CLS(0); 
} }; 
CLS obj; 
cout << obj.m_i << endl; 

====
10. C++中的空类，默认产生哪些类成员函数？[C++易] 
答： 
class Empty 
{ public: 
Empty(); // 缺省构造函数 
Empty( const Empty& ); // 拷贝构造函数 
~Empty(); // 析构函数 
Empty& operator=( const Empty& ); // 赋值运算符 
Empty* operator&(); // 取址运算符 
const Empty* operator&() const; // 取址运算符 const 
}; 

====
3. 以下两条输出语句分别输出什么？[C++难] 
float a = 1.0f; 
cout << (int)a << endl; 
cout << (int&)a << endl; 
cout << boolalpha << ( (int)a == (int&)a ) << endl; // 输出什么？ 
float b = 0.0f; 
cout << (int)b << endl; 
cout << (int&)b << endl; 
cout << boolalpha << ( (int)b == (int&)b ) << endl; // 输出什么？ 

====
2. 以下反向遍历array数组的方法有什么错误？[STL易] 
vector array; 
array.push_back( 1 ); 
array.push_back( 2 ); 
array.push_back( 3 ); 
for( vector::size_type i=array.size()-1; i>=0; --i ) // 反向遍历array数组 
{ cout << array[i] << endl; 
} 

====
6. 以下代码有什么问题？[STL易] 
typedef vector IntArray; 
IntArray array; 
array.push_back( 1 ); 
array.push_back( 2 ); 
array.push_back( 2 ); 
array.push_back( 3 ); 
// 删除array数组中所有的2 
for( IntArray::iterator itor=array.begin(); itor!=array.end(); ++itor ) 
{ if( 2 == *itor ) array.erase( itor ); 
} 

====
11. 写一个函数，完成内存之间的拷贝。[考虑问题是否全面] 
答： 
void* mymemcpy( void *dest, const void *src, size_t count ) 
{ 
char* pdest = static_cast<char*>( dest ); 
const char* psrc = static_cast<const char*>( src ); 
if( pdest>psrc && pdest<psrc+cout ) 能考虑到这种情况就行了 
{ 
for( size_t i=count-1; i!=-1; --i ) 
pdest[i] = psrc[i]; 
} 
else 
{ 
for( size_t i=0; i<count; ++i ) 
pdest[i] = psrc[i]; 
} 
return dest; 
} 
int main( void ) 
{ 
char str[] = "0123456789"; 
mymemcpy( str+1, str+0, 9 ); 
cout << str << endl; 
system( "Pause" ); 
return 0; 
}


