# 壹·引用与致谢

本文由https://io.zouht.com/154.html 补充与精简而来 遵守[CC-BY-SA-4.0 license](https://github.com/guoweiyi/notes-backup?tab=readme-ov-file#)原则

同时对作者@[ChrisKim](https://space.bilibili.com/23986264)表示感谢 您的视频教程非常详细与易懂

**C++ 标准模板库 (STL, Standard Template Library)**：包含一些常用数据结构与算法的模板的 C++ 软件库。

# 贰·常用容器


## 2.1 向量 vector

**`#include <vector>`**

连续的顺序的储存结构（和数组一样的类别），但是有长度可变的特性。

时间复杂度：$O(n)$

```cpp
vector<int> arr;
arr.push_back(3);// 尾接
arr.pop_back();  // 尾删

arr.size()

arr.resize(5,3); //改长赋初值 改短直接删

arr.empty(); // 如果是空返回 `true` 反之返回 `false`.

arr.clear();

for (auto ele : arr) cout << ele << endl;
for (int i = 0; i < arr.size(); i++) cout << a[i] << endl;
```

尾接 & 尾删 时间复杂度：均摊 $O(1)$

清空 时间复杂度：$O(n)$

改变长度 时间复杂度：$O(n)$

一般情况 `vector` 可以替换掉普通数组，除非该题**卡常（指卡时间复杂度常数**。

### 注意事项

#### 提前指定长度

如果长度已经确定，那么应当直接在构造函数指定长度，而不是一个一个 `.push_back()`. 因为 `vector` 额外内存耗尽后的重分配是有时间开销的，直接指定长度就不会出现重分配了。

```cpp
// 优化前: 522ms
vector<int> a;
for (int i = 0; i < 1e8; i++)
    a.push_back(i);
// 优化后: 259ms
vector<int> a(1e8);
for (int i = 0; i < a.size(); i++)
    a[i] = i;
```

#### 当心 size_t 溢出

vector 获取长度的方法 `.size()` 返回值类型为 `size_t`，通常 OJ 平台使用的是 32 位编译器（有些平台例如 cf 可选 64 位），那么该类型范围为 $[0,2^{32})$.

```cpp
vector<int> a(65536);
long long a = a.size() * a.size(); // 直接溢出变成0了
```

## 2.2 栈 stack

**`#include <stack>`**

通过二次封装双端队列 (deque) 容器，实现先进后出的栈数据结构。

### 常用方法

| 作用                   | 用法              | 示例                 |
| ---------------------- | ----------------- | :------------------- |
| 构造                   | `stack<类型> stk` | `stack<int> stk;`    |
| 进栈                   | `.push(元素)`     | `stk.push(1);`       |
| 出栈                   | `.pop()`          | `stk.pop();`         |
| 取栈顶                 | `.top()`          | `int a = stk.top();` |
| 查看大小 / 清空 / 判空 | 同                | 同                   |
| Stk.clear()            | 错误              | 错误                 |

### 注意事项

#### 不可访问内部元素！

**下面都是错误用法**

```cpp
for (int i = 0; i < stk.size(); i++)
    cout << stk[i] << endl;
for (auto ele : stk)
    cout << stk << endl;
```

## 2.3 队列 queue

**`#include <queue>`**

通过二次封装双端队列 (deque) 容器，实现先进先出的队列数据结构。

### 常用方法

| 作用                   | 用法              | 示例                   |
| ---------------------- | ----------------- | ---------------------- |
| 构造                   | `queue<类型> que` | `queue<int> que;`      |
| 进队                   | `.push(元素)`     | `que.push(1);`         |
| 出队（出的是队首       | `.pop()`          | `que.pop();`           |
| 取队首                 | `.front()`        | `int a = que.front();` |
| 取队尾                 | `.back()`         | `int a = que.back();`  |
| 查看大小 / 清空 / 判空 | 同                | 同                     |

### 注意事项

不可访问内部元素！**同栈stack**

## 2.4 优先队列 priority_queue

**`#include <queue>`**

提供常数时间的最大元素查找，对数时间的插入与提取，底层原理是二叉堆。

### 常用方法

**`priority_queue<类型, 容器, 比较器> pque`**

```cpp
priority_queue<int> pque1;                            // 储存int的大顶堆
priority_queue<int, vector<int>, greater<int>> pque2; // 储存int的小顶堆
```

| 作用            | 用法          | 示例                 |
| --------------- | ------------- | -------------------- |
| 进堆            | `.push(元素)` | `que.push(1);`       |
| 出堆            | `.pop()`      | `que.pop();`         |
| 取堆顶          | `.top()`      | `int a = que.top();` |
| 查看大小 / 判空 | 略            | 略                   |

进出队复杂度 $O(\log n)$，取堆顶 $O(1)$.

### 适用情形

持续维护元素的有序性：每次向队列插入大小不定的元素，或者每次从队列里取出大小最小/最大的元素，元素数量 $n$，插入操作数量 $k$.

- 每次插入后进行快速排序：$k\cdot n\log n$
- 使用优先队列维护：$k\cdot\log n$

### 注意事项

#### 仅堆顶可读

只可访问堆顶，其他元素都无法读取到。**下面是错误用法：**

```cpp
cout << pque[1] << endl;
```

#### 所有元素不可写

堆中所有元素是不可修改的。

如果你恰好要修改的是堆顶元素，那么是可以完成的：

```cpp
int tp = pque.top();
pque.pop();
pque.push(tp + 1);
```

## 2.5 集合 [set](https://zh.cppreference.com/w/cpp/container/set)

**`#include <set>`**

提供对数时间的插入、删除、查找的集合数据结构。底层原理是红黑树。

| 集合三要素 | 解释                           | set           | multiset      | unordered_set |
| ---------- | ------------------------------ | ------------- | ------------- | ------------- |
| 确定性     | 一个元素要么在集合中，要么不在 | ✔             | ✔             | ✔             |
| 互异性     | 一个元素仅可以在集合中出现一次 | ✔             | ❌（任意次）   | ✔             |
| 无序性     | 集合中的元素是没有顺序的       | ❌（从小到大） | ❌（从小到大） | ✔             |

### 常用方法

**`set<类型, 比较器> st`**

- 类型：要储存的数据类型
- 比较器：比较大小使用的比较器，默认为 `less<类型>`，可自定义

```cpp
set<int> st1;               // 储存int的集合（从小到大）
set<int, greater<int>> st2; // 储存int的集合（从大到小）

if(st.find(1) != st.end()) cout << "yes" << endl; // 查找
```

可使用迭代器进行遍历：

```cpp
for (set<int>::iterator it = st.begin(); it != st.end(); ++it) cout << *it << endl;
for (auto it = st.begin(); it != st.end(); ++it) cout << *it << endl;
```

基于范围的循环（C++ 11）：

```cpp
for (auto &ele : st) cout << ele << endl;
```

| 作用                        | 用法            | 示例                    |
| --------------------------- | --------------- | ----------------------- |
| 插入元素                    | `.insert(元素)` | `st.insert(1);`         |
| 删除元素                    | `.erase(元素)`  | `st.erase(2);`          |
| 查找元素                    | `.find(元素)`   | `auto it = st.find(1);` |
| 判断元素是否存在（只有1/0） | `.count(元素)`  | `st.count(3);`          |
| 查看大小 / 清空 / 判空      | 同              | 同                      |

增删查时间复杂度均为 $O(\log n)$

### 适用情形

- 元素去重：$[1,1,3,2,4,4]\to[1,2,3,4]$
- 维护顺序：$[1,5,3,7,9]\to[1,3,5,7,9]$

### 注意事项

#### 不存在下标索引

set 虽说可遍历，但仅可使用迭代器进行遍历，它不存在下标这一概念，无法通过下标访问到数据。

#### 元素只读

set 的迭代器取到的元素是只读的（因为是 const 迭代器），不可修改其值。如果要改，需要先 erase 再 insert. **下面是错误用法：**

```cpp
cout << *st.begin() << endl; // 正确。可读。
*st.begin() = 1;             // 错误！不可写！
```

#### 不可用迭代器计算下标

set 的迭代器不能像 vector 一样相减得到下标。**下面是错误用法：**

```cpp
auto it = st.find(2);      // 正确，返回2所在位置的迭代器。
int idx = it - st.begin(); // 错误！不可相减得到下标。
```

## 2.6 映射 map

**`#include <map>`**

提供对数时间的有序键值对结构。底层原理是红黑树。

| 性质   | 解释                         | map           | multimap      | unordered_map |
| ------ | ---------------------------- | ------------- | ------------- | ------------- |
| 互异性 | 一个键仅可以在映射中出现一次 | ✔             | ❌（任意次）   | ✔             |
| 无序性 | 键是没有顺序的               | ❌（从小到大） | ❌（从小到大） | ✔             |

### 常用方法

**`map<键类型, 值类型, 比较器> mp`**

```cpp
map<int, int> mp1;               // int->int 的映射（键从小到大）
map<int, int, greater<int>> st2; // int->int 的映射（键从大到小）
```

可使用迭代器进行遍历：

```cpp
for (map<int, int>::iterator it = mp.begin(); it != mp.end(); ++it)
    cout << it->first << ' ' << it->second << endl;
```

基于范围的循环（C++ 11）：

```cpp
for (auto &pr : mp) cout << pr.first << ' ' << pr.second << endl;
```

| 作用                                 | 用法           | 示例                    |
| ------------------------------------ | -------------- | ----------------------- |
| 增 / 改 / 查元素                     | 中括号         | `mp[1] = 2;`            |
| 查元素（返回迭代器）                 | `.find(元素)`  | `auto it = mp.find(1);` |
| 删除元素                             | `.erase(元素)` | `mp.erase(2);`          |
| 存在？普通 map 有互异性 只能返回 1/0 | `.count(元素)` | `mp.count(3);`          |
| 查看大小 / 清空 / 判空               | 略             | 略                      |

增删改查时间复杂度均为 $O(\log n)$

### 适用情形

需要维护映射的场景可以使用：输入若干字符串，统计每种字符串的出现次数。(`map<string, int> mp`)

### 注意事项

#### 中括号访问时默认值

如果使用中括号访问 map 时对应的键不存在，那么会新增这个键，并且值为默认值，因此中括号会影响键的存在性。

```cpp
map<char, int> mp;
cout << mp.count('a') << endl; // 0
mp['a'];                       // 即使什么都没做，此时mp['a']=0已经插入了
cout << mp.count('a') << endl; // 1
cout << mp['a'] << endl;       // 0
```

#### 不可用迭代器计算下标

map 的迭代器不能像 vector 一样相减得到下标。**下面是错误用法：**

```cpp
auto it = mp.find('a');      // 正确，返回2所在位置的迭代器。
int idx = it - mp.begin();   // 错误！不可相减得到下标。
```

## 2.7 字符串 string

**`#include <string>`**

### 常用方法

```cpp
string s1;           // 构造字符串，为空
string s2 = "awa!";  // 构造字符串，并赋值awa!
string s3(10, '6');  // 构造字符串，通过构造函数构造为6666666666
```

| 作用                   | 用法                          | 示例                            |
| ---------------------- | ----------------------------- | ------------------------------- |
| 修改、查询指定下标字符 | `[]`                          | `s[1] = 'a';`                   |
| 是否相同               | `==`                          | `if (s1 == s2) ...`             |
| 字符串连接             | `+`                           | `string s = s1 + s2;`           |
| 尾接字符串             | `+=`                          | `s += "awa";`                   |
| 取子串                 | `.substr(起始下标, 子串长度)` | `string sub = s.substr(2, 10);` |
| 查找字符串             | `.find(字符串, 起始下标)`     | `int pos = s.find("awa");`      |

数值与字符串互转（C++11）

| 源                                             | 目的        | 函数        |
| ---------------------------------------------- | ----------- | ----------- |
| int / long long / float / double / long double | string      | to_string() |
| string                                         | int         | stoi()      |
| string                                         | long long   | stoll()     |
| string                                         | float       | stof()      |
| string                                         | double      | stod()      |
| string                                         | long double | stold()     |

### 注意事项

#### 尾接字符串一定要用 `+=`

string 的 += 运算符，将会在原字符串原地尾接字符串。而 + 了再 = 赋值，会先生成一个临时变量，在复制给 string.

通常字符串长度可以很长，如果使用 + 字符串很容易就 TLE 了。

```cpp
// 优化前: 15139ms
string s;
for (int i = 0; i < 5e5; i++)
    s = s + "a";

// 优化后: < 1ms (计时器显示0)
string s;
for (int i = 0; i < 5e5; i++)
    s += "a";
```

#### `.substr()` 方法的奇葩参数

**一定要注意，C++ string 的取子串的第一个参数是子串起点下标，第二个参数是子串长度。**

**第二个参数不是子串终点！不是子串终点！要与 java 等其他语言区分开来。**

#### `.find()` 方法的复杂度

该方法实现为暴力实现，时间复杂度为 $O(n^2)$.

## 2.8 二元组 pair

**`#include <utility>`**

顾名思义，就是储存二元组的。

### 常用方法

**`pair<第一个值类型, 第二个值类型> pr`**

- 第一个值类型：要储存的第一个值的数据类型
- 第二个值类型：要储存的第二个值的数据类型

```cpp
pair<int, int> p1;
pair<int, long long> p2;
pair<char, int> p3;
// ...
```

赋值

```cpp
pair<int, char> pr = {1, 'a'};
```

直接取值

- 取第一个值：`.first`
- 取第二个值：`.second`

```cpp
pair<int, char> pr = {1, 'a'};
int awa = pr.first;
char bwb = pr.second;
```

结构化绑定 C++17

```cpp
pair<int, char> pr = {1, 'a'};
auto &[awa, bwb] = pr;
```

判同 直接用 `==` 运算符

```cpp
pair<int, int> p1 = {1, 2};
pair<int, int> p2 = {1, 3};
if (p1 == p2) { ... } // false
```

# 叁·迭代器简介

## 3.1迭代器认识

对于一个 vector，我们可以用下标遍历：

```cpp
for (int i = 0; i < a.size(); i++)
    cout << a[i] << endl;
```

我们同时也可以用迭代器来遍历：

```cpp
for (vector<int>::iterator it = a.begin(); it != a.end(); ++it)
    cout << *it << endl;
```

- `a.begin()` 是一个迭代器，指向的是第一个元素
- `a.end()` 是一个迭代器，指向的是最后一个元素**再后面一位**
- 上述迭代器具有自增运算符，自增则迭代器向下一个元素移动
- 迭代器与指针相似，如果对它使用解引用运算符，即 `*it`，就能取到对应值了

## 3.2 为何需要迭代器？

很多数据结构并不是线性的（例如红黑树），对于非线性数据结构，下标是无意义的。无法使用下标来遍历整个数据结构。

迭代器的作用就是定义某个数据结构的遍历方式，通过迭代器的增减，代表遍历到的位置，通过迭代器便能成功遍历非线性结构了。

例如，set 的实现是红黑树，我们是没法用下标来访问元素的。但是通过迭代器，我们就能遍历 set 中的元素了：

```cpp
for (auto it = st.begin(); it != st.end(); ++it) cout << *it << endl;
```

## 3.3 迭代器用法

对于 vector 容器，它的迭代器功能比较完整，以它举例：

- `.begin()`：头迭代器
- `.end()`：尾迭代器
- `.rbegin()`：反向头迭代器
- `.rend()`：反向尾迭代器
- 迭代器 `+` 整型：将迭代器向后移动
- 迭代器 `-` 整型：将迭代器向前移动
- 迭代器 `++`：将迭代器向后移动 1 位
- 迭代器 `--`：将迭代器向前移动 1 位
- 迭代器 `-` 迭代器：两个迭代器的距离// set 不可用
- `prev(it)`：返回 it 的前一个迭代器
- `next(it)`：返回 it 的后一个迭代器

## 3.4 常见问题

**`.end()` 和 `.rend()` 指向的位置是无意义的值**

对于一个长度为 10 的数组：`for (int i = 0; i < 10; i++)`，第 10 位是不可访问的

对于一个长度为 10 的容器：`for (auto it = a.begin(); it != a.end(); ++it)`，.end 是不可访问的

**不同容器的迭代器功能可能不一样**

迭代器细化的话有正向、反向、双向，每个容器的迭代器支持的运算符也可能不同，因此不同容器的迭代器细节很有可能是不一样的。

**删除操作时需要警惕**

# 肆·常用算法

## 4.1 `swap()`

交换两个变量的值

## 4.2 `sort()`

默认排序从小到大

```cpp
vector<int> arr{1, 9, 1, 9, 8, 1, 0};
sort(arr.begin(), arr.end());
// arr = [0, 1, 1, 1, 8, 9, 9]
```

如果要从大到小，则需要传比较器进去。

```cpp
vector<int> arr{1, 9, 1, 9, 8, 1, 0};
sort(arr.begin(), arr.end(), greater<int>());
// arr = [9, 9, 8, 1, 1, 1, 0]
```

如果需要完成特殊比较，则需要手写比较器。

比较器函数返回值是 bool 类型，传参是需要比较的两个元素。记我们定义的该比较操作为 $\star$：

- 若 $a\star b$，则比较器函数应当返回 `true`
- 若 $a\not\star b$，则比较器函数应当返回 `false`

```cpp
bool cmp(pair<int, int> a,pair<int, int> b){
    //第二位从小到大
    if(a.second != b.second) return a.second < b.second;
    //第一位从大到小
    return a.first > b.first;
}
//如果传入cmp的两个元素相同 则他一定会返回false 与 sort 函数实现有关
// return a.first >= b.first 错误写法 会 RE
int main()
{
    vector<pair<int, int>> arr{{1, 9}, {2, 9}, {8, 1}, {0, 0}};
    sort(arr.begin(), arr.end(), cmp);//手写比较器
    // arr = [(0, 0), (8, 1), (2, 9), (1, 9)]
    for (auto ele : arr) cout << ele.first << " " << ele.second << endl;
    return 0;
}
```

## 4.3 `lower_bound()` / `upper_bound()`

在**已升序排序**的元素中，应用二分查找检索指定元素，返回对应元素迭代器位置。**找不到则返回尾迭代器。**

- `lower_bound()`: 寻找 $\geq x$ 的第一个元素的位置
- `upper_bound()`: 寻找 $>x$ 的第一个元素的位置

怎么找 $\leq x$ / $< x$ 的第一个元素呢？

- $>x$ 的第一个元素的前一个元素（如果有）便是 $\leq x$ 的第一个元素
- $\geq x$ 的第一个元素的前一个元素（如果有）便是 $<x$ 的第一个元素

**返回的是迭代器，如何转成下标索引呢？减去头迭代器即可。**

**用法示例**

```cpp
vector<int> arr{0, 1, 1, 1, 8, 9, 9};
vector<int>::iterator it = lower_bound(arr.begin(), arr.end(), 7);
int idx = it - arr.begin();
// idx = 4
```

我们通常写成一行：

```cpp
vector<int> arr{0, 1, 1, 1, 8, 9, 9};
idx = lower_bound(arr.begin(), arr.end(), 7) - arr.begin(); // 4
idx = lower_bound(arr.begin(), arr.end(), 8) - arr.begin(); // 4
idx = upper_bound(arr.begin(), arr.end(), 7) - arr.begin(); // 4
idx = upper_bound(arr.begin(), arr.end(), 8) - arr.begin(); // 5
```

## 4.4 `reverse()`

反转一个可迭代对象的元素顺序

**用法示例**

```cpp
vector<int> arr(10);
iota(arr.begin(), arr.end(), 1);
// 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
reverse(arr.begin(), arr.end());
// 10, 9, 8, 7, 6, 5, 4, 3, 2, 1
reverse(arr.begin() + 2,arr.begin() + 6);//反转 为左闭右开区间 [2,6) 反转 2345
```

## 4.5 `max()` / `min()`

返回最大值 / 最小值的**数值

```cpp
int mx = max({1, 2, 3, 4}); // 4
int mn = min({1, 2, 3, 4}); // 1
```

## 4.6 `unique()`

消除数组的重复**相邻**元素，数组长度不变，但是有效数据缩短，返回的是有效数据位置的结尾迭代器。

例如：$[1,1,4,5,1,4]\to[1,4,5,1,4,\underline?]$，下划线位置为返回的迭代器指向。

```cpp
template< class ForwardIt >
ForwardIt unique( ForwardIt first, ForwardIt last );
```

**用法示例**

单独使用 unique 并不能达成去重效果，因为它只消除**相邻**的重复元素。但是如果序列有序，那么它就能去重了。

但是它去重后，序列尾部会产生一些无效数据：$[1,1,2,4,4,4,5]\to[1,2,4,5,\underline?,?,?]$，为了删掉这些无效数据，我们需要结合 erase.

```cpp
vector<int> arr1{1,2,1,4,5,4,4};
//1 1 2 4 4 4 5
//1 2 4 5 *(unique返回*的位置)
sort(arr1.begin(),arr1.end());
arr1.erase(unique(arr1.begin(),arr1.end()), arr1.end());
```

## 4.7 数学函数

所有函数参数均支持 `int` / `long long` / `float` / `double` / `long double`

| 公式                    | 示例         |
| ----------------------- | ------------ |
| $f(x)=\lvert x\rvert$   | `abs(-1.0)`  |
| $f(x)=e^x$              | `exp(2)`     |
| $f(x)=\ln x$            | `log(3)`     |
| $f(x,y)=x^y$            | `pow(2, 3)`  |
| $f(x)=\sqrt x$          | `sqrt(2)`    |
| $f(x)=\lceil x\rceil$   | `ceil(2.1)`  |
| $f(x)=\lfloor x\rfloor$ | `floor(2.1)` |
| $f(x)=\left<x\right>$   | `round(2.1)` |

**注意事项**

由于浮点误差，有些的数学函数的行为可能与预期不符，导致 WA。如果你的操作数都是整型，那么用下面的写法会更稳妥。

> 原文地址：https://codeforces.com/blog/entry/107717

- $\lfloor\frac{a}{b}\rfloor$
  - 别用：`floor(1.0 * a / b)`
  - 要用：`a / b`
- $\lceil\frac{a}{b}\rceil$
  - 别用：`ceil(1.0 * a / b)`
  - 要用：`(a + b - 1) / b`  （$\lceil\frac{a}{b}\rceil=\lfloor\frac{a+b-1}{b}\rfloor$）
- $\lfloor\sqrt a\rfloor$
  - 别用：`(int) sqrt(a)`
  - 要用：二分查找 https://io.zouht.com/7.html
- $a^b$
  - 别用：`pow(a, b)`
  - 要用：快速幂 https://io.zouht.com/18.html
- $\lfloor\log_2 a\rfloor$
  - 别用：`log2(a)`
  - 要用：`__lg` （不规范，但是这是竞赛）/ `bit_width`（C++20 可用）

## 4.8 `gcd()` / `lcm()`

（C++17）返回最大公因数 / 最小公倍数

```cpp
int x = gcd(8, 12); // 4
int y = lcm(8, 12); // 24
```

如果不是 C++17，但是是 GNU 编译器（g++），那么可以用内置函数 `__gcd()`.

当然，`gcd` / `lcm` 函数也挺好写，直接写也行（欧几里得算法）：

```cpp
int gcd(int a, int b)
{
    if (!b)
        return a;
    return gcd(b, a % b);
}

int lcm(int a, int b)
{
    return a / gcd(a, b) * b;
}
```

# 伍·题目练习

* 字符相关补充 
  * cin.getline(buf, n, '@');  遇到‘@’或者读满 n-1 结束
  * getchar(); 去换行
  * getline(cin, s, '@')遇到‘@’停止

## 5.1vector

### 洛谷 [P1047](https://www.luogu.com.cn/problem/P1047)

用  bool 向量记录每个位置的状态 

```cpp
vector<bool> arr(l + 1,false);
for(int a = 0;a < m;a++){
    int i,j;
    cin >> i >> j;
    for(int b = i;b <= j;b++) arr[b] = true;
}
```

## 5.2stack/queue

### 洛谷 [P1739](https://www.luogu.com.cn/problem/P1739)表达式括号匹配

检查()左右括号匹配

遇到左括号就入一次栈 右括号就抵消一次 最后判空

```cpp
stack<bool> st;
for (char ch : s){
    if(ch == '(') st.push(true);
    else if(ch == ')'){
        if(st.empty()){
            cout << "NO";
            return 0;
        }
        st.pop();//抵消一次
    }
}
if(st.empty()) cout << "YES";
else cout << "NO";
```

### 洛谷 [P1449](https://www.luogu.com.cn/problem/P1449)后缀表达式

```cpp
stack<int> arr;//栈
string s;
int num = 0;//临时 用来存数
getline(cin, s, '@');
for( char ch : s){
    if(ch == '.'){//将数压入栈
        arr.push(num);
        num = 0;
    }
    else if(ch == '+'){
        int a = arr.top(); arr.pop();
        int b = arr.top(); arr.pop();
        arr.push(a+b);
    }
    else if(ch == '-'){
        int a = arr.top(); arr.pop();
        int b = arr.top(); arr.pop();
        arr.push(b-a);
    }
    else if(ch == '*'){
        int a = arr.top(); arr.pop();
        int b = arr.top(); arr.pop();
        arr.push(a*b);
    }
    else if(ch == '/'){
        int a = arr.top(); arr.pop();
        int b = arr.top(); arr.pop();
        arr.push(b/a);
    }
    else num = num * 10 + (ch - '0');//叠数字
}
cout << arr.top();//栈顶即是答案
```

### 洛谷 [P1996](https://www.luogu.com.cn/problem/P1996)约瑟夫问题

一个圆圈点到人出局 使用队列解决问题 每次安排 m-1 个人出列到队尾 第 m 个人报数出列

```cpp
for(int i = 1;i <= n;i++) arr.push(i);
while(!arr.empty()){
    for(int i = 1;i < m;i++){
        int x = arr.front();
        arr.pop();
        arr.push(x);
    }
    cout << arr.front();
    arr.pop();
    if(!arr.empty()) cout << " ";
}
```
