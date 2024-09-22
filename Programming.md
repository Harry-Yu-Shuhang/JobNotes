# JobNotes-Programming
面试常考编程相关知识，主要语言为Python和C++。

## 一、基础概念篇
### 1.Python和C/C++的区别是什么？C和C++的区别是什么？
Python是基于C/C++编写的，C/C++相较于Python更为基础，是Python的基石。Python拥有强大的库，代码简洁易懂，善于解决机器学习等复杂任务。C/C++在运行速度、程序大小方面有无可替代的优势，适合低延迟要求的任务，如游戏引擎、量化交易系统等。

C和C++的区别：C++是基于C进行的改进。C为面向过程的编程语言，而C++面向对象，比如C++中新增了类，将需要调用的数据特征抽象为一个类，相较于C的顺序结构，写起来更简洁。


### 2.Python中元组和列表的区别是什么？
元组不可修改，列表可以修改。

### 3.数组和链表的区别是什么？各自的优势是什么？
数组采用顺序结构存储，链表中各个元素通过指针连接，更为灵活。数组定义、使用简单，适合存储固定的数据，或者只需要在最尾部修改的数据，而链表则适合经常需要在中间位置插入或者删除元素的数据。

### 4.B-树和B+树的区别？各自的优势？mysql选择的是哪种？
B-树的每个节点既包含键值，也包含数据，每个节点都可以作为查找的终点。而B+树的非叶子节点只存储键值，真正的数据只存储在叶子节点上。因此，数据量很大的时候，B-树的查找效率不如B+树稳定，因为B-树可能会一层就找到了，也可能需要找很多很多层。但是，当要查找的值恰好处在一个非叶子节点时，查找就会成功并结束，这种情况下B-树有优势。

总之，B+树更稳定，B-树在查询非叶子节点时比B+树有优势。

mysql选择的是B+树。

### 5.Chatgpt的大致原理是什么？
开发岗一般不会问的很深，讲出是通过大数据训练生成的问答系统，用的是Transformer模型即可。

### 6.C++中的虚函数是什么？
定义方式是virtual，主要用于实现多态，同一个函数名在不同情况下进行不同的override覆写，从而调用不同结果。

### 7.讲讲C++中类的继承和派生。
继承是继承一个类的所有属性，派生是在继承的基础上新增自己需要的新属性。
### 8.讲讲Hadoop的大致原理。
这块可能数据分析、数据开发岗面的多一些，前后端和测开一般不考。

Hadoop分为三个模块

hdfs：负责存储数据。

mapreduce：负责运算。

yarn：负责资源调度。

#### 追问：能讲讲hadoop是如何处理大数据的吗？

答：主要通过分治法，将大数据分割成许多数据包进行并行处理。

### 9.Python深拷贝和浅拷贝的区别是什么？
浅拷贝拷贝出的新对象在原始拷贝的对象的值发生变化以后，新对象的值也会发生变化，而深拷贝出的新对象则不会受到影响。

## 二、代码实战篇
这部分主要还是靠自己的能力，不管用什么编程语言，能写出来就行。我一般用Python。但是也有几个常考的。

### 1.反转链表
```
#include <iostream>
using namespace std;

struct ListNode
{
    int val;
    struct ListNode *next;
};

//1.非递归法反转链表
ListNode* reverseList(ListNode* head) {
        ListNode* temp; // 保存cur的下一个节点
        ListNode* cur = head;
        ListNode* pre = NULL;
        while(cur) {
            temp = cur->next;  // 保存一下 cur的下一个节点，因为接下来要改cur->next
            cur->next = pre; // 翻转操作
            // 更新pre 和 cur指针
            pre = cur;
            cur = temp;
        }
        return pre;
    }

//2.递归反转链表
ListNode* reverse(ListNode* pre,ListNode* cur){
        if(cur == NULL) {return pre;}
        ListNode* temp = cur->next;
        cur->next = pre;
        // 可以和双指针法的代码进行对比，如下递归的写法，其实就是做了这两步
        // pre = cur;
        // cur = temp;
        return reverse(cur,temp);
    }
```

### 2.Python常用函数
切片：基本语法为'sequence[start:stop:step]'。左闭右开。 
```
#Example 1: 切片
nums = [1,2,3,4,5,6,7,8,9]
nums[4:]=[1,2,3]
print(nums)
#结果为[1, 2, 3, 4, 1, 2, 3](把nums[4]以及之后的换成[1,2,3])

nums = [1,2,3,4,5,6,7,8,9]
nums[4:6]=[1,2,3]
print(nums)
#结果为[1, 2, 3, 4, 1, 2, 3, 7, 8, 9]（把nums[4]~nums[6]（左闭右开，即5，6）换成[1,2,3]）

nums = [1,2,3,4,5,6,7,8,9]
nums[4:8]=[1,2,3]
print(nums)
#结果为[1, 2, 3, 4, 1, 2, 3, 9]（把nums[4]~nums[8]（左闭右开，即5，6，7，8）换成[1,2,3]）

nums = [1,2,3,4,5,6,7,8,9]
print(nums[::-2])
#结果为[9, 7, 5, 3, 1]。从尾到头，步长-2
```

排序：sort 如：nums.sort(reverse = True)这就是递减排序.不加默认的话是递增

分割：split，默认分割符为空格。
```
csv_text = "apple,banana,cherry,date"
fruits = csv_text.split(',')
print(fruits)
#结果：['apple', 'banana', 'cherry', 'date']
```
### 3.Linux指令
| 功能  | 指令 |
| ----- | --- |
| 查看文件目录 | pwd |
| 切换目录 | cd |
| 创建文件夹 | mkdir |
| 删除文件夹 | rm |
| 移动或者重命名 | mv |
| 查看文件内容 | cat或者more |
| 查看网卡信息 | ifonfig |
| 杀进程 | kill |
| 编译程序 | gcc |
