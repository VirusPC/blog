---
title: Matlab/Octave 移动数据
categories: [机器学习]
tags: [Matlab, Octave]
---

矩阵操作
---

```MATLAB
>> A = ones(3, 2)
A =

   1   1
   1   1
   1   1

>> size(A)  %矩阵大小
ans =

   3   2

>> size(A, 1)  % 行数
ans =  3
>> size(A, 2)  % 列数
ans =  2
>> length(A)  % 最大维度，建议只对向量使用而不是对矩阵使用
ans =  3
```

可以使用部分linux命令
---

```SHELL
>> pwd
>> ls
>> cd .
```

在系统中加载数据和寻找数据
---

```MATLAB
>> load featuresX.dat  % 加载为矩阵
>> load priceY.dat
>> load('featuresX.dat')
>> who  % 显示已经定义的变量
Variables in the current scope:

A    a    ans  b    c    r    v    w    w2

>> whos  % 详细信息s
Variables in the current scope:

   Attr Name        Size                     Bytes  Class
   ==== ====        ====                     =====  =====
        A           3x2                         48  double
        a           1x1                          8  double
        ans         1x14                        14  char
        b           1x2                          2  char
        c           1x1                          1  logical
        r           1x3                         24  double
        v           1x2                         24  double
        w           1x10000                  80000  double
        w2          2x2                         32  double

Total is 10033 elements using 80153 bytes
```

清除数据
---

```MATLAB
>> clear w
>> who
Variables in the current scope:

A    a    ans  b    c    r    v    w2

>>
```

保存数据
---

```MATLAB
>> B = [1 2; 3 4; 5 6]
B =

   1   2
   3   4
   5   6

>> B(1:2)  % 取出1~2号元素，列优先排序
ans =

   1   3

>> B(2:3)
ans =

   3   5

>> B(1:4)
ans =

   1   3   5   2

>> cd octave_folder/
>> save hello.mat B;  % 保存为二进制文件
>> ls
hello.mat

>> save hello.txt B -ascii  % 保存为ASCII文本
>> ls
hello.mat    hello.txt

```

操作矩阵元素
---

```MATLAB
>> B(1,2)  % 获取指定位置的元素
ans =  2
>> B(1, :)  % 冒号意味着该行/列的每一个元素
ans =

   1   2

>> B(:, :)  % 全部元素
ans =

   1   2
   3   4
   5   6

>> B([1,2], :)  % 可指定某几行/列
ans =

   1   2
   3   4

>> B(1:3, 1)  % 1~3行，1列
ans =

   1
   3
   5

>> B([2,3], :) = [5 6; 3 4]  % 更改多行多列
B =

   1   2
   5   6
   3   4

>> B = [B [10; 11; 12]]  % 添加多行/列
B =

    1    2   10
    5    6   11
    3    4   12

>> B(:) % 将所有的元素放入一个列向量（列优先）
ans =

    1
    5
    3
    2
    6
    4
   10
   11
   12
```
