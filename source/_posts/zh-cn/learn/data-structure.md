---
title: data_structure
lang: zh-cn
category: learn
date: 2020-09-09 20:54:30
tags:
toc: true
---

## 数据结构概论

1. 抽象数据类型(Abstruct Data Type, ADT): 数据对象(Int, String, List等)的逻辑描述方法
   ```
   ADT 线性表(List)
   Data
       创建一个存储 DataType 类型元素的线性表, 线性表的数据对象集合为{a_1, a_2, ..., a_n}, 每个元素的类型均为DataType.
       其中除第一个元素 a_1 外, 每一个元素有且只有一个前驱元素; 除了最后一个元素 a_n 外, 每一个元素有且只有一个直接后继元素.
       数据元素之间的关系是一对一(链式)的关系.
   Operation
       InitList(*L) : 初始化操作, 建立一个空的线性表 L .
       ListEmpty(L) : 判断线性表是否为空表, 若线性表为空, 返回 true , 否则返回 false .
       ClearList(*L) : 将线性表清空.
       GetElem(L, i, *e) : 将线性表 L 中的第 i 个位置元素值返回给 e .
       LocateElem(L, e) : 在线性表 L 中查找与给定值 e 相等的元素, 如果查找成功, 返回该元素在表中序号表示成功; 否则返回 -1 表示失败
       ListInsert(*L, i, e) : 在线性表 L 中第 i 个位置插入新元素 e .
       ListDelete(*L, i, *e) : 删除线性表 L 中第 i 给位置元素, 并用 e 返回其值.
       ListLength(L) : 返回线性表 L 的元素个数.
   endADT
   ```
2. 数据的逻辑结构: 有 集合, 线性, 树形, 图形 四类

## 算法

1. 特性(仅了解):
   * 输入输出: ≥0个输入, ≥1个输出
   * 有穷性: 不会死循环
   * 确定性: 相同的输入, 一样的结果
   * 可行性: 不会消耗过长的时间
2. 要求(仅了解):
   * 正确性
   * 可读性
   * 健壮性: 即使是奇怪的输入也不会导致奇怪的后果
   * 时间效率高
   * 存储量低
### 时间复杂度

表示随着输入规模增大导致耗时增加的程度
记作: \\(T(n) = O(f(n))\\) , 其中 \\(n\\) 代表问题规模, \\(f(n)\\) 是渐进时间复杂度 (高数中的同阶无大)
通常我们用 \\(O(f(n))\\) 来表示算法的时间复杂度, 叫作大O阶
推导大O阶:
1. 只保留 \\(T(n)\\) 中的最高阶项
2. 去除最高阶项的常数(除非最高阶是常数 1)
4. 常见的时间复杂度
   | 函数阶           | 非正式用语       | 常见于                                |
   |------------------|------------------|---------------------------------------|
   | \\(O(1)\\)       | 常数阶           | 略                                    |
   | \\(O(n)\\)       | 线性阶           | 循环结构                              |
   | \\(O(n^2)\\)     | 平方阶           | 嵌套双循环                            |
   | \\(O(n^3)\\)     | 立方阶           | 嵌套3循环                             |
   | \\(O(2^n)\\)     | 指数阶           | 嵌套n个循环(嵌套多了, 就变成指数阶了) |
   | \\(O(\log n)\\)  | 对数阶           | 循环子的增长呈指数的循环              |
   | \\(O(n\log n)\\) | \\(n\log n\\) 阶 | 对数阶+套个循环                       |
   耗费的时间从小到大依次是:
   \\(O(1)<O(\log n)<O(n)<O(n\log n)<O(n^2)<O(n^3)<O(2^n)<O(n!)<O(n^n)\\)
### 最坏情况与平均情况

算法的时间复杂度不是固定的, 依据输入的数据而决定
通常算法给出的时间复杂度都是指最坏情况

### 空间复杂度

算法所需占用的内存
记作 \\(S(n)=O(f(n))\\)
空间复杂度为 \\(O(1)\\) 时称为**原地工作**
实际情况下通常不考虑空间复杂度

## 线性表(List)

### 定义和抽象数据类型

1. 定义:
   线性表的结构长这样:
   {% asset_img List.png %}
   记作: \\((a_1, a_2, \dots, a_{i-1}, a_i, a_{i+1}, \dots, a_n)\\)
   若元素个数 n=0 时, 称为空表, i 称作**位序**
   每一个元素有1个**前驱**和**后继**, 第一个和最后一个元素除外.
   如图中 \\(a_i\\) 有前驱 \\(a_{i-1}\\) 和 后继 \\(a_{i+1}\\)
2. ADT 定义:
   ```
   ADT 线性表(List)
   Data    
       线性表的数据对象集合为{a1, a2, ......, an}, 每个元素的类型均为DataType.
       其中, 除第一个元素a1外, 每一个元素有且只有一个直接前驱元素, 除了最后一个元素an外, 每一个元素有且
       只有一个直接后继元素.
       数据元素之间的关系是一对一的关系.
   Operation    
       InitList(*L):          初始化操作, 建立一个空的线性表L.    
       ListEmpty(L):          若线性表为空, 返回true, 否则返回false.    
       ClearList(*L):         将线性表清空.    
       GetElem(L, i, *e):     将线性表L中的第i个位置元素值返回给e.    
       LocateElem(L, e):      在线性表L中查找与给定值e相等的元素, 如果查找成功, 
                              返回该元素在表中序号表示成功；否则, 返回-1表示失败
       ListInsert(*L,i,e):    在线性表L中的第i个位置插入新元素e
       ListDelete(*L,i,*e):   删除线性表L中的第i个位置元素, 并用e返回其值
       ListLength(L):         返回线性表L的元素个数
   EndADT
   ```

