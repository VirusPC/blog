---
title: 'D3:基础操作'
categories: ['可视化']
tags: ['javascript', 'd3', 'visualization', '可视化-']
resource_path: /blog/assets/2020/
---

{% include posts_hook.html %}

D3基础操作
===

- [D3基础操作](#d3基础操作)
  - [介绍](#介绍)
  - [选择集](#选择集)
  - [数据绑定与动态属性](#数据绑定与动态属性)
  - [DOM元素与数据个数不匹配问题](#dom元素与数据个数不匹配问题)
  - [过渡](#过渡)

---

介绍
---
**D3js**是一个可以基于数据来操作文档的 JavaScript 库。可以帮助你使用 HTML, CSS, SVG 以及Canvas来展示数据。D3 遵循现有的 Web 标准，可以不需要其他任何框架独立运行在现代浏览器中，它结合强大的可视化组件来驱动 DOM 操作。  

D3 可以将数据绑定到 DOM 上，然后根据数据来计算对应 DOM 的属性值。例如你可以根据一组数据生成一个表格。或者也可以生成一个可以过渡和交互的 SVG 图形。

D3 不是一个框架，因此也没有操作上的限制。没有框架的限制带来的好处就是你可以完全按照自己的意愿来表达数据，而不是受限于条条框框，非常灵活。D3 的运行速度很快，支持大数据集和动态交互以及动画。

你可以用```selection.style("name", "value")```为 DOM 元素设置样式，也可以直接使用 css 为 D3 创建的 DOM 元素设置样式。可以用```selection.attr("name", "value")```为 DOM 元素设置属性。


选择集
---
要想对元素进行操作，首先要对元素进行选择。通过 [d3-selection](https://d3js.org.cn/document/#selections-d3-selection)，我们可以通过```d3.select(selector)```和```d3.selectAll(selector)```对元素进行选择，并对得到的**选择集(selection)**进行操作。  

无论是```select```还是```selectAll```，使用时都需要传入一个[选择器(selector)](https://www.runoob.com/cssref/css-selectors.html)作为参数。选择器可以是标签选择器、类选择器、ID选择器、属性选择器等等。  


 注意```select```与```selectAll```的区别在于，```select```会取得选择器选择结果集合中的第一个元素，而```selectAll```会取得选择器选择结果整个集合。  

```javascript
d3.select("h");  // 选中所有h标签生成的元素中的第一个
d3.selectAll("h");  // 选中所有h标签生成的元素
```

选择完毕之后，后续方法调用会影响选择集中的每一项。

```javascript
d3.selectAll("p").style("color", "white");  // 设置所有的p标签的颜色为白色
```

数据绑定与动态属性
---
**动态属性**是指，样式、属性以及其他属性的值在 D3 中可以是函数形式，而不仅仅是常量。 因此，它可以支持动态设置属性、样式等值。

既然是通过函数动态设置属性，则必然存在参数。那么我们是把什么数据作为参数传入的呢？这里就涉及到**数据绑定**。

我们要绑定的数据应该是一个数组。通过```selection.data(array)```将数据数组```array```与选择集```selection```绑定起来。默认情况下 D3 会将选择集的每一项依次与数据数组的每一项对应起来：将数据数组中的第一个数据传递给选择集中的第一个元素，第二个元素传递给选择集中的第二个元素，以此类推。之后，在计算每个结点自己的动态属性时，会将对应数据以及数据在数组中的下标传递给函数。

作为属性值的函数有两个参数：第一个参数d代表着绑定的数据数组中的一项 ；第二个参数i代表着该数据项在数组中的下标。即该元素在```selection```中的次序，从0开始计数。返回值是一个代表着属性值的常量。  

比如我们可以为奇偶段落设置不同的颜色：

```javascript
let dataArray = ['#000', '#111', '#222', '#333'];
let selection = d3.selectAll("p");  // 假设选择了4个节点
function color(d, i){  // 通过此函数动态设置属性
    return d;
}
selection.data(dataArray);  // 绑定数据
selection.style("color", color);  // 根据绑定数据动态设置属性
```

DOM元素与数据个数不匹配问题
--- 
数据绑定的时候可能出现 DOM 元素与数据元素个数不匹配的问题，那么```enter```和```exit```就是用来处理这个问题的。```enter```操作用来添加新的 DOM 元素，```exit```操作用来移除多余的 DOM 元素。

在数据绑定时存在三种情形：

1. 数据元素个数**等于** DOM 元素个数。此时正常操作。
2. 数据元素个数**少于** DOM 元素个数。此时可以调用```selection.exit().remove()```来移除多余的 DOM 元素。例如：  
    ```javascript
    let selection = d3.selectAll("p")  // 假设有4个元素
                .data[1, 2, 3]   // 假设有3个数据
                .exit().remove();  // 会移除最后一个元素
    ```

3. 数据元素个数**多于** DOM 元素个数。此时可以调用```selection.enter().append(label)```来增加 DOM 元素。例如：  
    ```javascript
    let selection = d3.selectAll("p")  // 假设有4个元素
                .data[1, 2, 3, 4]  // 假设有5个数据
                .enter().append("p");  // 会增加一个p元素 
    ```

过渡
---
D3 支持动画效果，这种动画效果可以通过对样式属性的**过渡**实现。可以通过```elastic```、```cubic-in-out```和```linear```等函数来控制渐变。其补间插值支持多种方式，比如线性/弹性等。此外 D3 内置了多种插值方式，比如对数值类型、字符类型、路径数据以及颜色等，甚至可以扩展D3的插值器注册表，以支持复杂的属性和数据结构。

比如对元素的背景颜色进行过渡：  
```javascript
d3.select("body").transition()
    .style("backgroud-color", "black");
```

此外还可以为一组元素设置不同的延迟：
```javascript
d3.selectAll("circle").transition()
    .duration(750)
    .delay(function(d, i) { return i * 10;} )
    .attr("r", function(d) { return Math.sqrt(d * scale); });
```

当然，除了 D3 提供的过渡之外，你也可以通过CSS动画来实现对元素的过渡效果。




---

相关资料：  
[D3](d3js.org)
[D3 API Reference](https://d3js.org.cn/document/)