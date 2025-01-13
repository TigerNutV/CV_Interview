# 模板编程

## 函数模板与类模板

**函数模板**是一种允许函数在不同的数据类型上工作的机制。通过模板参数来指定函数可以接受的数据类型。

语法：

```c++
template <template T>
返回类型 参数名（参数列表）{
    //函数实现；
}
template <template T>
T max(T a, T b){
    return (a > b)? a:b
}
int main (){
    int i = max(3,7); //实例化int类型的max函数
    double d = max(6.35, 3.12); //实例化double类型的max函数
    return 0;
}
```

**类模板**是允许类在不同的数据类型上工作的机制。通过模板参数指定类可以使用的数据类型

```c++
template <template T>
class 类名{
    // 类的成员变量和函数
}；
template <template T>
class Stack{
private:
    T* data;
    int top;
    int capacity;
public:
    Stack(int size) : data(new T[size]) : top(-1) : capacity(size){}
    ~Stack(){delete[] data;} //动态释放数组
    
    void push(T item){
        if (top<capacity-1){
            data[++top]= item;
        }
    }
    
    T pop(){
        if(top>=0){
            return data[top--];
        }
        throw std::out_of_range("Stack is empty");  //抛出异常 数组为空
    }
};

int main(){
    Stack<int> intStack(10); //实例化为int类型的stack类
    inStack.push(5);
    inStack.push(10);
    int num = inStack.pop(); //num = 10
    
    Stack<double> doubleStack(5); //实例化为double类型的stack类
    doubleStack.push(3.14);
    double num2 = doubleStack.pop(); //num2 = 3.14
    return 0;
}
```

总的来说，**模板函数**是模板化函数，在不同的数据类型上执行相同的操作。**模板类**是提供一个通用类的实现，用于封装和操作不同数据类型的类。



## 模板的偏特化和全特化（了解即可）

模板的偏特化是为模板的某些参数提供具体的实现，其他参数保持通用

模板的全特化是为模板提供一种特定的类型，对于模板函数需要重载来定义



## 模板元编程（如std::enable_if，std::tuple原理）

用代码生成代码，将程序视为数据，从而在程序编译动态更改程序的行为和结构。

### std::enable_if

`std::enable_if` 是一个条件编译时选择器，它根据给定的布尔条件来启用或禁用某个模板的特化。如果条件为真，`std::enable_if` 会生成一个类型，否则生成一个无效的类型（通常是由于SFINAE - Substitution Failure Is Not An Error原则）。

#### 语法

```cpp
std::enable_if<condition, Type>::type
```

- `condition`：一个布尔表达式。
- `Type`：如果条件为真，则`std::enable_if`会生成这个类型。

#### 示例

```cpp
template <typename T>
typename std::enable_if<std::is_integral<T>::value, T>::type
process(T val) {
    // 仅当T是整数类型时，此函数才可用
    return val * 2;
}

// 尝试调用 process 与非整数类型将导致编译错误
// int result = process(3.14);
```

在这个例子中，`process` 函数仅在模板参数 `T` 是整数类型时可用。如果尝试传递一个非整数类型的参数，编译器将生成一个错误。

### std::tuple

`std::tuple` 是C++11引入的一个固定大小的异构容器，它可以存储不同类型的值。`std::tuple` 利用模板元编程来实现对不同类型值的存储和访问。

#### 语法

```cpp
std::tuple<Types...>
```

- `Types...`：任意数量和类型的模板参数。

#### 示例

```cpp
#include <tuple>
#include <iostream>

int main() {
    std::tuple<int, double, std::string> myTuple(1, 2.5, "Hello");

    // 访问tuple中的元素
    std::cout << "Integer: " << std::get<0>(myTuple) << std::endl;
    std::cout << "Double: " << std::get<1>(myTuple) << std::endl;
    std::cout << "String: " << std::get<2>(myTuple) << std::endl;

    return 0;
}
```

在这个例子中，`myTuple` 是一个包含整数、浮点数和字符串的 `std::tuple` 对象。可以使用 `std::get` 模板函数来访问 `std::tuple` 中的元素。





## STL容器与算法

背景:STL Standard Template Library标准模板库，STL是C++中强大的模板类和函数的集合，容器与算法属于STL六大组件。六大组件包含容器、算法、迭代器、仿函数、适配器和空间配置器。

**容器**是STL中用于存储和管理的数据结构。常用的容器包含向量、列表、双端队列、集合、映射等(vector\list\deque\set\map)。容器分为序列式容器和关联式容器两大类。

​	序列式容器（如vector、deque、list）强调元素的顺序存储，允许通过索引访问元素；

​	关联式容器（如set、map、multiset、multimap）基于二叉树结构，存储键值对或无序数据，强调元素的排序和唯一性。

### 序列式容器

**vector**是连续的内存块，支持快速随机访问，但容量不足时，会出现翻倍增长，可用reserver提前分配空间；

**deque**是分段的内存块，支持快速随机访问，支持两端插入和删除元素，内存开销大于vector，中间插入不如list