### 线性表的顺序存储结构

1. 结构特点:
   用一段地址连续的存储单元依次存储线性表的数据元素
   {% asset_img List-sequence-structure.png %}
2. 顺序存储结构需要三个属性：
   * 存储空间地址 data
   * 线性表的最大存储容量 MaxSize
   * 线性表的当前有效长度 length
3. 线性表的顺序存储的结构代码:
   ```C++
   typedef int ElemType;          /* ElemType类型根据实际情况而定, 这里假设为int */
   
   typedef struct
   {          
       ElemType data[MAXSIZE];    /* 数组存储数据元素, 最大值为MAXSIZE */    
       int length;                /* 线性表当前长度 */
   
   } SequenceList;
   ```
4. 地址的计算:
   要取得 \\(a_i\\) 的地址
   ```C++
   ElemType a_i = data + (i-1) * sizeof(ElemType);
   ```
5. 线性表顺序存储结果的优缺点
   <table>
   <thead>
     <tr>
       <th>优点</th>
       <th>缺点</th>
     </tr>
   </thead>
   <tbody>
     <tr>
       <td>* 无须为表示表中元素之间的逻辑关系而增加额外的存储空间<br>* 可以快速地存取表中任一位置的数据</td>
       <td>* 插入和删除操作需要移动大量元素<br>* 当线性表长度变化较大时, 难以确定存储空间的容量<br>* 造成存储空间的"碎片"</td>
     </tr>
   </tbody>
   </table>

#### 顺序存储结构的插入与删除

1. GetElem(L, i, *e) 的实现
   返回 0 代表 OK, -1 代表 ERROR
   ```C++
   #define OK 0
   #define ERROR -1
   
   int GetElem(SequenceList list, int i, ElemType *e)
   {
       if (list.length == 0 || i < 1 || i > L.length)
           return ERROR;
   
       *e = list.data[i - 1];
       return OK;
   }
   ```
2. 插入操作
   {% asset_img List-insert-operation.png %}
   思路:
   * 为了给插入元素腾出空间, 遍历从 i 开始至最后一个元素, 将它们向后移动一个位置(除非插入到表尾).
   * 将元素插入位置 i 处
   * 表长+1
   * 若插入位置不合理, 抛出异常
   * 若线性表已满, 抛出异常或动态增加容量
   实现代码如下:
   ```C++
   #define OK 0
   #define ERROR -1

   int ListInsert(SequenceList *list, int i, ElemType e)
   {
   
       if (list->length == MAXSIZE)                            /* 顺序线性表已满 */
           return ERROR;
       if (i < 1 || i > list->length + 1)                       /* 当i不在范围内时 */
           return ERROR;
   
       if (i <= list->length)                                  /* 若插入数据位置不在表尾 */
       {
           for (int k = list->length - 1; k >= i - 1; k--)     /*将要插入位置后数据元素向后移动一位 */
               list->data[k + 1] = list->data[k];
       }
   
       list->data[i - 1] = e;                                 /* 将新元素插入 */
       list->length++;
       return OK;
   }
   ```
3. 删除操作
   {% asset_img List-delete-operation.png %}
   思路:
   * 为了缩回空出的地方, 遍历从 i 开始至最后一个元素, 将它们向前移动一个位置(除非删除的是表尾的元素).
   * 表长-1
   * 如果删除位置不合理, 抛出异常
   实现代码如下:
   ```C++
   #define OK 0
   #define ERROR -1

   int ListDelete(SequenceList *list, int i, ElemType *e)
   {
       if (list->length == 0)                     /* 线性表为空 */
           return ERROR;
   
       if (i < 1 || i > list->length)             /* 删除位置不正确 */
           return ERROR;
   
       *e = list->data[i - 1];
   
       if (i < list->length)                      /* 如果删除不是最后位置 */
       {
          for (int k = i; k < list->length; k++)  /* 将删除位置后继元素前移 */
              list->data[k - 1] = list->data[k];
       }

       list->length--;
       return OK;
   }
   ```
4. 插入与删除的时间复杂度分析
   * 如果元素要插入到最后一个位置, 或者删除最后一个元素, 时间复杂度为 \\(O(1)\\)
   * 如果元素要插入到第一个位置或者删除第一个元素, 意味着要移动所有的元素向后或者向前, 时间复杂度为 \\(O(n)\\)
   * 平均情况:
      由于元素插入到第 i 个位置, 或删除第 i 个元素，需要移动 n-i 个元素. 平均执行次数是 \\(\frac{(n-1)+(n-2)+\dots+(n-n)}{n}=\frac{n^2-\frac{(1+n)n}{2}}{n}=n-\frac{1+n}{2}=\frac{n-1}{2}\\) (运用等差数列的知识进行化简)
      化为大O阶后平均时间复杂度还是 \\(O(n)\\)

### 线性表的链式存储结构

顺序线性表的缺点就是插入和删除时需要移动大量元素, 显然很耗费时间
因此有了存储形式非线性的链式线性表

1. 结构特点:
   1. 用一组任意的存储单元存储线性表的数据元素，这组存储单元可以是连续的，也可以是不连续的。这些数据元素可以存在内存未被占用的任意位置
      {% asset_img List-chain-structure.png %}
   2. 链式结构中，除了要存数据元素信息外，还要存储它的后继元素的存储地址。
   