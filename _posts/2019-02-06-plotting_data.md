---
title: 绘制数据
categories: [机器学习]
resource_path: /blog/assets/2019-02-06-plotting_data
---

基本操作
---

```MATLAB
>> t = [0:0.01:0.98];
>> y1 = sin(2*pi*4*t);
>> plot(t, y1)
>> y2 = cos(2*pi*4*t);
>> plot(t, y2)
```

![plot(t, y1)]({{page.resource_path}}/sin.png "plot(t, y1)")

![plot(t, y2)]({{page.resource_path}}/cos.png "plot(t, y2)")

```MATLAB
```

![hold_on]({{page.resource_path}}/hold_on.png)

```MATLAB
>> close  % 关闭图表
>> figure(1)  % 打开或切换到图表1，对其进行操作
>> plot(t, y1)
>> figure(2)  % 打开或切换到图表2，对其进行操作
>> plot(t, y2)
```

格式化参数
---

* 调用方式：plot(X, Y, FMT)
* FMT是一个字符串，由几个可选项组成："&lt;linewith&gt;&lt;marker&gt;&lt;color&gt;&lt;marker&gt;&lt;color&gt;&lt;displayname&gt;"，以下为官方帮助文档中参数各可选项的可选值。（color中的‘blacK’首字母没大写！:unamused:）

  > Format arguments:
  >
  > linestyle
  >
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'-'  Use solid lines (default).
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'--' Use dashed lines.  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;':'  Use dotted lines.  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'-.' Use dash-dotted lines.  
  >
  >marker  
  >
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'+'  crosshair  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'o'  circle  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'*'  star  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'.'  point  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'x'  cross  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'s'  square  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'d'  diamond  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'^'  upward-facing triangle  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'v'  downward-facing triangle  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'>'  right-facing triangle  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'<'  left-facing triangle  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'p'  pentagram  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'h'  hexagram  
  >
  >color
  >
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'k'  blacK  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   'r'  Red  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'g'  Green  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   'b'  Blue  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   'y'  Yellow  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   'm'  Magenta  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   'c'  Cyan  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   'w'  White  
  >
  >";displayname;"  
  > &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   Here "displayname" is the label to use for the plot legend .

其他操作
---

```MATLAB
>> xlabel('feature')  % 为刚刚生成的图表动态添加x轴的名字
>> ylabel('value')  % 为刚刚生成的图表动态添加y轴的名字
```

![add_label]({{page.resource_path}}/add_label.png 'add xlabel and ylabel')

```MATLAB
>> legend('sin', 'cos')  % 给绘制的两条线命名,也可通过plot函数的FMT参数中的<;displayname;>这一项，在一开始就命名
```

![legend]({{page.resource_path}}/legend.png 'legend(\'sin\', \'cos\')')

```MATLAB
>> title('my plot')  % 动态添加图表的名称
```

![add_title]({{page.resource_path}}/title.png 'title(\'my plot\')')

```MATLAB
>> subplot(4, 4, 1)  % 将画板按4*4矩阵划分，将子图放在第一块上
>> plot(t, y1) % 在该子图上绘图
>> subplot(2, 2, 4)  % 将画板按2*2矩阵划分，将子图放在第四块上。
>> plot(t, y2)  % 在该子图上绘图
```

![subplot]({{page.resource_path}}/subplot.png 'subplot')

```MATLAB
>> axis([-1 1 -0.5 1])  % 设置x轴和y轴的取值范围：x∈[-1, 1]， y∈[-0.5, 1]
```

![set_axis]({{page.resource_path}}/axis.png 'aixs(p-1 1 0.5 1])')

```MATLAB
>> A = magic(5)
A =

   17   24    1    8   15
   23    5    7   14   16
    4    6   13   20   22
   10   12   19   21    3
   11   18   25    2    9

>> imagesc(A)  % 根据矩阵的值给矩阵各块上色
>> colorbar  % 在右侧添加色度条
>> colormap gray;  % 选取要映射的颜色表
```

![imagesc]({{page.resource_path}}/imagesc.png 'imagesc(A)')
![colormap]({{page.resource_path}}/colormap.png 'add color bar and set colormap')

```MATLAB
>> clf  % 清空当前图表，使得可以在该窗口重新绘制另一个图表
```

保存
---

```MATLAB
>> cd E:\Octave\octave_folder; print -dpng 'myPlot.png'
>>  % print后的可选项格式为：d + 图片类型后缀名
```

[课程链接](https://www.coursera.org/learn/machine-learning/lecture/I7gx3/plotting-data)