```cpp
#include <deque>
#include <iostream>
int main (){
    std::deque<int> dq = {1,2,3};
    dq.push_front(0);
    dq.push_back(4);
    for(int num:dq){
        std::cout<<num<<" ";
    }
    return 0;
}
```

**list**是非连续内存，不支持快速随机访问，支持高效插入和删除元素，内存开销较大，每个节点存储额外的指针。



### 关联式容器

map和set是基于二叉树实现的，节点非连续内存，查、插入、删除的时间复杂度为Olog(N)，时间复杂度随着数据量N的增大呈对数增长。节点存储的信息可能较多，内存开销较大，适合顺序查询和存储的场景。通常map将额外信息存储在值中，而set将额外信息存储在键对上。

map额外信息存储在自定义结构体值中

```cpp
#include <iostream>
#include <map>
#include <string>
struct StudentInfo{
    std:;string name; //姓名
    float score; //成绩
};

int main (){
    std::map<int, StudentInfo> StudentMap;
    // insert info
    StudentMap[101] = {"Alice",88.5};
    StudentMap[102] = {"HUHU",99.99};
    //TRAVERSE INFO
    for (const auto &[id ,info]:StudentMap){
        std::cout<<"ID:"<<id<<"name:"<<info.name<<"score:"<<info.score<<std::endl;
    }
}
```

若map中的键需要特殊规则排序，可以通过自定义比较器实现（如字符串长度）

```cpp
#include <iostream>
#include <map>
#include <set>
#include <string>

//自定义比较器：按字符串长度排序
struct CompareByLength{
    bool operator()(const std::string&a ,const std::string&b) const{
        return a.size() < b.size(); //字符串短的排前面
    }
};
//函数指针：自定义比较函数
bool operator()(const std::string&a ,const std::string&b) const{
    return a.size() <b.size()
}

struct student{
    int id;
    std::string name;
    float score;
}

int main (){
    //map example
    std::map<std::string ,int,CompareByLength> wordCounts;
    //std::map<std::string , int , bool(*)(const std::string&, const std::string&)> wordCounts;
    wordCounts["apple"] = 5;
    wordCounts["banana"] = 3;
    for (const auto& [word,count]: wordCounts){
        std::cout<<word<<count<<std::endl;
    }
    
    //set example
    std::set<std::string, CompareByLength> words;
    std::set<student,CompareByLength> students;
    words.insert("apple");
    words.insert("banana");
    //students.insert({101, "Alice", 88.5});
    //students.insert({102, "Bob", 92.0});
    //students.insert({103, "Charlie", 85.0});
    for (const auto& word:words){
        std::cout<<word<<std::endl;
    }
    
}

```

提到比较器，比较器必须是自定义的一个类，并且重载operator()，重载的operator()返回值必须是bool变量，这种比较器适用于所有关联式容器，拥有自定义比较器也可实现排序和查找的功能。



迭代器用于遍历容器，对容器的元素进行访问和操作，是一种指针抽象，封装了容器内的遍历逻辑，为算法提供统一的接口

迭代器分为五种类型：输入迭代器、输出迭代器、前向迭代器、双向迭代器、随机访问迭代器。

输入迭代器用于只读遍历，支持从前向后单次遍历，如：istream_iterator。

输出迭代器用于只写遍历，支持从前向后单次遍历，如：ostream_iterator。

前向迭代器支持多次遍历和读写操作，如forwar_list迭代器。

双向迭代器支持前后遍历和读写操作，如std::list/std::set/std::map迭代器。

随机访问迭代器支持任意位置访问，如std::vector/std::deque迭代器。



通过容器的方法获取迭代器（begin()和end()），通过++或--移动迭代器通过解引用*访问元素

```cpp
#include <iostream>
#include <vector>
int main(){
    std::vector<int> vec = {1, 2, 3, 4};
    //使用普通迭代器遍历
    for(std::vector<int>::iterator it = vec.begin();it != vec.end();++it){
        std::cout<< *it <<" "
    }
    std::cout<<std::endl;
    //for循环基于迭代器实现
    for(int val : vec){
        std::cout<<val<<" ";
    }
    std::cout<<std::endl;
    
    //反向遍历
    for(std::vector<int>::reverse_iterator rit = vec.rbegin();rit != vec.rend();++rit){
        std::cout<<*rit<<" "
    }
    //修改容器里的值
    for(std::vector<int>::iterator it = vec.begin();it != vec.end();++it){
        *it *= 2;
    }
    return 0;
}
```

注意：

1. 增删容器中的元素可能导致迭代器的失效。当迭代器失效后，重新获取或者使用 it = container.erase(it);重新获取迭代器；
2. 输入迭代器和输出迭代器 istream_iterator/ostream_iterator只允许++，不允许--和随机访问。（通常使用*访问元素）



**智能指针**：主要类型std::unique_ptr、std::shared_ptr、std::weak_ptr三种类型，注意unique_ptr可以用move转移所有权

