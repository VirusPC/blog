# React 简书项目实战

---

Table of Contents

- [React 简书项目实战](#react-简书项目实战)
  - [styled-components 与 Reset.css](#styled-components-与-resetcss)
    - [特性](#特性)
    - [基本用法](#基本用法)
    - [样式组件](#样式组件)
  - [使用 iconfont 嵌入头部图标](#使用-iconfont-嵌入头部图标)
  - [搜索框动画效果实现](#搜索框动画效果实现)
  - [使用 React-Redux 进行应用数据的管理](#使用-react-redux-进行应用数据的管理)
  - [使用 combineReducers 完成对数据的拆分管理](#使用-combinereducers-完成对数据的拆分管理)
  - [actionCreators 与 constants 的拆分](#actioncreators-与-constants-的拆分)
  - [使用 immutable.js 来管理 store 中的数据](#使用-immutablejs-来管理-store-中的数据)
  - [使用 redux-immutable 统一数据格式](#使用-redux-immutable-统一数据格式)
  - [热门搜索样式布局](#热门搜索样式布局)
  - [Ajax获取推荐数据](#ajax获取推荐数据)
  - [代码优化微调](#代码优化微调)
  - [热门搜索换页功能实现](#热门搜索换页功能实现)
  - [换页旋转动画效果的实现](#换页旋转动画效果的实现)
  - [避免无意义的请求发送](#避免无意义的请求发送)

---

## styled-components 与 Reset.css

在使用`create-react-app`创建的项目中, 如果在一个地方以`import xxx.css`的形式导入某个样式文件, 这个样式文件会被所有的组件共享, 需要注意不同的样式文件中不要起相同的类名. 要想每个组件使用不同的样式文件, 我们可以借助于[`styled-components`](https://styled-components.com/).

### 特性

包括刚才说的, styled components 具有以下特性:

- **Automatic critical CSS**: 样式化组件跟踪页面上呈现的组件，并完全自动地注入它们的样式。
- **No class name bugs**: 为样式生成唯一的类名.
- **Easier deletion of CSS**: 很难知道样式文件中的某个类作用于代码中的哪个地方. styled-components 使得所有样式都紧密的与某个组件相关联, 很容易知道某个样式作用于哪里.
- **Simple dynamic styling**: 根据组件的props或全局主题调整组件的样式是简单而直观的，无需手动管理几十个类.
- **Painless maintenance**: 无需遍历其他文件来查找影响组件的样式，方便维护.
- **Automatic vendor prefixing**: 按照当前的标准编写CSS，然后让样式化的组件处理其余部分。

### 基本用法

第一步, 使用yarn安装`styled-components`依赖.

第二步, 编写样式组件.

第三步: 用样式组件将目标组件包裹起来.

### 样式组件

1. **Getting Started**: `styled-components` 使用 tagged template literals 来为组件定义样式. 当你定义样式时, 你是真的在创建一个普通的具有指定样式 React 组件. 如```const Thing = styled.div`color: blue;` ``` 创建了一个颜色为 blue 的 div 组件. [#](https://styled-components.com/docs/basics#getting-started)

2. **Adapting based on props**: 可以为一个 styled component 的 template literal 传入一些函数 (interpolations), 以根据props (styled component 是 react component) 来调整样式. 如: ``` const Button = `background: ${props => props.primary ? "red" : "blue"}` ``` [#](https://styled-components.com/docs/basics#adapting-based-on-props)

3. **Extending Styles**: 为了方便一个组件继承另一个组件的样式, 直接将另一个组件包裹在 `styled()` 构造器中即可, 如`const StyledComponent2 = styled(StyledComponent1)`. 在某些情况下，继承后的组件可能需要更改 styled component 渲染的目标标签或组件, 这时候可以使用`"as" polymorphic prop`, 如`<Button as='a' href="/">button</Button>` [#](https://styled-components.com/docs/basics#extending-styles)

4. **Styling any component**: `styled` 方法适用于所有的自定义的或者任何第三方组件, 只要他们将传入的 `className` 这一 prop 给到 DOM 元素上. [#](https://styled-components.com/docs/basics#styling-any-component)

5. **Passed props**: styled-components 会自动对传入的属性做区分, 如果添加样式的目标是基础元素, 会从传入的属性拆分出基本的 HTML 属性直接添加到元素上, 并将剩余属性作为 props 传递给 interpolation. [#](https://styled-components.com/docs/basics#passed-props)

6. **Pseudoelements, pseudoselectors, and nesting**: [#](https://styled-components.com/docs/basics#pseudoelements-pseudoselectors-and-nesting)
    1. ampersand (`&`) 可以用来引用组件本身.
        ```js
        const Thing = styled.div`
          color: blue

          &: hover {
            color: red;
          }

          & + & {
            background: line;
          }
        `
        ```
    2. 如果使用不带 `&` 的 selector, 他们会指向组件的孩子.
        ```js
        const Thing = styled.div`
          color: blue;

          .something {
            color: red;
          }
        `
        ```
    3. `&` 可以被用于提升组件的规则的 specificity.
        ```jsx
        const Thing = styled.div`
          && {
            color: blue;
          }
        `

        const GlobalStyle = createGlobalStyle`
          div${Thing} {
            color: red;
          }
        `

        render(
          <React.Fragment>
            <GlobalStyle />
            <Thing>
             I'm blue, da ba dee da ba daa
            </Thing>
          </React.Fragment>
        )
        ```

7. **Attaching additional props**: 为了避免不必要的只是向渲染组件传入某些 props 的嵌套, 可以使用 `.attrs` 构造器. 它使得你可以为一个组件附加多余的 props. 当多个样式组件嵌套或继承时子组件可以覆盖父组件的 `.attrs` 里重复的 props. [#](https://styled-components.com/docs/basics#attaching-additional-props)
    ```js
    const Input = styled.input.attrs(props => ({
      type: "text",
      size: props.size || "1em",
    }))`
      border: 2px solid palevioletred;
      margin: ${props => props.size};
      padding: ${props => props.size};
    `;

    // Input's attrs will be applied first, and then this attrs obj
    const PasswordInput = styled(Input).attrs({
      type: "password",
    })`
      // similarly, border will override Input's border
      border: 2px solid aqua;
    `;

    render(
      <div>
        <Input placeholder="A bigger text input" size="2em" />
        <br />
        {/* Notice we can still use the size attr from Input */}
        <PasswordInput placeholder="A bigger password input" size="2em" />
      </div>
    );
    ```

8. **Animations**: [#](https://styled-components.com/docs/basics#animations)

---

## 使用 iconfont 嵌入头部图标

## 搜索框动画效果实现

## 使用 React-Redux 进行应用数据的管理

## 使用 combineReducers 完成对数据的拆分管理

## actionCreators 与 constants 的拆分

## 使用 immutable.js 来管理 store 中的数据

## 使用 redux-immutable 统一数据格式

## 热门搜索样式布局

## Ajax获取推荐数据

## 代码优化微调

## 热门搜索换页功能实现

## 换页旋转动画效果的实现

## 避免无意义的请求发送
