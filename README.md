# 现代C++ 输出库

![License](https://img.shields.io/github/license/JustJuicer/Modern-Cout)
![Build Status](https://github.com/jujinqian162/Modern-Cout/actions/workflows/cmake-multi-platform.yml/badge.svg)
![Release](https://img.shields.io/github/v/release/JustJuicer/Modern-Cout)  

Modern cout 库是C++20一个单头文件的库，提供了方便快速的输出方式 \
与对大部分标准库类型的重载
# 环境
1.c++ 标准：**C++20标准**    

2.编译器要求： gcc最低版本**10**， clang最低版本**12**，msvc v19.30 VS17.0
# 示例
以下是使用示例
## 打印
### 标准库通用打印方式
```cpp
ju::cout << "hello world\n";  
ju::print << "hello world\n";
```
### 函数风格的打印

```cpp
ju::cout("hello ", "world\n");
ju::print("hello ", "world\n");
int val{ 3 };
ju::cout("val: ", val, "\n");
```

### 宏
```cpp
int var = 4;
PRTVAR(var);
// output: var = 4
LOGVAR(var); // 仅当定义DEBUG宏时打印
// output: var = 4 
```

### 换行与颜色
方便的使用多种颜色输出
```cpp
using namespace ju;

coutln("auto break"); 

cred("red, 红色输出\n");
cred << "red\n";

cgreen("green, 绿色输出\n");
cgreen << "green\n";

cblue("blue, 蓝色输出\n");
cblue << "blue\n";

// 支持用operator<<输出
// cxxx << "...";
```

### 多维range输出
支持递归的输出多维嵌套的数组，符合range概念并无法转为string类型的可迭代对象将遍历打印如下
```cpp
using namespace std;
using namespace ju;
coutln << vector{ vector{1, 2, 3}, 
                  vector{4, 5, 6}, 
                  vector{7, 8, 9} };
// output: 
//         [[1 2 3] 
//          [4 5 6] 
//          [7 8 9]]
```

### 标准库类输出重载
提供对tuple， pair， optional等类型的输出重载，可通过宏关闭重载
```cpp
using namespace ju;
coutln(std::vector{1, 2, 3, 4});
// output [1, 2, 3, 4]

coutln(make_tuple("first", "second", 1, 2.5));
// output (first, second, 1, 2.5)

coutln(make_pair("char*",2.5));
// output (char*, 2.5)

std::optional<int> option;
coutln(option);
// output null

std::optional<int> option1{4};
coutln(option1);
// output 4
```


### 可迭代对象输出重载
#### 自定义可迭代对象
自定义类在满足std::ranges::range且无转换为string时
cout 迭代输出元素
```cpp
struct iterable {
    struct iterator {
        using difference_type = ptrdiff_t;
        auto operator*() const { return a; }
        auto& operator++() {
            --a;
            return *this;
        }
        auto& operator++(int) {
            --a;
            return *this;
        }
        bool operator==(iterator iter) const {
            return a > 0 ? false : true;
        }
        inline static int a = 10;
    };
    auto begin(){ return iterator{}; }
    auto end()  { return iterator{}; }
};
using namespace ju;
int main(){
    iterable i;
    coutln(i);
    // output [10 9 8 7 6 5 4 3 2 1]
}


```
### 自定义重载字符串转换接口
#### 自定义重载toString接口
```cpp
struct S{
    std::string toString(){
        return "struct S";
    }
};
using namespace ju;
int main() {
    coutln(S{});
    // output struct S
}
```
#### 自定义重载operator<<
重载你自定义的类的输出流
```cpp
struct S{
    int a{ 5 };
    friend std::ostream& operator<<(std::ostream& os, S self){
        return os << "a = " << self.a;
    }
};
int main() {
    coutln(S{});
    //output a = 5
}
```

### 取消重载std类型输出重载
```cpp
#define NO_STD_TYPE_OVERLOAD
```
