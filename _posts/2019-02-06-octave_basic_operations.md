---
title: 基本操作
categories: ['Matlab/Octave'] 
tags: ['Octave', 'Matlab']
resource_path: /blog/assets/2019/02/06/octave_basic_operations
---


乘方
---

``` MATLAB
>> 2^10
ans =  10242^6
```

不等于
---

``` MATLAB
>> 1 ~= 2
ans = 1
```

与、或、异或
---

``` MATLAB
>> 1 && 0
ans = 0
>> 1 || 0
ans = 1
>> xor(1, 0)
ans = 1
```

更改提示符
---

``` MATLAB
octave:1> PS1(">> ") % 双引号中为想要的命令提示符
>>
```

语句结束符
---

语句结束符可为空，逗号或分号

 在命令行中，若一条语句以分号结尾，则不会自动打印输出；若以空或逗号结尾，会自动打印输出，逗号结尾方便多条想打印的语句写在同一行

```MATLAB
>> a= [1 2;
>> 3 4;
>> 5 6]
a =
    1   2
    3   4
    5   6

>> a=1;b=2;
>> a=1, b=2, c=3
a =  1
b =  2
c =  3
```

打印语句
---

输出对象:

```MATLAB
>> a=pi; % pi是内置常量
>> disp(a); % 注意每句都以分号结尾
3.1416
```

格式化输出

```MATLAB
>> disp(sprintf('2 decimals: %0.2f', a))
2 decimals: 3.14
```

设置默认输出得到数字的格式（保留多少位）

```MATLAB
>> a = pi
a =  3.1416
>> format long
>> a
a =  3.141592653589793
>> format short
>> a a =  3.1416 
```

创建矩阵
---
一般方式

```MATLAB
>> A = [1 2; 3 4; 5 6] % 用空格或逗号用来分隔行元素，分号用来分隔列元素
A =

    1   2
    3   4
    5   6

>> A = [1 2;
> 3 4;
> 5 6] % 注意双箭头变为了单箭头
A =

    1   2
    3   4
    5   6
```

start:step:end

```MATLAB
>> v = 1:0.3:2 % 生成行向量，元素取值范围为[1, 2]，每步大小为0.3
v =

    1.0000    1.3000    1.6000    1.9000

>> v = 1:2 % 元素取值为范围为[1, 2]， 每步大小为默认值1
v =

    1   2
```

函数

```MATLAB
>> ones(1, 3) % 元素全为1。若只传入一个参数n，则建立一个n*n的矩阵，其他函数同理
ans =

1   1   1

>> zeros(1, 3) % 元素全为0
ans =

0   0   0

>> eye(3) % 3*3的单位矩阵
ans =

Diagonal Matrix

1   0   0
0   1   0
0   0   1

>> rand(1, 3) % 各元素值的随机范围(0, 1）
ans =

0.800381   0.043756   0.940759

>> randn(1,3) % 高斯随机变量，服从标准正态分布N(0, 1)
ans =

0.5878375  -1.3163437   0.0099364
```

绘制直方图
---

```MATLAB
>> w = -6 + sqrt(10)*(randn(1,10000));
>> hist(w)
>> hist(w, 50) % 指定直方图有50列
```
![hist(w)]({{page.resource_path}}/hist(w).png)
![hist(2,50)]({{page.resource_path}}/hist(2,&#32;50).png)
