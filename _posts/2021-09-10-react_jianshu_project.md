---
title: "React简书项目技术总结" 
categories: ['前端']
tags: ['frontend', 'react']
resource_path: /blog/assets/2021/09/10
---

# React 简书项目实战

Table of Contents

- [React 简书项目实战](#react-简书项目实战)
  - [使用 styled-components 为组件添加样式](#使用-styled-components-为组件添加样式)
  - [使用 reset.css 覆盖浏览器默认样式](#使用-resetcss-覆盖浏览器默认样式)
  - [使用 iconfont 嵌入头部图标](#使用-iconfont-嵌入头部图标)
  - [使用 react-transition-group 实现搜索框动画效果](#使用-react-transition-group-实现搜索框动画效果)
  - [使用 Redux, React-Redux 进行应用数据的管理](#使用-redux-react-redux-进行应用数据的管理)
  - [使用 immutable.js 来管理 store 中的数据](#使用-immutablejs-来管理-store-中的数据)
  - [使用 redux-immutable 统一数据格式](#使用-redux-immutable-统一数据格式)
  - [热门搜索样式布局](#热门搜索样式布局)
  - [Ajax获取推荐数据](#ajax获取推荐数据)
  - [代码优化微调](#代码优化微调)
  - [热门搜索换页功能实现](#热门搜索换页功能实现)
  - [换页旋转动画效果的实现](#换页旋转动画效果的实现)
  - [避免无意义的请求发送](#避免无意义的请求发送)

---

## 使用 styled-components 为组件添加样式

1. 网址: https://styled-components.com/

2. 概述: 一个 css-in-js 思想下的, 方便写css的库。

3. 为什么使用它? 在使用`create-react-app`创建的项目中, 如果在一个地方以`import xxx.css`的形式导入某个样式文件, 这个样式文件会被所有的组件共享, 需要注意不同的样式文件中不要起相同的类名. 要想每个组件使用不同的样式文件, 我们可以借助于[`styled-components`](https://styled-components.com/). 此外，我们可以利用js的语法，来增强css，而不是像 sass，less 等通过预处理器来增强css。

4. 原理: **styled-components generates an actual stylesheet with classes, and attaches those classes to the DOM nodes of styled components via the className prop.**

5. 为什么使用它? (特性): 包括刚才说的, styled components 具有以下特性:
    - **利用js来增强css**.
    - **Automatic critical CSS**: 样式化组件跟踪页面上呈现的组件，并完全自动地注入它们的样式。
    - **No class name bugs**: 为样式生成唯一的类名.
    - **Easier deletion of CSS**: 很难知道样式文件中的某个类作用于代码中的哪个地方. styled-components 使得所有样式都紧密的与某个组件相关联, 很容易知道某个样式作用于哪里.
    - **Simple dynamic styling**: 根据组件的props或全局主题调整组件的样式是简单而直观的，无需手动管理几十个类.
    - **Painless maintenance**: 无需遍历其他文件来查找影响组件的样式，方便维护.
    - **Automatic vendor prefixing**: 按照当前的标准编写CSS，然后让样式化的组件处理其余部分。

6. 整体用法
    1. 使用yarn安装`styled-components`依赖.
    2. 编写样式组件. 当你定义样式时, 你是真的在创建一个普通的具有指定样式 React 组件.
    3. 用样式组件将目标组件包裹起来.

7. 具体用法-基本
    1. 创建样式组件
        1. 利用 tagged template literals 创建.  
            ```js
             const Thing = styled.div`color: blue`;
             const Thing = styled.div`color: ${props.color}`;
            ```
        2. 利用 style objects 创建.  
            ```js
            const Thing = styled.div({color: blue}); // 样式对象
            const Thing = styled.div(props => {color: props.color}); // 函数, 返回样式对象
            ```
    2. 继承
        1. 不改变目标组件的html元素类型, 直接继承
            ```js
            const Div = styled.div`color:blue`;
            const Div2 = styled(Div);
            ```
        2. 改变目标组件的html元素类型, 在目标组件上加`as`
            ```jsx
            const Div = styled.div`color:blue`;
            const link = <Div as="a" href="/">a link</Div>
            ```
        3. 父组件为自定义组件. `styled` 方法适用于所有的自定义的或者任何第三方组件, 只要他们将传入的 `className` 这一 prop 给到 DOM 元素上.
            ```jsx
            const Div = ({ className, children }) => (<div className={className}>{children}</div>);
            const StyledDiv = styled(Div)`color: palevioletred; font-weight: bold;`;
            ```
    3. 编写样式
       1. css预处理器: 使用stylis预处理器, 语法类似于scss.[#](https://styled-components.com/docs/basics#pseudoelements-pseudoselectors-and-nesting)
            1. 可以把样式组件本身看作是一种可以被选择器选择的div这样的元素类型, 在组件样式中用`&`来引用这一类型. 普通css的作用域是全局的, 但这里的作用域仅限于样式组件内, 默认是只作用于该组件的后代.
            2. `&&` 可以被用于提升组件的规则的 specificity.
        1. `css` helper function[#](https://styled-components.com/docs/api#css): 之前说的定义的样式都是绑定到某个HTML元素或是组件上的, 有时候只是想定义与元素无关的可以复用的css样式, 可以借助 `css`. props 会从父组件自动传递给 complexMixin. 不使用`css` 这一 helper function的话，complexMixin会得到 `"color: props => (props.whiteColor ? 'white' : 'black')"` 这一字符串结果。这是因为 template literals 中，会将表达式直接转成字符串，所以我们不可以在template literal 中使用函数。
            ```jsx
            const complexMixin = css`color: ${props => props.color}`
            const StyledComp = styled.div`${props => props.complex ? complexMixin: "color: blue"}`
            ```
    4. props
        1. 自动区分 props 与 attribute: styled-components 会自动对传入的属性做区分. 如果添加样式的目标是基础元素, 会从传入的属性拆分出其基本的 HTML 属性直接添加到元素上, 并将剩余属性作为 props.
        2. 附加额外的props: 为了避免不必要的只是向渲染组件传入某些 props 的嵌套, 可以使用 `.attrs` 构造器. 它使得你可以为一个组件附加额外的 props. 当多个样式组件嵌套或继承时子组件可以覆盖父组件的 `.attrs` 里重复的 props.
          ```jsx
          const TextInput = styled.div.attrs(props => {type: "text", .color: props.color || "red"})`color: ${props.color}`
          ```

8. 具体用法-高级
    1. Animation: 需要借助`keyframes`这一helper function来定义keyframe.
    2. React Native: 与 web-version 相比, 不可使用 `keyframes` 和 `createGlobalStyle`.
    3. Theming: 通过context api, theme 里定义的属性, 会放到 ThemeProvider 里的每个组件的props里. 可通过 function themes 设置子主题.
    4. Refs: 对目标是基本DOM元素的样式组件(如通过styled.div定义的)使用refs, 会返回潜在的DOM节点; 对目标是自定义组件的样式组件使用refs, 会返回组件实例. 总之, 可以将styled.div/styled(MyComponent) 看作只是为css属性们生成一个独一无二的class, 并给目标组件加了class而已. (虽然内部更复杂).
    5. Security: 不要把用户的输入作为样式, 会带来安全隐患. 如当 background 通过 url 设置, 且 url 是用户输入的话, 用户如果输入一个 api 的 url, 会直接调用该 api. [#](https://styled-components.com/docs/advanced#security).  
        ```jsx
        const userInput = '/api/withdraw-funds'
        const ArbitraryComponent = styled.div`
          background: url(${userInput});
          /* More styles here... */
        `
        ```
    6. Existing CSS: 由于 styled-components 的工作模式是, 生成一个类的样式表, 并将这个类通过`className`添加到节点上. 如果你要添加样式的目标组件已经有一个全局 class 的话, 需要注意一下结合的方式. 如果多个 class 里重复设置了同优先级的某个属性, 会采用最后一个 class 的设置.  
        ```jsx
        class MyComponent extends React.Component {
          render() {
            // Attach the passed-in className to the DOM node
            return <div className={`some-global-class ${this.props.className}`} />
          }
        }
        ```
    7. Referring to other components  
        ```jsx
        const link = styled.a``
        const Icon = styled.svg`
          ${Link}:hover & {
            fill: blue
          }
        `
        const Component = () => (<Icon><Link href="#"/><Icon>)
        ```
    8. Server Side Rendering

9. 为方便对比记忆, 这里单独列出四种用到括号的情况.  
    ```js
    // 普通通过 template literal 创建
    const Div = styled.div`background: ${props.background}`

    // 继承
    const Div1 = styled(Div)``

    // additional props
    const Div2 = styled.div.attr(props => ({
      background: 'red'
    }))`background: ${props.background}`

    /* 不使用 template literals, 直接利用对象创建 */
    // Style Objects - static
    const Div3 = styled.div({
      background: 'red',
    });
    // Style Objects - based on props
    const Div4 = styled.div(props => ({
      background: props.background
    }));

    ```

---

## 使用 reset.css 覆盖浏览器默认样式

1. 网址: https://meyerweb.com/eric/tools/css/reset/

2. 概述: 就是一个普通css文件, 定义了一些基本样式.

3. 为什么使用它?
   1. 为了保证页码在所有浏览器上的展示效果的一致性, 可以使用Reset.css, 覆盖掉浏览器默认样式.

4. 使用方式
   1. 直接把网站主页的css复制过来用就可以, 然后用`styled-components` 的 `createGlobalStyle` 设置为全局样式

5. 一些相关文章:
    - [到底该不该用 CSS reset?](https://www.zhihu.com/question/23554164/answer/34482613)
    - [Reset.css](https://meyerweb.com/eric/tools/css/reset/)
    - [Normalize.css](https://necolas.github.io/normalize.css/)
    - [About normalize.css](http://nicolasgallagher.com/about-normalize-css/)
    - [Applying Normalize.css Reset](https://blog.teamtreehouse.com/applying-normalize-css-reset-quick-tip)

---

## 使用 iconfont 嵌入头部图标

1. 网址: https://www.iconfont.cn/

2. 概述: 阿里巴巴矢量图标库

3. 为什么使用它?
    1. 海量图标库
    2. 支持各种格式
    3. 方便团队协作

4. iconfont 的一般使用方法有三种: Unicode, Font class 以及 Symbol, 使用方法在下载解压后的`demo_index.html`里都有详细.

5. iconfont 结合 styled-component 的使用方式:
   1. 图标下载到本地解压后, 只取`iconfont.css`, `iconfont.eot`, `iconfont.svg`, `iconfont.ttf` 以及 `iconfont.woff` 五个文件即可. (其他文件是和使用说明相关的)  
      ![iconfont]({{page.resource_path}}/iconfont.png)

   2. 将`iconfont.css`中的绝对路径修改为相对路径. 并只保留`@font-family`和`.iconfont`  
     ![iconfont2]({{page.resource_path}}/iconfont2.png)

   3. 将css改为styled-component的格式. 注意 iconfont 应该作为全局样式.  
     ![iconfont3]({{page.resource_path}}/iconfont3.png)

   4. 挑选相应图标并获取字体变法, 应用于界面. 字体的unicode编码可以在`demo_index.html`中看到, 也可以在`iconfont.css` 或 iconfont网站上自己的对应项目中看到.
     ![iconfont4]({{page.resource_path}}/iconfont4.png)

---

## 使用 react-transition-group 实现搜索框动画效果

1. 网址: https://reactcommunity.org/react-transition-group/

2. 概述
    1. 暴露了组件 transition 的步骤, 使得用户可以在 transition 的不同阶段执行想要的操作
    2. 由四部分组成: *Transition*, *CSSTransition*, *SwitchTransition* 以及 *TransitionGroup*
    3. *Transition* 组件允许您使用简单的声明性API来描述一段时间内一个组件从状态到另一个状态的转换。常用于动画组件的安装和卸载，也可以用于描述原位过渡状态。
    4. *Transition* 组件是一个平台无关的组件. 如果使用CSS中的transition, 则最好使用*CSSTransition*. **后者继承了前者的所有功能**, 并添加了 css transtiion 相关的其他功能.

3. 核心思想
    1. 将 transition 的过程划分成多个阶段, 并将他们暴露出来, 在各阶段之间提供了slot 来供用户操作.
    2. > "exposes simple components useful for defining entering and exiting transitions…it does not animate styles by itself. Instead it exposes transition stages, manages classes and group elements and manipulates the DOM in useful ways, making the implementation of actual visual transitions much easier."
    3. 本质也是切换css类[#](https://github.com/reactjs/react-transition-group/blob/5007303e729a74be66a21c3e2205e4916821524b/src/CSSTransition.js#L118)

4. 为什么使用它?
   1. 比 css transition 的阶段划分更灵活.
   2. 可以在各个阶段中间插入回调函数, 回调函数中可以直接操作 dom.
   3. 其他?

5. *Transition* 与 *CSSTransition* 的区别
    1. `Transition` 的子元素为函数, `CSSTransition` 的子元素为组件或基本元素.
    2. *Transition* 在不同阶段为函数传入不同的状态字符串, *CSSTransition* 在不同阶段为组件设置不同的 css class.
    3. 比起*Transition*, *CSSTransition* 将 appear 阶段 (组件刚挂载且 in 为`true`) 从 enter 阶段 (in 为 `true`) 中拆了出来.
        1. *Transition* 的阶段与 slots:
            1. **当 in=true**: (onEnter) => **entering** => (onEntering) => **entered** => (onEntered)
            2. **当 in=false**: (onExit) => **exiting** => (onExiting) => **exited** => (onExited)
        2. *CSSTransition*的阶段与挂载的 css class (设*CSSTransition*的类名为 `*` ):
            1. **当 in=true 且组件首次挂载**: *-appear, *-appear-active, *-appear-done
            2. **当 in=true**: *-enter, *-enter-active, *-enter-done
            3. **当 in=false**: *-exit, *-exit-active, *-exit-done

6. 其他注意事项
    1. *Transition* 中, `appear` 属性默认为 `false`，即组件刚 mount 时不会触发 enter 。为 `true` 时会。
    2. `timeout={{appear: 1, enter: 1, exit: 1}}` 等价于 `timeout={1}`
    3. *-active 类表示你希望的动画结束时的样式, 所以**应该将`transition`属性放且仅在这里**, 否则最终效果可能不会如你所想.

7. *CSSTransition* 使用方式:
    1. `import {CSSTransition} from 'react-transition-group'`, 使用 `<CSSTransition>` 标签将要添加动画的组件包裹起来
    2. 设置一个 state 用来控制 transition 是 enter 还是 exit
        ```js
        class MyComponent{
          state = {inProp = true}
          /* ... */
        }
        ```
    3. 为 `CSSTransition` 标签指定属性, 有三个最重要的属性: `in`, `classNames` 和 `timeout`
        ```jsx
        <CSSTransition in={inProp} timeout={200} classNames="my-node">
          <div>
            {"I'll receive my-node-* classes"}
          </div>
        </CSSTransition>
        ```
    4. 设置相应的 css class. 注意, enter/active/exit三个阶段, 没到一个阶段就会把上一个阶段的class全部移除. 同一阶段内的三个类依次叠加.[#](https://github.com/reactjs/react-transition-group/blob/5007303e729a74be66a21c3e2205e4916821524b/src/CSSTransition.js#L201):
        ```css
        .my-node-enter{
          /* ... */
        }
        .my-node-enter-active{
          /* ... */
        }
        .my-node-enter-done{
          /* ... */
        }
        
        ```

---

## 使用 Redux, React-Redux 进行应用数据的管理

1. 使用目的: 简化组件之间的数据传递
2. 详细: 见之前的两篇博客: [Redux基础](https://viruspc.github.io/blog//%E5%89%8D%E7%AB%AF/2021/03/07/redux.html), [Redux进阶](https://viruspc.github.io/blog//%E5%89%8D%E7%AB%AF/2021/03/23/redux_advance.html)

---

## 使用 immutable.js 来管理 store 中的数据

1. 网址: [https://immutable-js.com/](https://immutable-js.com/)

2. 概述: 提供了一些常见数据结构的 immutable 版本, 方便创建 immutable 数据和将普通js数据转化为 immutable 数据. 另外还提供了支持 lazy evaluation 的 immutable 数据结构.

3. 核心思想: immutable. immutable data 一旦被创建, 不可被更改. immutalbe 的对象上的操作, 只会返回一个新的对象, 而不会对其本身做任何修改. immutalbe collections(List, Map, Set等) 应该被视为值而不是对象. 对象是可以随时间改变的, 而一个值代表着一个事物在某一具体时间的状态.

4. 为什么使用它?
    1. Simpler application development
    2. No defensive copying
    3. Enabling advanced memoization and change detection techniques with simple logic.

5. 使用方式:
    1. immutable.js 模拟了 ES2015 的 Array, Map, Set 等. 其上面的任何操作, 都会返回一个新的 immutable 对象.
    2. 创建: 可以很方便的利用 JS 的对象和数组, 来创建 immutable collection.
        ```js
        const map = Immutable.Map({ a: 1, b: 2, c: 3, d: 4 });
        const list = Immutable.List([1, 2, 3]);
        Immutable.Seq({ a: 1, b: 2, c: 3 })
          .map(x => x * x)
          .toObject();
        ```
    3. 嵌套结构:
        ```js
        const { fromJS } = require('immutable');
        const nested = fromJS({ a: { b: { c: [3, 4, 5] } } });

        const nested2 = nested.mergeDeep({ a: { b: { d: 6 } } });
        // Map { a: Map { b: Map { c: List [ 3, 4, 5 ], d: 6 } } }

        console.log(nested2.getIn(['a', 'b', 'd'])); // 6

        const nested3 = nested2.updateIn(['a', 'b', 'd'], value => value + 1);
        console.log(nested3);
        // Map { a: Map { b: Map { c: List [ 3, 4, 5 ], d: 7 } } }

        const nested4 = nested3.updateIn(['a', 'b', 'c'], list => list.push(6));
        // Map { a: Map { b: Map { c: List [ 3, 4, 5, 6 ], d: 7 } } }
        ``` 
    4. immutable data 之间的比较: 不应该使用`===`, 应该使用`Immutable.is()` 或 `.equals()`. (虽然出于性能优化, 如果操作结果不对集合本身做任何修改, 会直接返回集合本身, 而不是新建一个对象再返回)
        ```js
        const { Map } = require('immutable');
        const map1 = Map({ a: 1, b: 2, c: 3 });
        const map2 = Map({ a: 1, b: 2, c: 3 });
        map1.equals(map2); // true
        map1 === map2; // false
        ``` 
    5. immutable data 的复制: 由于 immutable collections 应被视为值而非对象, 所以直接像"复制"一个数字那样, 直接让新的引用指向它即可.
        ```js
        const { Map } = require('immutable');
        const map = Map({ a: 1, b: 2, c: 3 });
        const mapCopy = map; // Look, "copies" are free!
        ``` 
    6. 转换回 JavaScript 对象:
        ```js
        const deep = Map({ a: 1, b: 2, c: List([3, 4, 5]) });
        console.log(deep.toObject()); // { a: 1, b: 2, c: List [ 3, 4, 5 ] }
        console.log(deep.toArray()); // [ 1, 2, List [ 3, 4, 5 ] ]
        console.log(deep.toJS()); // { a: 1, b: 2, c: [ 3, 4, 5 ] }
        JSON.stringify(deep); // '{"a":1,"b":2,"c":[3,4,5]}'
        ```
    7. Lazy Seq:
        ```js
        const oddSquares = Seq([1, 2, 3, 4, 5, 6, 7, 8])
          .filter(x => x % 2 !== 0)
          .map(x => x * x);
        ``` 

6. 管理 store 中的数据:
    1. 将 state 从普通对象, 改为 immutable 对象.
        ```js
        import {Search_Focus_Action, Search_Blur_Action} from "./actionTypes"
        import { fromJS } from 'immutable';

        const defaultState = fromJS({
          focused: false
        });
        ```
    2. 修改其他地方改值取值的方式.
       1. reducer.js
            ```js
            export default (state = defaultState, action) => {
              if(action.type === Search_Focus_Action){
                if(state.get("focused") === false) {
                  // immutable对象的set方法,会结合之前immutable对象的值和设置的值,返回一个全新的对象
                  return state.set("focused", true)
                }
              } else if(action.type === Search_Blur_Action){
                if(state.get("focused") === true) {
                  // return newState;
                  return state.set("focused", false);
                }
              }
              return state;
            }
            ```
        1. Header.js (使用了combineRedcuers, 将 Header 的reducer 命名为 header)
            ```js
            const mapStateToProps = (state) => {
              return {
                focused: state.header.get('focused')
              }
            }
            ```
    
7. 注意事项


## 使用 redux-immutable 统一数据格式

1. 网址: [https://github.com/gajus/redux-immutable](https://github.com/gajus/redux-immutable)

2. 概述: `redux-immutable` 用于创建一个与 Redux 的 `combineReducers` 相同的函数, 但是它创建的全局 state 是 immutable 的.

3. 核心思想: 利用 `immutable.js` 来增强 `combineReducers`, 使得其自动生成的全局 state 变成了一个 immutable 对象.

4. 为什么使用它?
    之前的代码看起来很难受. 这是因为, combineReducers 生成的全局 state 是一个普通对象, 通过点或中括号来获取属性值. 而组件自己的 reducer 中使用了 immutable.js, 返回组件的state是 immutable 对象. 我们可以直接让 `combineReducers` 生成的全局 state 是 immutable 的.

    ```js
    // globle reducer.js
    export default combineReducers({
      header: headerReducer
    })
    // Header.js
    const mapStateToProps = (state) => {
      return ({
        focused: state.header.get('focused')
      })
    }
    ```

5. 使用方式:
    1. 使用 `redux-immutable` 中的 `combineReducers`, 来替代 Redux 中的 `combineReducers`:
        ```js
        //import { combineReducers } from 'redux';
        import { combineReducers } from 'redux-immutable';
        ```
    2. 修改组件获取state的方式:
        ```js
        const mapStateToProps = (state) => {
          return {
            // focused: state.header.get("focused")
            // focused: state.get("header").get("focused")
            focused: state.getIn(["header", "focused"])
          }
        }
        ```

## 热门搜索样式布局



## Ajax获取推荐数据

## 代码优化微调

## 热门搜索换页功能实现

## 换页旋转动画效果的实现

## 避免无意义的请求发送

---

Reference:
- [React开发简书项目 从零基础入门到实战](https://coding.imooc.com/class/229.html)