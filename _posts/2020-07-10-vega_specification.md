---
title: 'Vega: specification'
categories: ['可视化']
tags: ['javascript', 'vega', 'visualization', '可视化']
resource_path: /blog/assets/2020/07/10/vega_specification
---

Vega: Specification
===

一个 Vega specification 是一个 JSON 对象，它描述了一个交互式可视化设计。在定义好 specification 后，我们就可以通过调用 Vega 提供的 API 来解析 specification，从而产生交互式可视化视图。

Vega specification 整体结构如下

```json
{
  "$schema": "https://vega.github.io/schema/vega/v5.json",
  "title": "title",
  "description": "A specification outline example.",
  "width": 500,
  "height": 200,
  "padding": 5,
  "autosize": "pad",

  "signals": [],
  "data": [],
  "scales": [],
  "projections": [],
  "axes": [],
  "legends": [],
  "marks": []
}
```

* [画布尺寸：width, height, padding, autosize](#width-height)
* [动态变量：signals](#signals)
* [数据集：data](#data)
* [比例尺：scales](#scales)
* [坐标轴：axes](#axes)
* [图形标记：marks](#marks)
* [图例：legends](#legends)
* [地图投影：projections](#projections)
* [其它信息：description, title，background, encode, usermeta](#description)

---

width, height
---

数字值。决定绘制数据的视图范围（data rectangle 组件）。额外的组件（axes, legends）一般会占用额外的空间(将它们放置在视图外面的padding所决定的区域中)。

padding
---

数字值。定义 data rectangle 之外的padding。它所定义的范围用于放置额外的组件。padding 在 autosize 完成之后进行。

autosize
---

字符串值。自动调整画布的 content 和 padding 或者组件大小。其属性值有以下几种选项：

* **"pad"**  
    默认值选项。自动设置padding，以增加空间来放置额外的 axes, legends 等组件。
* **"fit"**  
    自动修改各组件的大小。将额外组件也放入 data rectangle 中，而非像平常那样放入 padding 所定义的范围中。各组件成比例缩小。
* **"none"**  
    完全按照设置的 width, height 和 padding 来设置，不会自动修改padding或是各组件的大小。可能会造成额外的组件无法正常显示。

signals
---

signals 的属性值是 signal 数组。signal 是动态变量（与静态常量相对应），是可视化的参数，可以驱动交互行为。它的值是 reactive 的，可以对输入事件流、外部 API 调用或是上流signal（数据流中位于本 signal 之前的 signal）做出反应，使得值发生变化。其值的变化会传播到其他用到该 signal 的地方，可视化会自动重新渲染。

signal 在定义后可以在 specification 的其他部分被使用：假设有一个signal 的 name 是 ```indexDate```，则其他属性的属性值（包括表达式）中可直接引用它的名称 ```"expr": month(indexDate)```。

```json
"signals": [
  {
    "name": "indexDate",
    "description": "A date value that updates in response to mousemove.",
    "update": "datetime(2005, 0, 1)",
    "on": [{"events": "mousemove", "update": "invert('xscale', x())"}]
  }
]
```

以下是 signal 的一些属性：

* **name**  
    必选。signal 的一个独一无二的名字。"datum", "event", "item" 和 "parent" 是保留字，不可作为名字。
* **[bind](https://vega.github.io/vega/docs/signals/#bind)**  
    将 signal 绑定到一个外部输入元素，如slider，radio button 等。
* **description**  
    signal 的文本描述。用于行内注释、文档描述。便于维护。
* **on**  
    属性值为 [Handler](https://vega.github.io/vega/docs/signals/#handlers) 数组。指定 signal 响应的事件。
* **init**  
    属性值为初始化表达式。仅被调用一次。
* **update**  
    属性值为更新表达式。若表达式中包含位于其之前的其它 signal， 且 react 属性不为 false， 则会自动根据其之前的 signal 来进行更新。
* **react**  
    属性值为布尔值。为 true 则表示会根据其所依赖的上流的 signal 来自动重新计算。
* **value**  
    初始值。默认为 undefined。赋值先于 init 和 update。

data
---

数据集的定义和转换（transform）。在这里定义要加载的数据集以及如何对其进行处理。

```json
"data": [
  {
    "name": "stats",
    "format": {"type": "csv", "parse": "auto"},
    "source": "table.csv",
    "transform": [
      {
        "type": "aggregate",
        "groupby": ["x"],
        "ops": ["average", "sum", "min", "max"],
        "fields": ["y", "y", "y", "y"]
      }
    ]
  }
]
```

以下是 data 的一些属性

* **name**  
    必选。给数据集起一个独一无二的名字
    
* **format**  
    配合下面的 url 使用。用以指定加载的数据文件的格式。默认支持的格式是 json，除此之外还支持 csv，tsv，dsv 以及 topojson。format 的属性值是 "auto" 或者一个对象：若为"auto" 则表示运行自动类型引用来决定数据类型；若为对象则手动设置数据类型（boolean, date, number）。对象包含两个属性：type 和 parse。type 指明数据文件格式，可以为 "json"，"csv", "tsv"，"dsv" 或 "topojson"。parse的属性值是"auto"或一个对象，该对象用来设置数据类型（date 可能需要设置要以怎样的格式识别， 使用 [d3-time-format](https://github.com/d3/d3-time-format#locale_format) 语法）。  
    ```json
    "format": {
        "type": "csv",
        "parse": {
            "name": "string",
            "date": "date:'%m%d%Y'",
        }
    }
    ```

* **source, url, values**  
    三选一，用来指定数据集。
    * **source**  
        本数据集中的数据来自其它一个或多个数据集时使用（将它们合并）。当来源数据集多于一个时，要将他们的名字以数组的形式给出。在 tansform 时可能会有用。
    * **url**  
        给出要加载的数据集的 url 地址。可通过上面的 format 来指定文件格式。默认为 json。
    * **values**  
        直接给出完整的数据集定义。属性值是记录的数组。一个数据集含有多条记录，每条记录包含任意多个有名字的数据属性。一个记录相当于一个对象，一个数据集相当于由很多记录组成的列表（数组）。数据集定义如下：  
        ```json
         "values": [ 
            {"index": 0, "data": 5},
            {"index": 1, "data": 3}, 
            {"index": 2, "data": 8}, 
            {"index": 3, "data": 1}
         ]
        ```  
    
* **async, on, transform**  
    交互/动态可视化可能会用到
    * **async**  
        指定数据集的加载是异步还是同步。属性值可以为 true 或 false。
    * **on**  
        属性值是 [Trigger](https://vega.github.io/vega/docs/triggers/) 数组。是用于插入，移除，切换数据值，或是当触发器条件满足时清空数据的更新数组。例如：  
    * **transform**  
        属性值是 [Transform](https://vega.github.io/vega/docs/transforms/) 数组。

scales
---

Scales 将数据值（numbers，dates，categories，etc）映射为视觉值（pixels，colors，sizes）。Vega 使用 [d3-scale](https://github.com/d3/d3-scale) 库所提供的 scales。

```json
"scales": [
  {
    "name": "xscale",
    "type": "band",
    "domain": {"data": "table", "field": "category"},
    "range": "width",
    "padding": 0.05,
    "round": true
  },
  {
    "name": "yscale",
    "domain": {"data": "table", "field": "amount"},
    "nice": true,
    "range": "height"
  }
]
```

以下是 scale 的一些公共属性，不同类型的 scale 还有各自[独特的属性](https://vega.github.io/vega/docs/scales/#scale-types)：

* **name**  
    scale 的一个独一无二的名字。
* **type**  
    类比D3。scale的类型，默认为linear。
* **domain**  
    含两个元素的一维数字数组。类比D3。定义域。注意其属性值也可以设置为 signal 引用。
* **domainMax**  
    设置 domain 的上限，覆盖之前的 domain。
* **domainMin**  
    设置 domain 的下限，覆盖之前的 domain。
* **domainMid**  
    用于 diverging color scales。
* **domianRaw**  
    一个 raw values 的数组。若非空，则直接覆盖 domain。对于实现平移和缩放很有用。一开始先使用数据驱动的 domian 确定比例尺，然后通过设置 rawDomain 的值来修改域以响应用户输入。
* **interpolate**  
    类比 [D3-interpolate](https://github.com/d3/d3-interpolate)
* **range**  
    类比D3。值域。
* **reverse**  
    布尔值。将比例尺反向。
* **round**  
    布尔值。sclae 输出值取整。

axes
---

使用刻度线、网格线和标签来将 scale 可视化。Vega 目前支持笛卡尔坐标系下的坐标轴。axes 可以在规范的顶层定义，也可以作为组标记的一部分。

```json
"axes": [
  {
    "orient": "bottom",
    "scale": "x",
    "title": "X-Axis",
    "encode": {
      "ticks": {
        "update": {
          "stroke": {"value": "steelblue"}
        }
      },
      "labels": {
        "interactive": true,
        "update": {
          "text": {"signal": "format(datum.value, '+,')"},
          "fill": {"value": "steelblue"},
          "angle": {"value": 50},
          "fontSize": {"value": 14},
          "align": {"value": "left"},
          "baseline": {"value": "middle"},
          "dx": {"value": 3}
        },
        "hover": {
          "fill": {"value": "firebrick"}
        }
      },
      "title": {
        "update": {
          "fontSize": {"value": 16}
        }
      },
      "domain": {
        "update": {
          "stroke": {"value": "#333"},
          "strokeWidth": {"value": 1.5}
        }
      }
    }
  }
]
```

axe 有以下一些属性：

* 必选属性
    * **scale**  
        字符串值。必选。指出 axis 组件基于哪个 scale。
    * **orient**  
        字符串值。必选。指出 axis 的 ticks 的朝向。
* domain line(axis baseline) 相关
    * **domain**  
        布尔值。是否显示坐标轴基线。
    * **domainCap**  
        domain line [两端的形状](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/user-interface/graphics/skiasharp/paths/lines)。值为"butt", "round" 或 "square"
    * **domainColor**  
        domain line 的颜色。
    * **domainDash**  
        参考 [svg-dasharray](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/stroke-dasharray)
    * **domainOpacity**  
        domain line 的透明度。
    * **domainWidth**  
        domain line 的宽度。
* label 相关  
    * **labels**  
        布尔值，是否显示 labels。
    * **format**  
        设置 labels 的 format specifier。对于数字值，必须为合法的 [d3-format](https://github.com/d3/d3-format#locale_format) specifier。对于日期值，必须为合法的 [d3-time-format](https://github.com/d3/d3-time-format#locale_format) specifier 或 一个 [TimeMultiFormat](https://vega.github.io/vega/docs/types/#TimeMultiFormat) 对象.
    * **formatType**  
        指出 format 的类型。可以为 "number"，"time" 或 "utc"。
    * **labelColor**  
        labels 的颜色。
    * **labelAngle**  
        数字，单位为度。label 旋转角度。顺时针。一圈为360度。
    * **labelFront**  
        字符串。字体名字。
    * **labelFrontSize**  
        字体大小。
    * **labelPadding**  
        label 与 tick之间的距离。
    
* tick 相关  
    * **ticks**  
        布尔值，是否显示 tick。
    * **bandPosition**  
        数字值，一般取值范围 0-1。指明 band scale 中，ticks 的位置。为 0 则表示将 tick 置于 band 的最左侧。为 0.5 则表示将 tick 置于 band 中间。
    * **tickCap, tickColor, tickDash, tickOpacity, tickWidth**  
        参考 domain line 相关。
    * **tickCount**  
        若为数字，直接指定 tick 的数量。真实数量可能和设置的数量有所不同（真实数量是 nice 的，是2，5，10的倍数）。  
        对于 time scale 或 utc scale，tickCount 的值还可以为字符串或对象。合法字符串值为"millisecond", "second", "minute", "hour", "dat", "week", "month" 和 "year"。对象的格式类似于 ```{"interval": "month", "step": 3}```。
    * **tickMinStep**  
        相邻两个 tick 之间的最小距离。
* grid 相关  
    * **grid**  
        布尔值。是否显示网格线。  
    * **gridScale**  
        默认为必选属性里的 scale。也可另外定义。  
    * **gridCap,  gridColor, gridDash, gridOpacity, gridWidth**  
        参考 domain line 相关。  
* title 相关  
    * **title**  
        字符串值。坐标轴的名字。
    * **titleAnchor** 
        title 摆放的位置。"start", "middle", "end" 或 "none"（默认，自动确定）。
    * **titleX, titleY** 
        自定义相对于坐标轴的偏移量。 
    * **titleColor, titleAngle, titleFront, titleFrontSize**  
        参考 label 相关。
* 整体相关
    * **translate**  
        轴布局的坐标空间转换偏移。默认情况下，轴的 x 和 y 坐标都以 0.5 像素的偏移量进行转换，以使描边线与像素网格对齐。然而，对于矢量图形输出来说，这些针对像素的调整可能并不可取，在这种情况下，可以更改 translate（例如，为零）。
    * **values** 
        明确设置可见的轴刻度线和标签值。数组条目应该是支持刻度域中的合法值。
    * **zindex**  
        组件的深度。
* [其它](https://vega.github.io/vega/docs/axes/#axis-properties)   

marks
---

将数据编码为图形标记，比如条形图中的矩形、折线图中的线和其它符号等。mark 的属性可以是简单的常量或数据字段，也可以使用比例尺将数据值映射到视觉值。

vega 支持的 mark 类型有 arc, area, image, group（其它mark的容器，即 svg 中的 g 标签）, line（描线，通常用于显示随着时间推移而发生的变化）, path, rect, rule, shape, symbol, text 以及 trail（可以基于下层的数据来改变尺寸的线条）。

```json
"marks": [{
  "type": "rect",
  "from": {"data": "table"},
  "encode": {
    "enter": {
      "y": {"scale": "yscale", "field": "value"},
      "y2": {"scale": "yscale", "value": 0},
      "fill": {"value": "steelblue"}
    },
    "update": {...},
    "exit": {...},
    "hover": {...}
  }
}]
```

以下是 mark 的一些属性

* **name**  
    非必选。
* **type**  
    必选项。字符串。其值必须为[支持的 mark 类型](https://vega.github.io/vega/docs/marks/#types)。
* **clip**  
    一个对象。指出 mark 们是否需要被裁剪为某个特定的形状。  
    clip 是一个对象，它需要从 path 和 sphere 两个属性中二选一。path 是一个 svg path。sphere 在地图投影时会用到
* **encode**  
    一个对象。包含 mark 属性的视觉编码规则集合。
* **from**  
    一个对象。用于描述该 mark 需要可视化的数据。默认为空集合。其值可以为顶层定义的数据集的 name。也可以提供一个分面指令，将一个数据集细分为一组 group mark。
* **interactive**  
    布尔标志。默认为true。指明该 mark 是否可以作为输入事件源。
* **key**  
    一个数据字段，用作数据绑定的唯一键。当更新可视化的数据时，键值将用于将数据元素匹配到现有的标记实例。使用键字段使对象在动态数据上的转换具有恒定性。
* **on**  
    trigger 的集合。用于响应 signal 的变化，修改 mark 属性。
* **sort**  
    一个 [comparator](https://vega.github.io/vega/docs/types/#Compare)。
* **transform**  
    预先编码好的 [transform](https://vega.github.io/vega/docs/transforms) 的集合。在 ```encode``` 之后应用。
* **role**
    字符串。渲染为 svg group 时，将该值加上 ```role-``` 前缀后，作为该组的类名。
* **style**  
    一个字符串或数组。指明要应用于该 mark 的自定义 style 的名字。默认定义在顶层属性 [configuration](#configuration) 中。
* **zindex**  
    数字。设置该元素的深度。数字大的摆在上方。
legends
---

将 scale 可视化为颜色、形状或是尺寸视觉编码。

projections
---

对地图（经度、纬度）数据进行制图投影。

title
---

设置整个 chart 的标题。

以下是 title 的一些属性:  

* **text**  
    必选项。标题的文本。
* **orient**  
    放置标题的位置， "top"、"bottom"、"left"或"right"
* **[其它](https://vega.github.io/vega/docs/title/)**

description
---

整个可视化的描述。不显示出来，可被读屏软件读出。

background
---

设置整个视图的背景颜色（默认为透明）。

encode
---

编码指令，用于表示图表数据矩形的顶层组标记的视觉属性。例如，这可以用来设置绘图区域的背景填充颜色，而不是整个视图。

usermeta
---

可选元数据。将会被 Vega parser 忽略。
            








---

相关资料：  
[参数类型](https://vega.github.io/vega/docs/types/#String)   
[Specification 属性](https://vega.github.io/vega/docs/)