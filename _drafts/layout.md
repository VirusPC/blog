CSS Layout
===

Table of contents
- [CSS Layout](#css-layout)
  - [Box Model](#box-model)
  - [Normal flow](#normal-flow)
  - [Formattiing Context](#formattiing-context)
  - [Stacking Context](#stacking-context)

---

Box Model
---

CSS 中的所有元素都有一个 box 围着它。该模型定义了box的组成部分（*margin*, *border*, *padding* 和 *content*），以及如何各部分协同工作来创建一个可以在页面上看到的box。

CSS 中的 box 有两种: block box 和 inline box. block 与 inline也被称为 outer display type，规定了元素如何参与 flow layout，可以通过 `display` 属性来设置。通过 `display`, 我们还可以指定 inner display type, 指定 fomatting context。

顺带提一下，HTML 元素在历史上被分为 “block-level” 元素和 “inline-level” 元素。注意元素的分类不受 CSS 影响，比如即使 `div` 标签的 `display` 设为 `inline`, 也不影响它是 block-level element。而因为这是一个表示特性，所以它现在由 CSS 在 flow layout(normal flow) 中指定(表示特性全部归css管)。

后来，HTML5 不再将元素简单分为 block-level 和 inline-level，而是细分成了 flow content, phasing content(大致相当于之前的 inline-level element), sectioning content 等种类。这样做的原因之一就是防止 html 元素的分类与 css display 的类型的混淆。

Block box:
1. 会放到新的一行
2. 宽度为父元素的宽度，高度为内容的高度
3. 可以使用width和height属性来指定大小
4. 设置padding, border, margin, 会将相邻元素推开。
   
Inline box:
1. 除非本行放不下时才会另起一行，否则与上一个元素放在同一行
2. 大小等同于内容的大小
3. width, height属性无效
4. padding, border, margin 在纵向不会将相邻元素推开，在横向会。这也导致了元素在纵向可能会与其他元素重叠。
   
Box model有两种模式，通过`box-sizing`设置: `content-box` 和 `border-box`。

---

Normal flow
---

Normal flow 是当您不用 CSS 执行任何控制页面布局的操作时，浏览器默认情况下布局 HTML 页面的方式。

Normal flow 创建 Block Formatting Context. 其里面的元素还可以创建 Inline Formatting Context.

所有元素都 in-flow， 除了:
- floated 的项目: 设置 float 属性的元素
- 绝对定位的项目: 设置 position 为 absolute / fixed 的元素
- 根元素 (html)
  
Out of flow 的元素创建新的 Block Formatting Context (BFC)。因此所有在它们里面的东西都可以被视为 mini layout，与页面中的其他元素分开

---

Formattiing Context
---

---

Stacking Context
---

- [Introduction to CSS layout](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Introduction)
- [In Flow and Out of Flow](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flow_Layout/In_Flow_and_Out_of_Flow#taking_an_item_out_of_flow)
- [display](https://developer.mozilla.org/en-US/docs/Web/CSS/display)