移动语义是一种优化机制，减少拷贝动作，允许在资源管理中转移资源的所有权，原位置被清空或销毁。std::move显示将一个对象转到右值，右值引用实现移动语义，左值仍然保留。

```cpp
#include <iostream>
#include <memory>
int main(){
    //create unique_ptr
    std::unique_ptr<int> ptr1 = std::make_unique<int>(10);
    std::cout<<*ptr1<<std::endl;
    
    //转移所有权，移动语义move显式将左值转换为右值
    std::unique_ptr<int> ptr2 = std::move(ptr1);
    //ptr1 is empty
    if(!ptr1){
        std::cout<<"ptr1 is now empty"<<std::endl;
    }
    std::cout<<*ptr2<<std::endl;
    int a = 10;
    int a = b+1;
    // 移动语义
    int&& x = std:::move(a);
    int&& x = b+1;
    int&& x = 10;
    //ptr1.use_count()   引用计数
    //ptr1.reset(); // ptr1引用计数从1减少到 0，自动释放资源
}
```





## 多线程（std::thread / std::async）

**std::thread**标准线程库，用于创建和管理独立的线程。std::thread构造函数，创建线程并指定入口函数；.join()等待线程执行完毕；.detach()分离线程，允许线程独立运行。

**std::async**提供了一种更高层次的并发机制，返回一个std::future获取异步操作的返回值。

**互斥锁**是C++提供的一种线程同步工具，防止多个线程同时访问共享资源而导致数据竞争。

```cpp
#include <iostream>
#include <thread>
#include <future>
#include <mutex>
#include <memory>

std::mutex mtx;   // create mutex lock
int counter = 0; //share memory
void task(int id){
    std::cout<<"Thread: "<<id<<"is running.\n";
}
int task2(int x){
    return x*x;
}

void increment(){
    for(int i =0;i<1000;++i){
        mtx.lock();
        //std::lock_guard<std::mutex> lock<mtx>;  //自动加锁和解锁，作用域结束后自动解锁
        ++countor;
        mtx.unlock();
    }
}

 //死锁问题：两个线程互相等待对方释放锁，从而导致程序无法进行
std::mutex mtx1,mtx2;
void thread1(){
    std::lock_guard<std::mutex> lock1(mtx1);
    std::this_thread::sleep_for(std::chrono::milliseconds(100));
    std::lock_guard<std::mutex> lock2<mtx2>;
    std::cout<<"Thread1 1 finished"<<std::endl;
}

void thread2(){
    std::lock_guard<std::mutex> lock2<mtx2>;
    std::this_thread::sleep_for(std::chrono::milliseconds(100));
    std::lock_guard<std::mutex> lock1<mtx1>;
    std::cout<<"Thread2 2 finished"<<std::endl;
}

//递归多次调用锁的问题
std::recursive_mutex rmtx;  //是一种允许同一线程多次锁定的互斥锁，适用于递归函数或重复加锁的场景
void recursiveFunction(int count){
    if(count<0) return;
    rmtx.lock();
    std::cout<<"Count: "<<count<<std::endl;
    recursiveFunction(count-1);
    rmtx.unlock();
}

//智能指针相互引用问题
void increment(std::weak_ptr<int> weakResource, const std::string& threadName) {
    for (int i = 0; i < 5; ++i) {
        if (auto sharedResource = weakResource.lock()) { // auto尝试提升为 shared_ptr
            std::lock_guard<std::mutex> lock(resourceMutex);
            ++(*sharedResource);
            std::cout << threadName << " incremented value to " << *sharedResource << std::endl;
        } else {
            std::cout << threadName << " failed to access resource (expired)." << std::endl;
        }
        std::this_thread::sleep_for(std::chrono::milliseconds(100));
    }
}

int main(){
    std::thread t1(task,1); //创建线程t1
    std::thread t2(task,2); //创建线程t2
    
    t1.join(); //等待t1执行完成
    t2.join(); //等待t2执行完成
    
    //std::async接收函数和参数，返回std::future，函数返回什么类型就用什么类型
    std::future<int> result = std::async(task2,5);
    result.get(); //获取结果，确保任务完成，若尚未完成，会阻塞到结果可用
    
    
    std::thread t3(increment);
    std::thread t4(increment);
    
    t3.join();
    t4.join();
    
    std::thread t5(thread1);
    std::thread t6(thread2);
    
    t5.join();
    t6.join();
    //t5 t6的运行程序会死锁，线程1 和线程2互相等待对方释放锁
   
    std::thread t7(recursiveFuncion,5);
    
    t7.join();
    
    auto sharedResource = std::make_shared<int>(0); // 共享资源
    std::weak_ptr<int> weakResource = sharedResource; // 创建弱引用

    std::thread t8(increment, weakResource, "Thread 1");
    std::thread t9(increment, weakResource, "Thread 2");

    t8.join();
    t9.join();

    std::cout << "Final value of sharedResource: " << *sharedResource << std::endl;
    return 0;
    
}
```

