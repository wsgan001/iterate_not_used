# 错题精解

> 牛客网基础题总结。

---

# 目录

| Chapter 1 | Chapter 2 |
| :---------: | :---------: |
|[编程语言(C++)](#prog)|[其他](#other)|

---

# 内容

## <span id = "prog">编程语言(C++)</span>

#### [Q 1](http://www.cnblogs.com/skynet/p/3343726.html) :

    题目：
        以下代码：
        class ClassA{
        public:
            virtual ~ClassA(){};
            virtual void FunctionA(){};
        };
        class ClassB{
        public:
            virtual void FunctionB(){};
        };
        class ClassC:public ClassA, public ClassB{
            public:
        };
        ClassC Object;
        ClassA* pA = &Object;
        ClassB* pB = &Object;
        ClassC* pC = &Object;
        关于pA,pB,pC的取值,下面的描述中正确的是:

    答案:
        pA和pB不相同

    解答:
        考察多继承且有虚函数情况下C++存储对象模型。
        1. 多继承按继承顺序组织对象模型，有虚函数时低地址包含指向虚函数表的指针。
        2. 对象Object的存储模型：类A虚函数表指针(ptrA) | 类A数据 | 类B虚函数表指针(ptrB) | 类B数据 | 类C数据。
        3. 子类的虚函数被放到了第一个基类的虚函数表最后（ptrA指向的虚函数表结构：类A虚函数 | 类C虚函数）。
        4. 有虚函数的继承，对象地址为指向虚函数表的指针的地址，即pC = &Object = &ptrA。
        6. pC = pA = &ptrA = &Object，(pC = pA) < pB。

#### [Q 2](http://www.cnblogs.com/qlwy/archive/2011/08/25/2153584.html) :

    题目：
        下列程序的输出结果：
        #include <iostream>
        using namespace std;
        class A{
        public:
            void print(){
                cout << "A:print()";
            }
        };
        class B:private A{
        public:
            void print(){
              cout << "B:print()";
            }
        };
        class C:public B{
        public:
           void print(){
                A::print();
            }
        };
        int main(){
            C b;
            b.print();
        }

    答案:
        编译出错

    解答:
        考察C++继承问题。
        1. 类B私有继承类A。
        2. 私有继承：类A的公有成员和保护成员都作为类B的私有成员，并且不能被类B的子类（如类C）所访问。

#### [Q 3](http://www.cnblogs.com/motadou/archive/2009/01/17/1558438.html) :

    题目：
        下面两个结构体：
        struct One{
            double d;
            char c;
            int i;
        }
        struct Two{
            char c;
            double d;
            int i;
        }
        在#pragma pack(4)和#pragma pack(8)的情况下，结构体的大小分别是：

    答案:
        16，16
        16，24

    解答:
        考察结构体对齐。
        1. 4字节对齐：Struct One[8 + (1 + 3(pading)) + 4], struct Two[(1 + 3(pading)) + 8 + 4]。
        2. 8字节对齐：Struct One[8 + (1 + 3(pading) + 4)], struct Two[(1 + 7(pading)) + 8 + (4 + 4(pading))]。
        3. 一句话总结：按序存储，装得下尽量装，装不下换一行。

#### [Q 4](http://blog.csdn.net/dgyanyong/article/details/21268469) :

    题目：
        下列代码的输出为：
        #include "iostream"
        #include "vector"
        using namespace std;
        int main(void)
        {
            vector<int>array;
            array.push_back(100);
            array.push_back(300);
            array.push_back(300);
            array.push_back(500);
            vector<int>::iterator itor;
            for(itor = array.begin(); itor != array.end(); itor++){
                if(*itor == 300){
                    itor = array.erase(itor);
                }
            }
            for(itor = array.begin(); itor != array.end(); itor++){
                cout << *itor << " ";
            }
            return 0;
        }

    答案:
        100
        300
        500

    解答:
        考察STL中erase和迭代器问题。
        1. erase返回值是一个迭代器，指向删除元素下一个元素。
        2. 删除第一个300时返回指向下一个300的迭代器，在循环体又被再加了一次，跳过了第二个300。

#### Q 5 :

    题目：
        下面程序的输出是什么？
        int main(void)
        {
            int a[5] = {1, 2, 3, 4, 5};
            int *ptr = (int *)(&a + 1);
            printf("%d,%d", *(a + 1), *(ptr - 1));
            return 0;
        }

    答案:
        2
        5

    解答:
        1. a表示数组首元素的地址，对a的所有操作均是以一个元素为单位的。
        2. &a表示整个数组的地址，对&a的所有操作均是以一个数组为单位的。
        3. ptr类型为int *，所有对ptr的所有操作均是以int大小为单位进行的。
        4. (int *)(&a + 1)表示指向a数组最后一个元素后一个字节的int类型指针，*(ptr - 1)表示向前移动一个int类型的数据的位置。
        5. 所有指针类型操作先看右侧是以什么为单位，之后再转换为左侧定义的单位。

#### Q 6* :

    题目：
        32位机上根据下面的代码，问哪些说法是正确的？
        signed char a = 0xe0;
        unsigned int b = a;
        unsigned char c = a;

    答案:
        b的十六进制表示是：0xffffffe0

    解答:
        考察有符号数和无符号数之间的转换。
        1. a : 1110 0000。
        2. 扩展问题：
            长 -> 短：低位对齐，按位复制。
            短 -> 长：符号位扩展。
        3. 精度提升：
            两个变量运算，表示范围小的变量精度达的变量提升（signed -> unsigned）。

#### Q 7 :

    题目：
        下列代码的输出为：
        int* pint = 0;
        pint += 6;
        cout << pint << endl;

    答案:
        24

    解答:
        考察指针运算。
        1. 变量pint为指向int类型的指针，这里“+1”表示地址加4（pint值加4）。
        2. 变量pint初值为0，pint + 6后pint的值变为24。

#### Q 8 :

    题目：
        如果两段内存重叠，用memcpy函数可能会导致行为未定义。而memmove函数能够避免这种问题，下面是一种实现方式，请补充代码。
        #include <iostream>
        using namespace std;
        void* memmove(void* str1, const void* str2, size_t n)
        {
            char* pStr1 = (char*) str1;
            const char* pStr2 = (const char*)str2;
            if( ){
                for(size_t i = 0;i != n; ++i){
                    *(pStr1++) = *(pStr2++);
                }
            }
            else{
                pStr1 += n - 1;
                pStr2 += n - 1;
                for(size_t i = 0; i != n; ++i){
                    *(pStr1--) = *(pStr2--);
                }
            }
            return ( );
        }

    答案:
        pStr1 < pStr2
        str1

    解答:
        1. 逐字符自动不存在内存覆盖问题。

#### Q 9 :

    题目：
        设x、y、t均为int型变量，则执行语句：t = 3; x = y = 2; t = x++ || ++y; 后，变量t和y的值分别为：

    答案:
        t = 1
        y = 2

    解答:
        考察逻辑短路和运算符优先级。
        1*. =的优先级最低，t = (x++ || ++y) = 1。
        2. x++和++y为或关系，因为x++的值非0，所以++y不执行，y不变。

#### Q 10 :

    题目：
        指出下面程序哪里可能有问题？
        class CBuffer
        {
            char * m_pBuffer;
            int m_size;
        public:
            CBuffer(){
                m_pBuffer = NULL;
            }
            ~CBuffer(){
                Free();
            }
            void Allocte(int size) (1) {
                m_size = size;
                m_pBuffer = new char[size];
            }
        private:
            void Free(){
                if(m_pBuffer! = NULL) (2){
                    delete[] m_pBuffer;
                    m_pBuffer = NULL;
                }
            }
        public:
            void SaveString(const char* pText) const (3){
                strcpy(m_pBuffer, pText); (4)
            }
            char* GetBuffer() const{
                return m_pBuffer;
            }
        };
        void main (int argc, char* argv[])
        {
            CBuffer buffer1;
            buffer1.SaveString("Microsoft");
            printf(buffer1.GetBuffer());
        }

    答案:
        1
        3
        4

    解答:
        考察动态分配空间等周边细节处理。
        1. 分配内存时, 未检测m_pBuffer是否为空, 容易造成内存泄露。
        2. 常成员函数不应该对数据成员做出修改, 虽然可以修改指针数据成员指向的数据, 但原则上不应该这么做。
        3*. 字符串拷贝时, 未检测是否有足够空间, 可能造成程序崩溃。

#### [Q 11](http://blog.csdn.net/candyliuxj/article/details/6307814) :

    题目：
        某32位系统下, C++程序，请计算sizeof 的值：
        char str[] = "http://www.xxxxx.com";
        char *p = str;
        int n = 10;
        sizeof(str) = (1);
        sizeof(p) = (2;
        sizeof(n) = (3);
        void Foo(char str[100]){
            sizeof(str) = (4);
        }
        void *p = malloc(100);
        sizeof(p) = (5);

    答案:
        21
        4
        4
        4
        4

    解答:
        考察sizeof返回值。
        1. 具体类型，返回该类型所占的空间大小。
        2. 对象，返回对象的实际占用空间大小。
        3. 数组，返回编译时分配的数组空间大小（数组名 ≠ 指针）。作为参数时数组退化为指针。
        4. 指针，返回存储该指针所用的空间大小。
        5. 函数，返回函数的返回类型所占的空间大小。函数的返回类型不能是void。
        6. 上题中(2)(4)(5)均为指针。

#### [Q 12*](http://blog.csdn.net/heyabo/article/details/8745942) :

    题目：
        在C++中，
        const int i = 0;
        int *j = (int *) &i;
        *j = 1;
        printf("%d, %d", i, *j);
        输出是多少？

    答案:
        0
        1

    解答:
        考察C++常量折叠。
        1. const变量放在编译器的符号表中，计算时编译器直接从表中取值，省去了访问内存的时间，从而达到了优化。
        2. 结论，const变量通过取地址方式可以修改该地址存储的数据值，但不能修改常量的值。

#### Q 13 :

    题目：
        下列代码的输出为：
        class parent{
        public:
            virtual void output();
        };
        void parent::output(){
            printf("parent!");
        }
        class son : public parent{
        public:
            virtual void output();
        };
        void son::output(){
            printf("son!");
        }
        son s;
        memset(&s, 0, sizeof(s));
        parent& p = s;
        p.output();

    答案:
        没有输出结果，程序运行出错。

    解答:
        考察memset和虚函数指针。
        1. 虚函数表地址被清空。

#### Q 14 :

    题目：
        有哪几种情况只能用intialization list而不能用assignment？

    答案*:
        当类中含有const成员变量；基类无默认构造函数时，有参的构造函数都需要初始化表；当类中含有reference成员变量。

    解答:
        1. 见答案。

#### [Q 15](http://blog.csdn.net/yby4769250/article/details/7294696) :

    题目：
        对以下数据结构中data的处理方式描述正确的是：
        struct Node{
            int size;
            char data[0];
        };

    答案:
        编译器会认为这就是一个长度为0的数组,而且会支持对于数组data的越界访问。

    解答:
        考察柔性数组。
        1*. 柔性数组，作为占位符放在结构体末尾，使得结构体的大小动态可变，在声明结构体变量的时候可根据需要动态分配内存。
        2. 长度为0的数组并不占用空间，因为数组名本身不占空间，它只是一个偏移量， 数组名这个符号本身代表了一个不可修改的地址常量。
        3. 常用于网络通信中构造不定长数据包，不会浪费空间浪费网络流量。

#### Q 16 :

    题目：
        给定3个int类型的正整数x，y，z，对如下4组表达式判断正确的选项：
        int a1 = x + y - z; int a2 = x - z + y;
        int b1 = x * y / z; int b2 = x / z * y;
        int c1 = x << y >> z; int c2 = x >> z << y ;
        int d1 = x & y | z; int d2 = x | z & y;

    答案:
        a1一定等于a2

    解答:
        考察对变量运算原理的了解。
        1. 加减操作虽然可能出现溢出，但相同操作数的不同顺序只是中间结果不同，最终结果相同。
        2. int类型做除法可能会造成截断，比如3/2 = 1。
        3. 移位运算可能会丢弃超出的位数。有符号数二进制数1111 1111，先左移2位再右移三位为1111 1111，反之1111 1100。

#### Q 17 :

    题目：
        若有以下定义和语句：
        char s1[] = "12345", *s2 = "1234";
        printf("%d\n", strlen(strcpy(s1, s2)));
        则输出结果是：

    答案:
        4

    解答:
        考察strcpy和strlen。
        1. 首先strlen得到的是'\0'之前的字符长度。
        2. strcpy将s2指向的字符串'1234\0'全部拷贝到s1指向位置并覆盖其'12345'部分。

#### Q 18 :

    题目：
        以下函数用法正确的个数是：
        void test1(){
            unsigned char array[MAX_CHAR + 1], i;
            for(i = 0;i <= MAX_CHAR; i++){
                array[i] = i;
            }
        }

        char *test2(){
            char p[] = "hello world";
            return p;
        }
        char *p = test2();

        void test3(){
            char str[10];
            str++;
            *str = '0';
        }

    答案:
        0

    解答:
        考察数组名和指针区别。
        1. i的范围有可能超过unsigned char范围。
        2. 这里char p[] = "hello world"是数组，该数组是临时变量，函数结束后不能继续使用。
        3. 如果为char *p = "hello world"，这里p是指针并指向常量区字串，虽然p会被销毁，但字符串仍然在，就不会出问题。
        4. 这里str是数组名，数组名是常量，不可以自增，正确的操作是char *p = str; p++; *p = '0'。

#### Q 19 :

    题目：
        假设在一个32位little endian的机器上运行下面的程序，结果是多少？
        #include <stdio.h>
        int main(){
            long long a = 1, b = 2, c = 3;
            printf("%d %d %d\n", a, b, c);
            return 0;
        }

    答案:
        1
        0
        2

    解答:
        考察小端法及printf输出控制符。
        1. long long占8字节。
        2. 小端表示，低字节在低位，最低4字节为1，接下来四字节为高位部分的0，再接下来4字节为第二个数低位的2。
        3. printf的控制符相当于分配好待打印容器大小，这里"%d %d %d"就分配了12字节，分别装入三个8字节元素，只装入一半。

#### Q 20 :

    题目：
        请选择下列程序的运行结果:
        #include<iostream>
        using namespace std;
        class B0{
        public:
            virtual void display(){
                cout << "B0::display0" << endl;
            }
        };
        class B1:public B0{
        public:
            void display(){
                cout << "B1::display0" << endl;
            }
        };
        class D1: public B1{
        public:
            void display(){
                cout << "D1::display0" << endl;
            }
        };
        void fun(B0 ptr){
            ptr.display();
        }
        int main(){
            B0 b0;
            B1 b1;
            D1 d1;
            fun(b0);
            fun(b1);
            fun(d1);
        }

    答案:
        B0::display0
        B0::display0
        B0::display0
    解答:
        1. 这里传递的是对象本身而非指针，对象被直接转为基类对象，调用基类的函数。
        2. 如果要实现虚函数动态绑定需要将B0 ptr改为B0* ptr，ptr->display()。
        3. 对象的形参传递需要先使用拷贝构造函数（默认）生成B0类型的临时变量，只拷贝基类部分数据（只有指向基类虚函数表的虚函数指针）。

#### Q 21 :

    题目：
        i的初始值为0，i++在两个线程里面分别执行100次，能得到最大值是()，最小值是()。

    答案:
        200
        2

    解答:
        考察多线程操作同一未上锁变量。
        1. 每次都准确加1，结果为最大，200。
        2. 结果为2时步骤：
            a取内存0到寄存器，b取内存0到寄存器；
            a执行99次并写入内存，内存值为99；
            b执行1次并写入内存，内存值被覆盖为1；
            a取内存1到寄存器，b取内存1到寄存器；
            b执行99次并写入内存，内存值为100；
            a执行1次，写入内存，覆盖之前的100，值为2。
        3. 每次计算过程必须是先从内存取数然后计算，之后再重新写入内存。但对各个线程而言，取数和计算中间可以被另一个线程打断。

#### Q 22 :

    题目：
        char fun(char x, char y){
            if(x)
                return(y);
        }
        int main(){
            int a = '0', b = '1', c = '2';
            printf("%c\n", fun(fun(a, b), fun(b, c)));
        }

    答案:
        2

    解答:
        1. 均为字符，非布尔值的0，所以每次返回后者。

#### Q 23 :

    题目：
        当一个类A中没有声明任何成员变量与成员函数,这时sizeof(A)的值是多少？

    答案:
        1

    解答:
        1. 一个空类对象的大小是1byte。这是被编译器安插进去的一个字节，这样就使得这个空类的两个实例得以在内存中配置独一无二的地址。

#### Q 24 :

    题目：
        有以下程序：
        #include<stdio.h>
        #include<stdlib.h>
        void fun(int *pl, int *p2, int *s){
            s = (int*)calloc(1, sizeof(int));
            *s = *pl + *p2;
            free(s);
        }
        int main(){
            int a[2] = {1, 2}, b[2] = {40, 50}, *q = a;
            fun(a, b, q);
            printf("%d\n", *q);
        }

    答案:
        1

    解答:
        考察形参不改变变量值问题。
        1. p是指针变量，但是是值传递，其值(指向数组a首元素的地址)并没有改变。
        2. 通过解引用*p才是数组a的地址，才能改变数组a的值。

#### [Q 25*](http://www.cnblogs.com/klcf0220/p/6889122.htmls) :

    题目：
        在32位操作系统gcc编译器环境下，下面程序的运行结果为：
        #include <iostream>
        using namespace std;
        class A{
        public:
            int b;
            char c;
            virtual void print(){
                cout << "this is father’s fuction! " << endl;
            }
        };
        class B: A{
        public:
            virtual void print(){
                cout << "this is children’s fuction! " << endl;
            }
        };
        int main(int argc, char * argv[]){
            cout << sizeof(A) << " " << sizeof(B) << endl;
            return 0;
        }

    答案:
        12
        12

    解答:
        考察结构体对齐及虚继承和虚函数继承的区别。
        1. A的大小包括本身的虚函数指针及定义的变量。
        2. B的大小包括本身的虚函数指针和继承自A的变量b和c。
        3. 如果是虚继承，则B的大小会增加4字节，增加的内容为指向虚继承的指针。

#### Q 26 :

    题目：
        有如下语句序列：
        char str[10]；
        cin >> str；
        当从键盘输入"I love this game"时，str中的字符串是:

    答案:
        I
    解答:
        1*. cin遇空格，结束输入。

#### Q 27 :

    题目：
        阅读下面代码，程序会打印出来的值是：
        #include <stdio.h>
        void f(char** p){
            *p += 2;
        }
        int main(){
            char *a[] = {"123", "abc", "456"}, **p;
            p = a;
            f(p);
            printf("%s\r\n", *p);
        }

    答案:
        3

    解答:
        1. p的类型为char **，(*P)的类型为char *。
        2. p原本指向字符串"123"。
        3. *p是char *类型的，*p + 2表示指向第一个字符串第三个字符。
        4. p是char **类型的，p + 2表示只想第三个字符串，*(p + 2) = "456"。
        5. p的值是*p的地址，虽然p是形参本身值未变，但*p的值在调用函数中被改变。

#### [Q 28](http://www.cnblogs.com/skynet/archive/2010/09/05/1818636.html) :

    题目：
        下列对函数double add(int a, int b)进行重载，正确的是：

    答案:
        int add(int a, int b, int c)
        int add(double a, double b)
        double add(double a, double b)

    解答:
        考察重载概念。
        1. 在使用重载时只能通过相同的方法名，不同的参数形式实现。
        2. 不同参数形式包括：
            参数类型不同（至少有一个）
            参数个数不同
            *如果同时在类中，对于函数名相同的const函数和非const函数能够构成重载
        3. 编译器区分重载函数是通过“返回类型 + 函数名 + 参数列表”重新改写函数名还区分重载函数的，但返回值类型在C++中并不作为重载标记。

#### Q 29 :

    题目：
        在linux gcc下，关于以下代码，正确的是：
        std::string& test_str(){
            std::string str = "test";
            return str;
        }
        int main(){
            std::string& str_ref = test_str();
            std::cout << str_ref << std::endl;
            return 0;
        }

    答案:
        编译警告
        返回局部变量的引用,运行时出现未知错误
        把代码里的&都去掉之后,程序可以正常运行

    解答:
        考察调用函数返回值和变量生命周期问题。
        1. 返回值为局部变量时可以正确运行。
        2. 返回值为指针时，看指针指向的变量实体定义的位置，如果是定义在栈上的变量则会出错，指向静态区则不会有问题。
        3. 引用返回的是局部变量本身，而不是复制一份再返回，所以结果难以预料。
        4. 如果去掉&，string类会调用复制构造函数，形同局部变量返回，可以正常运行。

#### Q 30 :

    题目：
        下面有关继承、多态、组合的描述，说法错误的是：

    答案:
        继承可以使用现有类的所有功能，并在无需重新编写原来的类的情况下对这些功能进行扩展
        覆盖是指不同的函数使用相同的函数名，但是函数的参数个数或类型不同

    解答:
        考察继承、多态概念。
        1. 父类只有非private的部分才能被子类继承访问。
        2. 重载（overload）：函数名相同 、函数参数不同、 必须位于同一个域（类）中。
        3. 覆盖（override）：函数名相同 、函数参数相同、 分别位于派生类和基类中（虚函数）。

#### Q 31 :

    题目：
        分析一下这段程序的输出：
        #include<iostream>
        using namespace std;
        class B{
        public:
            B(){
                cout << "default constructor" << " ";
            }
            ~B(){
                cout << "destructed" << " ";
            }
            B(int i):data(i){
                cout << "constructed by parameter" << data << " ";
            }
        private:
            int data;
        };
        B Play(B b){
            return b;
        }
        int main(int argc, char *argv[]){
            B temp = Play(5);
            return 0;
        }

    答案:
        constructed by parameter5
        destructed
        destructed

    解答:
        考察赋值运算顺序以及拷贝构造函数。
        1. B temp = Play(5)从右向左执行。
        2. 先将"5"转为形式参数B的类型，之后调用B(int i)打印"constructed by parameter"。
        3. B temp调用B的默认浅拷贝构造函数，完成赋值，由于拷贝构造函数没有输出，所以没有打印东西。
        4. Play()生命周期结束后，b析构打印"destructed"。
        5. main()生命周期结束后，temp析构打印"destructed"。

#### Q 32 :

    题目：
        int i=10, j=10, k=3;
        k*=i+j;
        k最后的值是？

    答案:
        60

    解答:
        考察运算符优先级。
        1. +优先级高于*=，等价于k = k * (i +j)。

#### Q 33 :

    题目：
        #include命令的功能是：

    答案:
        在命令处插入一个文本文件

    解答:
        1. "#include"在命令处插入，插入文本过程为预处理过程。

#### Q 34 :

    题目：
        有一个类A，其数据成员如下：
        class A {
        private:
            int a;
        public:
            const int b;
            float* &c;
            static const char* d;
            static double* e;
        };
        则构造函数中，成员变量一定要通过初始化列表来初始化的是：

    答案:
        b
        c

    解答:
        考察构造函数初始化列表的使用。
        1. 构造函数中，成员变量一定要通过初始化列表来初始化的有以下几种情况：
            const常量成员：因为常量只能在初始化，不能赋值，所以必须放在初始化列表中。
            引用类型：引用必须在定义的时候初始化，并且不能重新赋值，所以也要写在初始化列表中。
            没有默认构造函数的类类型：因为使用初始化列表可以不必调用默认构造函数来初始化，而是直接调用拷贝构造函数。

#### Q 35 :

    题目：
        在一个64位的操作系统中定义如下结构体：
        struct st_task{
            uint16_t id;
            uint32_t value;
            uint64_t timestamp;
        };
        同时定义fool函数如下：
        void fool(){
            st_task task = {};
            uint64_t a = 0x00010001;
            memcpy(&task, &a, sizeof(uint64_t));
            printf("%11u, %11u, %11u", task.id, task.value, task.timestamp);
        }
        上述fool()程序的执行结果为：

    答案:
        1
        0
        0

    解答:
        考察结构体对齐。
        1. 假设低地址在低位，最低16 bits被赋给低16位的id变量。
        2. 接下来16 bits的0x0001部分被赋值给了pading部分，没有被使用。
        3. value和timestamp均未被赋值。

#### Q 36 :

    题目：
        在32位系统中：
        char arr[] = {4, 3, 9, 9, 2, 0, 1, 5};
        char *str = arr;
        sizeof(arr) = (1);
        sizeof(str) = (2);
        strlen(str) = (3);
    答案:
        8；4；5

    解答:
        考察指针和数组名使用sizeof时区别及转义字符。
        1. 数字0对应'\0'。
        2. strlen求字符串长到'\0'前。

#### Q 37 :

    题目：
        下面代码输出什么：
        #include<stdio.h>
        int main( ){
            unsigned int a = 6;
            int b = -20;
            (a + b > 6) ? printf(">6") : printf("<=6");
            return 0;
        }

    答案:
        >6

    解答:
        考察强制类型转换。
        1. 必须先明确：int与unsigned相加，int -> unsigned int。
        2. int b = -20，首位位"1"，用无符号型表示是非常大的正整数。

#### Q 38 :

    题目：
        对于下面的C语言声明描述正确的一项是:
        char (*p)[16]

    答案:
        p是指向长度为16的字符数组的指针

    解答:
        考察运算符优先级。
        1. p先和那个运算符结合就是什么。
        2. char *p[16]：p是一个包含16个元素的Char型指针数组，[]优于*，p[]先结合，是数组。
        3. char (*p)[16]：p是一个指针，指向一个包含16个元素的char数组，由于()出现，先和*结合，是指针。

#### [Q 39](http://blog.csdn.net/crystal_avast/article/details/7678704) :

    题目：
        下面程序输出结果是什么：
        #include<iostream>
        using namespace std;
        class A{
        public:
            A(char *s){
                cout << s << endl;
            }
            ~A(){}
        };
        class B:virtual public A{
        public:
            B(char *s1, char*s2):A(s1){
                cout << s2 << endl;
            }
        };
        class C:virtual public A{
        public:
            C(char *s1, char*s2):A(s1){
                cout << s2 << endl;
            }
        };
        class D:public B, public C{
        public:
            D(char *s1, char *s2, char *s3, char *s4):B(s1, s2), C(s1, s3), A(s1){
                cout << s4 << endl;
            }
        };
        int main() {
            D *p = new D("class A", "class B", "class C", "class D");
            delete p;
            return 0;
        }

    答案:
        class A
        class B
        class C
        class D

    解答:
        考察虚继承的继承顺序。
        1. class B, class C为虚继承。
        2. 虚继承（class 派生类:virtual 继承方式 基类名）：从不同的路径继承过来的同名数据成员在内存中就只有一个拷贝。
        3. 继承顺序：
            执行基类构造函数，多个基类的构造函数按照被继承的顺序构造。
            执行成员对象的构造函数，多个成员对象的构造函数按照声明的顺序构造。
            执行派生类自己的构造函数。
        4. 要执行D构造函数必须先执行参数列表，欲构造B, C必须先构造A，其B, C虚继承A，所以只要执行一次构造函数。

#### Q 40 :

    题目：
        如下程序段：
        char a[] = "xyz", b[] = {'x', 'y', 'z'};
        if(strlen(a) > strlen(b))
            printf("a > b\n");
        else
            printf("a <= b\n");
        则程序输出：

    答案:
        a<=b

    解答:
        考察对strlen实现的理解。
        1. strlen函数判断字符串长仅仅是通过字符串末的'\0'（字符0）来确定。
        2. 数组b为标识具体'\0'位置，所以使用strlen结果至少大于等于3。

#### Q 41 :

    题目：
        执行以下语句，输出结果为：
        char *p1 = "hello";
        char *p2 = "world";
        char *p3 = "a piece of cake";
        char *str[] = {p1, p2, p3};
        printf("%c", *(str[0] + 1));

    答案:
        e

    解答:
        考察指针、指针函数及其操作。
        1. str是指针数组，每个元素都是指针。str[0]代表的是char *类型指针p1。
        2. p1是char *类型，只想字符串"hello"，所以这里“+1”代表以字符为单位，结果为'e'。

#### [Q 42](http://www.cnblogs.com/lizhenghn/p/3630405.html) :

    题目：
        以下表达式那些会被编译器禁止：
        int a = 248, b = 4;
        int const c = 21;
        const int *d = &a;
        int *const e = &b;
        int const * const f = &a;

    答案:
        *c = 32
        *d = 43
        e = &a
        f = 0x321f

    解答:
        考察指针常量、常量指针。
        1. 区分是指针的值不会变还是指针指向的变量值不会变。
        2. 方法：
            如果 const 位于 * 的左侧，则 const 就是用来修饰指针所指向的变量，即指针指向为常量。
            如果 const 位于 * 的右侧， const 就是修饰指针本身，即指针本身是常量。
        3. 方法使用：
            int const c -> 变量c的值不可改变。
            const int *d -> const在*左（离指针远），修饰指向的变量 -> 指针d指向的变量不可变。
            int *const e-> const在*右（例指针近），修饰指针 -> 指针e的值不可变。
            int const * const f -> 有左有右 -> 值和指针均不可变。

#### Q 43 :

    题目：
        以下描述正确的是：

    答案:
        虚函数不能是内联函数
        父类的析构函数是非虚的，但是子类的析构函数是虚的，delete子类对象指针会调用父类的析构函数

    解答:
        1. 虚函数不能是内联函数（编译时展开，必须有实体），不能是静态函数（属于自身类，不属于对象，而虚函数要求有实体），不能是构造函数（尚未建立虚函数表）。
        2. delete子类对象是一定会调用父类的析构函数的先调用子类的析构函数然后调用父类的析构函数。

#### Q 44 :

    题目：
        class Base{
        public:
            Base(){
                Init();
            }
            virtual void Init(){
                printf("Base Init\n");
            }
            void func(){
                printf("Base func\n");
            }
        };
        class Derived: public Base{
        public:
            virtual void Init(){
                printf("Derived Init\n");
            }
            void func(){
                printf("Derived func\n");
            }
        };
        int main(){
            Derived d;
            ((Base *)&d)->func();
            return 0;
        }

    答案：
        Base Init
        Base func

    解答：
        考察虚函数。
        1. 类Derived继承自Base，先调用基类构造函数Base()，再调用基类的init()，输出Base Init。
            构造子类对象，基类中不会调用子类的虚函数：
                基类构造函数 -> 子类构造函数
                子类还没有构造，还没有初始化，属于未初始化对象
                基类不会去调用子类虚函数（哪怕子类中确实声明为虚函数）
        2. 虽然 ((Base *)&d)->func()是虚函数调用的样子，但func()跟本没有被定义为虚函数，基类指针访问基类的func()。

#### Q 45 :

    题目：
        采用多路复用I/O监听3个套接字的数据时，如果套接字描述符分别是：5,17,19,则：
        select(int maxfd, struct fd_set* rdset, NULL, NULL)
        中的maxfd应取为：

    答案:
        20

    解答:
         1. maxfd是三个套接字描述符中最大数字加上1。

#### Q 46 :

    题目：

        下面的说法那个正确：
        #define NUMA 10000000
        #define NUMB 1000
        int a[NUMA], b[NUMB];
        void pa(){
            int i, j;
            for(i = 0; i < NUMB; ++i)
                for(j = 0; j < NUMA; ++j)
                    ++a[j];
        }
        void pb(){
            int i, j;
            for(i = 0; i < NUMA; ++i)
                for(j = 0; j < NUMB; ++j)
                    ++b[j];
        }

    答案:
        pb比pa快

    解答:
        1. 二维数组操作时，因为缓存的原因，外层放小循环，内层放大循环效率高。
        2. 个人觉得这题是因为在给大数组赋值时会发生缺页，而小数组赋值不会，所以pb比较快。

#### Q 47 :

    题目：
        看以下代码：
        class A{
        public:
            ~A();
        };
        A::~A(){
            printf("delete A");
        }
        class B : public A{
        public:
            ~B();
        };
        B::~B(){
            printf("delete B");
        }

        请问执行以下代码的输出是：
        A *pa = new B();
        delete pa;

    答案:
        delete A

    解答:
        考察虚析构函数。
        1. 若B *pb = new B()，则本题会同时输出delete A和delete B。
        2. 但如果delete的是一个指向派生类的基类指针，则需要虚构造函数。
        3. 这里基类没有定义虚析构函数，属未定义行为。

#### Q 48 :

    题目：
        下面程序输出结果为？
        #include<iostream.h>
        #define SUB(X,Y) (X)*Y
        int main(){
            int a = 3, b = 4;
            cout << SUB（a++, ++b);
            return 0;
        }

    答案：
        15

    解答：
        考察宏定义命令和自加运算。
        1. SUB(3++, ++4) = (3++) * ++4 = 3 * 5 =15
        2. 3++是否有括号并不影响，后置自增变量值在本条语句结束前（分号之前）均不改变。
        3. 前置自增值在本条语句内立即改变且自增优先级高于*。

#### Q 49 :

    题目：
        以下代码输出结果为：
        int main(){
            int a[2][5] = { {1, 2, 3, 4, 5}, {6, 7, 8, 9, 10} };
            int *ptr = (int *)(&a + 1);
            printf("%d\n", *(ptr - 3));
        }

    答案：
        8

    解答：
        考察指针运算。
        1. 首先需要明确a为二维数组，其类型是int **。
        2. 此时对a进行加1操作，操作的单位的每个一维数组。
        3. &a的单位可以理解为int ***，对其操作单位是整个二维数组。
        4. ptr = (int *)(&a + 1)指向的是二维数组后一个单元。
        5. 进行输出操作时，&a + 1被强制转换为int *类型，按四字节读取。
        6. ptr - 3从数组末尾回退3个int大小，指向数字8。

#### Q 50 :

    题目：
        在一台主流配置的PC机上，调用f(35)所需的时间大概是：
        int f(int x){
            int s = 0;
            while(x-- > 0)
                s += f(x);
            return max(s, 1);
        }

    答案：
        几分钟

    解答：
        考察递归时间复杂度。
        1.   O(f(n)) = O(f(n - 1)) + O(f(n - 2)) + ... + O(f(0))
           = 2*O(f(n - 2)) + 2*(O(f(n - 3)) + ... + 2*O(f(0))
           = 2^35*O(f(0))

#### Q 51 :

    题目：
        以下代码执行后，val的值是：
        unsigned long val = 0;
        char a = 0x48;
        char b = 0x52;
        val = b << 8 | a;

    答案：
        21064

    解答：
        考察默认类型转换和移位运算及优先级。
        1. 移位运算>>优先级高于|，val = (b << 8) | a;
        2. 类型转换：
            运算前转换：
                char/short -> int
                float -> double
                这里的b << 8计算时先需要把char转为int（b << 8 = 0x00005200, a = 0x00000048）
            运算中转换：
                int -> long -> unsigned -> double
        3. val = 0x00005248 =  21064

#### Q 52 :

    题目：
        下列说法错误的有：

    答案：
        在类方法中可用this来调用本类的类方法。
        在类方法中只能调用本类中的类方法。
        在类方法中绝对不能调用实例方法。

    解答：
        1. 成员方法又称为实例方法，静态方法又称为类方法。
        2. 类方法（静态方法）不属于特定的类，没有this指针。
        3. 可以通过类名作用域的方式调用ClassName::fun()。
        4. 类中申请一个类对象或者参数传递一个对象或者指针都可以调用实例方法。

#### Q 53 :

    题目：
        typedef struct{
            char flag[3];
            short value;
        }sampleStruct;
        union{
            char flag[3];
            short value;
        }sampleUnion;
        假设sizeof(char)=1，sizeof(short)=2
        那么sizeof(sampleStruct) = ()
            sizeof(sampleUnion) = ()

    答案：
        6
        4

    解答：
        考察结构体、联合体大小及对齐问题。
        1. union：sizeof的取值不仅考虑sizeof最大的成员，还要考虑对齐字节。
        2. 注：如果结构体内类型的最大字节小于系统位数对应的字节，那么按类型的最大字节对齐。所以本题结构体按2字节对齐。

#### Q 54 :

    题目：
        以下程序的输出结果为：
        #include "stdio.h"
        int func(int x, int y){
            return (x + y);
        }
        int main(){
            int a = 1, b = 2, c = 3, d = 4, e = 5;
            printf(" %d\n", func((a + b, b + c, c + a), (d, e)));
            return 0;
        }

    答案：
        9

    解答：
        考察逗号运算符。
        1. 逗号表达式的结果是其最右边表达式的值。
        2. (a + b, b + c, c + a)取最右边的值c + a = 4，(d, e)取最右边的值e = 5。

#### Q 55 :

    题目：
        当参数*x=1, *y=1, *z=1时，下列不可能是函数add的返回值的：
        int add(int *x, int *y, int *z){
            *x += *x;
            *y += *x;
            *z += *y;
            return *z;
        }

    答案：
        7

    解答：
        1. 此题考虑x, y, z是否可能指向同一个变量。
        2. 可能情况：
            x, y, z指向同一区域：8
            x, y指向同一区域：5
            x, z指向同一区域：5
            y, z指向同一区域：6
            x, y, z指向不同区域：4

#### Q 56 :

    题目：
        以下涉及到内存管理的代码段中，有错误的是：

    答案：
        1. int *a = new int(12);
           free(a);
        2. int *ip = static_cast<int*>(malloc(sizeof(int)));
           *ip = 10;
           delete ip;
        3. int *ip = new int(12);
           for(int i = 0; i < 12; ++i)
               ip[i] = i;
           delete []ip;

    解答：
        考察动态内存分配与释放。
        1. malloc和free，new和delete配套使用。
        2. int *ip = new int(12)：动态分配一个int类型变量并赋值为12，ip指向这个变量。
        3. int *ip = new int[12]，表示分配大小为12的int类型数组，ip指向这个数组。

#### Q 57 :

    题目：
        关于内联函数正确的是：

    答案：
        在所有类说明中内部定义的成员函数都是内联函数

    解答：
        考察内联函数。
        1. 见答案。
        2. 使用内联函数的地方会在编译阶段用内联函数体替换掉。

#### Q 58 :

    题目：
        以下函数中，和其他函数不属于一类的是：

    答案：
        pwrite

    解答：
        考察系统调用和库函数。
        1. 常见文件系统的系统函数：
            fcntl 文件控制
            open 打开文件
            creat 创建新文件
            close 关闭文件描述字
            read 读文件
            write 写文件
            read 从文件读入数据到缓冲数组中
            write 将缓冲数组里的数据写入文件
            pread 对文件随机读
            pwrite 对文件随机写

#### Q 59 :

    题目：
        std::vector::iterator重载了下面哪些运算符：

    答案：
        ++
        ==
        *

    解答：
        考察迭代器基本概念。
        1. ++和--用于迭代器以后移动。
        2. ==用于判断迭代器是否相等。
        3. *用于对迭代器指向的变量的引用。

#### Q 60 :

    题目：
        请问func(2012,2102)的结果是：
        int fuc(int m,int n){
            if(m%n == 0)
                return n；
            else
                return fuc(n,   m%n)；
        }

    答案：
        2

    解答：
        考察辗转相除法。
        1. 辗转相除法求两个数的最大公约数。

#### Q 61 :

    题目：
        class Eye{
            public:
            void Look(void);
        };
        现在希望定义一个Head类，也想实现Look的功能，应该使用()方法，实现代码重用。

    答案：
        组合

    解答：
        考察组合和继承的使用场景。
        1. 继承是细化的继承公共的，被继承的基类是抽象出的公共部分。
        2. 组合就是在定义类时直接在新类中以原有类的对象作为数据成员。
        3. 继承是派生类对基类的扩展和包含，组合是原有类被包含，这里Eye应该被包含在Head内。
        4. 优先使用对象组合，而不是继承。

#### Q 62 :

    题目：
        设m和n都是int类型，那么以下for循环语句：
        for(m = 0, n = -1; n = 0; m++, n++)
            n++;

    答案：
        循环体一次也不执行

    解答：
        1. 见答案。

#### Q 63 :

    题目：
        #pragma pack(2)
        class BU{
            int number;
            union UBffer{
                char buffer[13];
                int number;
            }ubuf;
            void foo(){}
            typedef char*(*f)(void*);
            enum{hdd,ssd,blueray}disk;
        }bu;

    答案：
        22

    解答：
        考察结构体、联合体、枚举大小综合题。
        1. pack(2)，所以int num和union大小分别为4和14没有疑问。
        2. 其他部分：
            void foo(){}：0。
            typedef char*(*f)(void*)：0。
            enum{hdd,ssd,blueray}disk：4。
            无虚函数，不存在虚函数指针的4字节。
        3. 枚举类型的sizeof值都是4。

#### Q 64 :

    题目：
        设变量已正确定义，以下不能统计出一行中输入字符个数（不包含回车符）的程序段是：

    答案：
        int n = 0;
        for(ch = getchar(); ch != '\n'; n++);

    解答：
        1. 对于for循环，其初始条件只执行一次，因此ch只从输入流中取一个字符，之后就再不会取字符，因此会死循环。
        2. int n = 0; while(getchar() != '\n') n++;和int n = 0; while(ch = getchar() != '\n') n++;均可。

#### Q 65 :

    题目：
        假设下面的函数foo会被多线程调用，那么让i、j、k三个变量哪些因为线程间共享访问需要加锁保护:
        int i = 0;
        void foo(){
            static int j = 0;
            int k = 0;
            i++;
            j++;
            k++;
        }

    答案：
        i和j

    解答：
        考察多线程情况下数据加锁问题。
        1. 多线程调用时要进行保护时，主要是针对全局变量和静态变量（无论局部或全局）的，函数内的局部变量不会受到影响。
        2. i是全局变量，j是静态局部变量。

#### Q 66 :

    题目：
        在C++面向对象编程语言中，以下阐述不正确的是：

    答案：
        接口中可以用虚方法
        接口中可以包含已经实现的方法

    解答：
        考察C++抽象类。
        1. 接口是一个概念，它在C++中用抽象类来实现。
        2. 抽象类必须是纯虚函数。

#### [Q 67](http://www.cnblogs.com/wangguchangqing/p/6141743.html) :

    题目：
        #include<iostream>
        using namespace std;
        class MyClass{
        public:
            MyClass(int i = 0){
                cout << i;
            }
            MyClass(const MyClass &x){
                cout << 2;
            }
            MyClass &operator=(const MyClass &x){
                cout << 3;
                return *this;
            }
            ~MyClass(){
                cout << 4;
            }
        };
        int main(){
            MyClass obj1(1), obj2(2);
            MyClass obj3 = obj1;
            return 0;
        }
        运行时的输出结果是：

    答案：
        122444

    解答：
        考察拷贝构造函数和赋值运算符的区别。
        1. 前两个1和2没有疑问，最后三个析构输出4也没有疑问。
        2. C MyClass obj3 = obj1;
            若obj3还不存在，调用拷贝构造函数输出2。
            若obj3存在，obj3 = obj，则调用赋值运算符重载函数。

#### Q 68 :

    题目：
        有以下程序:
        #include < stdio.h >
        int main(){
            char a[5][10] ={"one", "two", "three", "four", "five"};
            int i, j;
            char t;
            for (i = 0; i < 2; i++){
                for(j = i + 1; j < 5; j++ ){
                    if(a[i][0] > a[j][0]){
                        t = a[i][0];
                        a[i][0] = a[j][0];
                        a[j][0] = t;
                    }
                }
            }
            puts(a[1]);
        }

    答案：
        fwo

    解答：
        1. 对五个字符串首字母进行冒泡排序。

#### Q 69 :

    题目：
        两个等价线程并发的执行下列程序，a为全局变量，初始为0，假设printf、++、--操作都是原子性的，则输出肯定不是哪个：

    答案：
        0
        1

    解答：
        考察多线程并发结果。

#### Q 70 :

    题目：
        在32位机器上，设有以下说明和定义：
        typedef union{
            long i;
            int k[5];
            char c;
        }DATE;
        struct data{
            int cat;
            DATE cow;
            double dog;
        }too;
        DATE max;
        则sizeof(struct data) + sizeof(max)的执行结果是：

    答案：
        52

    解答：
        1. 联合体大小为20。
        2. 如果最大的基本元素小于等于机器位宽，按照最大基本元素大小对齐，否则按照机器字长对齐，此处对齐单位为4字节。

#### [Q 71](http://blog.csdn.net/sunxx1986/article/details/6619144) :

    题目：
        若PAT是一个类，则程序运行时，语句“PAT(*ad)[3];”调用PAT的构造函数的次数是：

    答案：
        0

    解答：
        考察数组指针。
        1. PAT *at[3]表示指针数组，本质是数组，数组元素是指向PAT的指针，数组大小为3。
        2. PAT(*ad)[3]表示数组指针，本质是指针，但该指针指向的是一个数组且数组大小为3。
        3. 同理，int *fun()是指针函数，int (*fun)()是函数指针。
        4. 这里并未构造PAT对象。

#### [Q 72](http://blog.csdn.net/zerocboy/article/details/51638828) :

    题目：
        下面有关volatile说法正确的有：

    答案：
        当读取一个变量时，为提高存取速度，编译器优化时有时会先把变量读取到一个寄存器中，以后再取变量值时，就直接从寄存器中取值
        优化器在用到volatile变量时必须每次都小心地重新读取这个变量的值，而不是使用保存在寄存器里的备份
        volatile适用于多线程应用中被几个任务共享的变量

    解答：
        考察volatile关键字修饰的变量。
        1. volatile作用是避免编译器优化，它是随时会变的。和const不矛盾，被const修饰的变量只是在当前作用范围无法修改，但是可能被其它程序修改。
        2. const volatile int i = 0; 表示：任何对i的直接修改都是错误的，但是i可能被意外情况修改掉。

#### Q 73 :

    题目：
        C++中32位单精度浮点数能表示的十进制有效数字是多少位：

    答案：
        7

    解答：
        考察浮点数表示。
        1. float浮点数含有1bit符号位，8bit阶码，23bit位尾数，加上隐藏位的1，实际可直接表示的数在2^24以内。
        2. float可以表示的十进制有效数字7位，double为16位。

#### Q 74 :

    题目：
        下列关于赋值运算符“=”重载的叙述中，正确的是：

    答案：
        赋值运算符只能作为类的成员函数重载

    解答：
        1. 不能被重载的运算符：
            ::  ,  *  .  ?  :
        2. 必须作为成员函数重载的运算符：
            =  []  ()  ->

#### Q 75 :

    题目：
        函数fun的声明为int fun(int *p[4]),以下哪个变量可以作为fun的合法参数：

    答案：
        int **a

    解答：
        1. 函数参数为指针数组，数组大小为4。
        2.

#### Q 76 :

    题目：
        char* getmemory(void){
            char p[] = "hello world";
            return p;
        }
        void test(void){
            char *str = NULL;
            str = getmemory();
            printf(str);
        }
        请问运行Test函数会有什么样的结果

    答案：
        输出乱码

    解答：
        1. p是数组，是局部变量。
        2. 数组p的生命周期仅存在于getmemory函数中。
        3. 返回的指针指向的数据已经在调用结束后被销毁，输出乱码。
        4. 可以通过编译，只不过结果非预期。

#### [Q 77](http://www.cnblogs.com/always-chang/p/6107437.html) :

    题目：
        关于浅复制和深复制的说法，下列说法正确的是：

    答案：
        浅层复制：只复制指向对象的指针，而不复制引用对象本身。
        深层复制：复制引用对象本身。
        如果是深拷贝，修改一个对象不会影响到另外一个对象。

    解答：
        考察深拷贝和浅拷贝。
        1. 对象里有指针时，浅拷贝只拷贝指针字面值，并不拷贝指针指向的内容。
        2. 深拷贝会重新分配一块空间，并把被拷贝对象中指向的数据逐一复制过去。
        3. 对象中有指针时，使用深拷贝。
        4. 因为拷贝者和被拷贝里的指针指向同一区域，所以任意一个对数据的修改都会影响到另一个。

#### Q 78 :

    题目：
        STL中的一级容器有：

    答案：
        vector, deque, list

    解答：
        考察STL容器概念。
        1. STL中一级容器是容器元素本身是基本类型，非组合类型。

#### Q 79 :

    题目：
        程序出错在什么阶段：
        int main(void){
            http://www.taobao.com
            cout << "welcome to taobao" << endl;
        }

    答案：
        正常运行

    解答：
        这题有毒。
        1. //后面被当做注释了。
        2. http本身是label。

#### Q 80 :

    题目：
        有如下程序段：
        class A{
            int _a;
        public:
            A(int a): _a(a){}
            friend int f1(A &);
            friend int f2(const A &);
            friend int f3(A);
            friend int f4(const A);
        };

    答案：
        f1(0)

    解答：

#### Q 81 :

    题目：
        下面叙述错误的是：
         char acX[] = "abc";
         char acY[] = {'a', 'b', 'c'};
         char *szX = "abc";
         char *szY = "abc";

    答案：
        szX的内容修改后，szY的内容也会被更改

    解答：
        考察常量区字符串。
        1. szX和szY的值（指向的位置）相同。
        2. szX和szY指向的"abc"定义在常量区，不可以修改。

#### Q 82 :

    题目：
        以下程序输出结果是：
        class A{
            public:
            virtual void func(int val = 1){
                std::cout << "A->" << val << std::endl;
            }
            virtual void test(){
                func();
            }
        };
        class B : public A{
            public:
                void func(int val = 0){
                    std::cout << "B->" << val << std::endl;
                }
        };
        int main(int argc ,char* argv[]){
            B*p = new B;
            p->test();
            return 0;
        }

    答案：
        B->1

    解答：
        1. 由于B类中没有覆盖（重写）基类中的虚函数test，指向派生类B的指针p调用继承自基类的函数test。
        2. test函数中继续调用虚函数func，因为虚函数执行动态绑定，p此时的动态类型为B*，因此执行的是B类中的func，输出"B->"。
        3. 缺省参数值是静态绑定，即此时val的值使用的是基类A中的缺省参数值，其值在编译阶段已经绑定，值为1。
        4. 结论：
            virtual函数是动态绑定，而缺省参数值却是静态绑定。
            绝不重新定义继承而来的缺省参数值。

#### Q 82 :

    题目：
        以下程序输出结果是：
        class A{
            public:
            virtual void func(int val = 1){
                std::cout << "A->" << val << std::endl;
            }
            virtual void test(){
                func();
            }
        };
        class B : public A{
            public:
                void func(int val = 0){
                    std::cout << "B->" << val << std::endl;
                }
        };
        int main(int argc ,char* argv[]){
            B*p = new B;
            p->test();
            return 0;
        }

    答案：
        B->1

    解答：
        1. 由于B类中没有覆盖（重写）基类中的虚函数test，指向派生类B的指针p调用继承自基类的函数test。
        2. test函数中继续调用虚函数func，因为虚函数执行动态绑定，p此时的动态类型为B*，因此执行的是B类中的func，输出"B->"。
        3. 缺省参数值是静态绑定，即此时val的值使用的是基类A中的缺省参数值，其值在编译阶段已经绑定，值为1。
        4. 结论：
            virtual函数是动态绑定，而缺省参数值却是静态绑定。
            绝不重新定义继承而来的缺省参数值。

---

## <span id = "prog">其他</span>

### 数据结构与算法

### 计算机网络

### 操作系统

#### Q 1 :

    题目：
        一次I/O操作的结束，有可能导致：

    答案：
        一个进程由睡眠变就绪

    解答：
        1. 独占设备：
            进程间互斥的访问这类设备，设备一旦被分配给某个进程，便由该进程独占。I/O操作后自然只有这个进程由等待进入就绪。
        2. 共享设备：
            一段时间内允许多个进程同时访问的设备。对I/O设备的访问是并发，而不是并行。一次I/O操作的结束，只是其对应的进程I/O操作的结束，只会唤醒这一个进程。

#### Q 2 :

    题目：

        在多道程序系统中，系统的现有空闲可用资源能否满足后备作业J的资源要求，是选择作业J进入内存的必要条件。

    答案：
        错

    解答：
        进入内存不一定有全部的资源。

#### Q 3 :

    题目：
        对进程和线程的描述，以下错误的是：

    答案：
        父进程里的所有线程共享相同的地址空间，父进程的所有子进程共享相同的地址空间
        改变进程里面主线程的状态会影响其他线程的行为，改变父进程的状态不会影响其他子进程
        多线程会引起死锁，而多进程则不会

    解答：
        1. 子进程拥有独立的地址空间。
        2. 多进程也会死锁。

#### Q 4 :

    题目：
        在下列说法中，哪个是错误的：

    答案：
        若进程A和进程B在临界段上互斥，那么当进程A处于该临界段时，它不能被进程B中断
        虚拟存储管理中的抖动(thrashing)现象是指页面置换(page replacement)时用于换页的时间远多于执行程序的时间


    解答：
        1. A进程是可以被B进程中断的，只是B不能进入临界区。
        2. 页面抖动现象是由于分配给进程的内存空间过小 + 不合理的置换算法导致的。

#### Q 5 :

    题目：
        关于读写锁的描述，以下正确的是：

    答案：
        读写锁在读加锁的状态下，可用进行读共享

    解答：
        1. 写加锁状态时，其他进行写操作线程会阻塞。
        2. 写锁就是防止其他进程读或写. 读锁就是防止在读的时候有写进程进入。

#### Q 6 :

    题目：
        采用可重定位分区分配方式：

    答案：
        解决了碎片问题

    解答：
        1. 通过移动内存中作业的位置，把原来多个分散的小分区拼接成一个大分区的方法称为拼接或紧凑。

#### Q 7 :

    题目：
        若一个用户进程通过read 系统调用读取一个磁盘文件中的数据，则下列关于此过程的叙述中，正确的是：
        Ⅰ. 若该文件的数据不在内存中，则该进程进入睡眠等待状态
        Ⅱ. 请求read系统调用会导致CPU从用户态切换到核心态
        Ⅲ. read系统调用的参数应包含文件的名称

    答案：
        Ⅰ & Ⅱ

    解答：
        1. 通过移动内存中作业的位置，把原来多个分散的小分区拼接成一个大分区的方法称为拼接或紧凑。
        2. open系统调用应该包含文件的名称，read只是包含输入流。

#### Q 8 :

    题目：
        在Bash中，以下哪些说法是正确的：

    答案：
        $?表示前一个命令的返回值
        $#表示参数的数量

    解答：
        1. $# 是传给脚本的参数个数
        2. $0 是脚本本身的名字
        3. $1 是传递给该shell脚本的第一个参数
        4. $2 是传递给该shell脚本的第二个参数$@ 是传给脚本的所有参数的列表
        5. $* 是以一个单字符串显示所有向脚本传递的参数，与位置变量不同，参数可超过9个
        6. $$ 是脚本运行的当前进程ID号$? 是显示最后命令的退出状态，0表示没有错误，其他表示有错误

#### Q 9 :

    题目：
        下列关于线程调度的叙述中，错误的是：

    答案：
        调用线程的yeild()方法，只会使与当前线程相同优先级的线程获得运行机会
        具有相同优先级的多个线程的调度一定是分时的

    解答：

        1. yeild()使当前线程进入就绪队列，给相同优先级或者高优先级线程机会。
        2. slssp()方法会给其他任何线程提供运行的机会，不论优先级高低均可以。

#### Q 10 :

    题目：
        在存储管理中，采用覆盖与交换技术的目的是:

    答案：
        减少程序占用的主存空间

    解答：
        1. 覆盖技术的实现是把程序划分为若干个功能上相对独立的程序段，按照其自身的逻辑结构使那些不会同时运行的程序段共享同一块内存区域。程序段先保存在磁盘上，当有关程序的前一部分执行结束后，把后续程序段调入内存，覆盖前面的程序段。
        2. 在分时系统中，用户的进程比内存能容纳的数量更多，系统将那些不再运行的进程或某一部分调出内存，暂时放在外存上的一个后备存储区，通常称为交换区，当需要运行这些进程时，再将它们装入内存。

#### Q 11 :

    题目：
        下面有关线程的说法错误的是：

    答案：
        每个线程有自己独立的地址空间
        线程包含CPU现场，可以独立执行程序

    解答：
        1. 在多线程中，多个线程共享一个进程中的地址空间。
        2. 线程是CPU调度的最小单位，但不能独立执行程序。

#### Q 12 :

    题目：
        关于子进程和父进程的说法，下面哪一个是正确的：

    答案：
        一个进程可以没有父进程或子进程

    解答：
        1. init进程就没有父进程。

#### Q 13 :

    题目：
        下面关于软连接的描述，正确的是：

    答案：
        软链接也叫符号链接
        如果原始文件被删除，所有指向它的软链接也都被破坏
        软链接指明了原始文件的位置，用户需要对原始文件的位置有访问权限才可以使用

    解答：
        1. 软链接克服了硬链接的不足，没有任何文件系统的限制，任何用户可以创建指向目录的符号链接。因而现在更为广泛使用，它具有更大的灵活性，甚至可以跨越不同机器、不同网络对文件进行链接。



## REF

- [Skill-Tree](https://github.com/linw7/Skill-Tree)
