---
title: 'React: 核心概念'
categories: ['前端']
tags: ['前端', 'React', 'javascript']
resource_path: /blog/assets/2020/08/09/react_main_concepts
---

React: 核心概念
===

---

## 目录

1. [React 特性](#react-特性)
2. [JSX 的基本使用](#jsx-的基本使用)
3. [组件的基本使用](#组件的基本使用)
4. [组件属性: state](#组件属性-state)
5. [组件属性: props](#组件属性-props)
6. [组件属性: refs与事件处理](#组件属性-refs与事件处理)
7. [组件: 组合使用](#组件-组合使用)
8. [组件: 受控组件与非受控组件](#组件-受控组件与非受控组件)
9. [组件: 生命周期](#组件-生命周期)
10. [虚拟 DOM 与 DOM Diff 算法](#虚拟-dom-与-dom-diff-算法)
11. [React 中的 key](#react-中的-key)

---

## React 特性

1. 声明式，而非直接操作 DOM 的命令式

2. 组件化，而非 js 那样的模块化，提高复用性

3. 一次学习，随处编写（React native）

4. 单向数据流，而非 Vue 那样的双向数据流.  父组件可以向子组件传递数据, 子组件可以读取但不要直接对数据进行修改. 修改时 react 会报只读错误. 这样方便维护和 debug, 若渲染和期望不一致直接去父组件找问题就可以, 不需要挨个查子组件是否也有问题.

5. 高效（虚拟 DOM，编码人员不直接操作 DOM。DOM Diff算法，最小化页面重绘）

6. 视图层框架. 复杂项目需借助 redux 等数据层框架.

7. 函数式编程. 方便测试, 方便拆分复杂函数等.

8. 可以与其他框架并存. 只管理指定的 DOM 节点.

## JSX 的基本使用

1. jsx = javascript + xml

2. 虚拟 DOM 可直接用 javascript 表达式创建, 也可以用 jsx 表达式创建.

3. 先运行 javascript, 再运行 babel。故不论先后顺序, babel 标签中的代码可以引用 javascript 标签中定义的变量. (javascript中只有两个作用域: 全局作用域和函数作用域. 对于 let 定义的变量有块作用域)

4. babel 会将 jsx 表达式转化为 javascript 表达式(React.createElement(component, props, ...children))。

5. React element （虚拟 DOM）格式如下：   
    ```javascript
    const element = {
       type: "h1",
       props: {
           className: "greeting",
           children: "hello, world!"
       }   
   }
    ```  
   1. 虚拟 DOM 比较轻，真实 DOM 比较重。 虚拟DOM上无需有过多的 API。  
   2. 编码时只操作虚拟 DOM。
   3. 虚拟 DOM 最终都被被转换为展示的 DOM。

6. js 中可以使用 jsx 表达式；jsx 表达式中也可以嵌套 js 表达式（js 语句不可）。可层层嵌套。

7. 比起 HTML，jsx 更像是 js。React DOM 使用驼峰式属性命名方式而非 HTML 属性名。
    比如：jsx 中 ```class``` 应改为 ```className```（js 中 ```class``` 为关键字，jsx中有可能嵌套js，避免歧义)。
    ```tabindex``` 应该为 ```tabIndex```。```onclick```应改为```onClick```。

8. jsx 中不允许有多个根标签。一个虚拟 DOM 里最顶级标签唯一。这也导致一个问题: 会有很多多余的根标签, 使得 DOM 树变得比较"深". 

9. 要想解决上条带来的问题, 在 React 16 之后的版本可以使用 ```Fragment```标签, 用它来做最外层的标签. ```Frangment```最后不会转换成任何标签, 只做占位符. 它还有一个短语法, 可以直接写成空标签```<></>```
```jsx
import Fragment from 'react'
//...
return (
  <Fragment>
    <div><input/><button>submit</button></div>
    <ul>
        <li>Learning English</li>
        <li>Learning React</li>
    <ul>
  </Fragment>
)
```

9. 由于 jsx 表达式会被编译成 js，jsx 中可以使用 if-else 等 js 控制语句，函数定义等。

10. 建议把 jsx 表达式用括号括起来，避免自动插入分号的陷阱。

11. jsx 表达式中, 不可以直接写内联样式, 样式的值不能直接设为字符串, 而是应该设为一个对象(键值对形式).  
    ```jsx
    <h1 style={ {opacity: 0.5, width: "30px"} }></h1>
    ```

12. react 不推荐使用 a 标签. 非要使用就用 ```onClick```来跳转(```href```属性随便设置一个锚点, 不可为空, 不可为普通链接, 不可为javscript函数...).  
    ```jsx
        <a href="#1" onClick=(this.func)>
    ```

13. jsx中推荐在花括号内写注释而非用按xml写注释. 这样最后注释不会生成到html文件中.
    ```jsx
    (<Fragment>
        {/* this is comment */}
        <div></div>
    <Fragment>)
    ```

14. 尽量不要在render返回的jsx表达式中参杂复杂逻辑. 如果必须要复杂逻辑(如TodoList中需要用map来生成子项), 就抽象出一个函数来, 再在jsx表达式中调用该函数.
    ```jsx
    render() {
        return (<ul>
                    {this.getTodoItems()}
                </ul>);
    }
    getTodoItems = () => {
        return this.state.list.map((item, i) => (
            <Fragment key={item.id}>
                <TodoItem item={item} onClick={() => this.handleItemDelete(item.id)} />
            </Fragment>
        ))
    }
    ```

## 组件的基本使用

1. 组件名必须以大写字母开头

1. 简单组件用工厂模式创建（无state）。复杂的用ES6类组件创建（含state）。
    ```jsx
    // 第一种定义组件的方式，使用工厂函数。首字母大写
    function MyComponent() {
        console.log(this);  // 工厂函数中的this是undefined
        return <h1>我是工厂函数创建的组件</h1>
    }

    class MyComponent2 extends React.Component{
        // 重写父类继承的render
        render(){
            console.log(this);  // Mycomponent2组件类的实例对象
            return <h1>我是ES6类组件创建的组件</h1>;
        }
    }
    ```

2. 渲染时,通过组件类标签渲染. html 标签都是小写，react 组件标签为大写，且自闭合：```<MyComponent/>```。
    ```jsx
    ReactDOM.render(<MyComponent2/>, document.getElementById("example2"))
    ```

3. 多个组件插入某个节点，需新建一个组件 将多个组件包裹。直接在某个节点上 render 会发生覆盖。  
    ```jsx
    return (<div> <MyComponent1/> <MyComponent2/> </div>)
    ```  

4. 若组件通过类来定义, 使用react组件标签时会创建组件类的实例对象. 然后会调用里面的```render()```方法来获取 jsx 表达式.

5. ```Hook```是 *React 16.8* 的新增特性. 它可以让你在不编写```class```的情况下使用```state```以及其他的 React 特性.



## 组件属性: state

1. ```state```是组件类的实例对象的最重要的属性，其值是一个对象。

2. 组件被称为"状态机"，通过更新组件的```state```来更新对应的页面显示（重新渲染组件）。```state```更新，页面就自动更新。

3. 为了使事件处理函数可以修改组件的```state```，需要将该函数与组件类实例绑定起来。（事件处理函数不通过组件类实例调用，将事件处理函数赋值给其它变量后```this```丢失，```this```不指向组件类实例，为```undefined```。```render```中```this```指向组件实例）。

4. javascript 中，实例的方法不会自动绑定this。(通过哪个实例调用，哪个实例就是```this```，不通过实例调用就是```undefined```)

5. 结合2、3、4，为了使事件处理函数中与组件类实例绑定在一起，需要手动将函数与组件类实例绑定。

6. 箭头函数中没有```this```，函数内如果用到```this```, 就会自动在函数定义时就向外层寻找```this```来绑定, 而不是在调用的时候根据调用位置(执行环境)来决定```this```。函数体内的```this```对象就是定义时所在的对象，而不是调用时所在的对象。可通过匿名函数来定义事件处理函数，以实现函数与组件的绑定。  
    ```jsx
    myMethod = () => {
        console.log(this);  // 自动绑定 this
        this.setState({isHot: !this.state.isHot});
    }
    ```

7. 箭头函数中```this```是组件的实例对象. 不可以直接用```confirm```, 需要通过```window.confirm```来使用: 当直接使用```confirm```时, 会去```this```查找, 不会去```window```查找. ```alert```可以正常使用.

8. 组件实例对象的state：1.不能直接修改2.不能直接更新  
    * 正确范例:  
        * ```js
            comments = [{}, {}, {}];
            // ...
            let comments = [...this.state.comments]; 
            // do some change on comments.
            this.setState({comments: comments}) // 是修改操作不是覆盖操作，属性有则修改，无则不动。
            ```
        * ```js
            comments = [{}, {}, {}];
            // ...
            // 新版react16推荐下面的写法, 这样react会把短时间内连续多个setState合并.
            this.setState((prevState) => {
                const {comments} = prevState;
                // do some change on comments.
                return {comments: comments};
            }); 
            ```
    * 错误范例:
        * ```js
            this.state.comments.push(comment);  // 需要通过setState方法来更新
            ```
        * ```js
            this.state.comments.push(comment);
            this.setState({comments: this.state.comments});  // react提倡immutable, 不要直接对state进行修改, 不利于后面的优化.
            ```
        * ```js
            let comments = this.state.comments;
            this.setState({comments: comments});  // 可能出现不更新的情况, 尤其是当comments内容为对象时.
            ```

9. 为标签添加事件时, 记得加```this```  
    ```jsx
    render(){
        //render
        let {isHot} = this.state;
        return <h1 className="demo" onClick={this.changeWeather}>今天天气很{isHot ? "炎热" : "凉爽"}</h1>
    }
    ```

10. ```render```[调用的次数为 1+n](#组件:-生命周期), 1是页面初始化时的调用次数, n是状态```state```更新的次数.

11. 一个原则: 状态在哪儿, 更新状态的函数就在哪儿.

12. ```setState()``` 将组件状态的更改放入一个队列. 应该视```setState()```为一个请求而不是立即执行的命令. 为了性能上的提升, React 可能会延迟更新, 然后一下子更新多个组件. [#1](https://reactjs.org/docs/faq-state.html#what-is-the-difference-between-passing-an-object-or-a-function-in-setstate)[#2](https://reactjs.org/docs/state-and-lifecycle.html)

13.  ```setState()```不总是立即更新组件, 它可能会晚些进行批处理或延迟更新, 这使得在调用完```setState()```后立刻读取```this.state```或是获取state对应的真实DOM会读取到更新前的数据.

14. 为什么传入函数而不是传入对象: 二者在事件处理函数中都是异步的. 但传入函数允许编程人员在函数内安全的通过参数访问当前的state. 由于```setState```调用是批处理的, 这使得你可以将更新串起来, 并且保证他们按序执行, 避免冲突. [#](https://reactjs.org/docs/faq-state.html#what-is-the-difference-between-passing-an-object-or-a-function-in-setstate)

## 组件属性: props

1. 官网各类型示例: https://reactjs.org/docs/typechecking-with-proptypes.html

1. ```state```用于保存一些用于动态变化的数据. ```props```用于保存组件标签的属性.  

2. 设想在信息管理中,需要展示很多人的信息.这些人都具有相同的一些属性(姓名, 性别, 年龄). 我们可以把每个人的信息的结构抽象为一个组件. 但是会遇到一个问题: 如何把把不同人的信息传递给组件, 使得可以使用同一个组件类创造出包含不同信息的实例. 在创建类时, 我们可以通过构造函数传参来产生具有相同属性, 不同属性值的实例. 类似的, 我们可以通过设置组件标签属性来向组件内传递信息. 组件标签属性的键值对就保存在```props```中, 通过```this.props.attrName```来调用.  

3. ```PropTypes```用于限制传递标签属性的: 类型, 必要性.  
    ```javascript
    import PropTypes from 'prop-types';
    //...
    class MyComponent extends Component{
        static propTypes = {
            gender: PropTypes.oneOf(['Male', 'Female'])  // 二选一字符串, 非必需
            age: PropTypes.instanceOf(Number),  // 数值, 非必需
            item: PropTypes.exact({  // 用于限制对对象, 比objectOf和shape更严格
                id: PropTypes.string.isRequired,
                name: PropTypes.oneOfType([PropTypes.string, PropTypes.string]), // 二选一类型
            }), 
            onClick: PropTypes.func
        }
        //...
    }
    ```

4. 可通过设置组件类的```defaultProps```属性来设置默认值.  
    ```javascript
    import PropTypes from 'prop-types';
    //...
    class MyComponent extends Component{
        static defaultProps = {
            name: "anonymous",
            age:10
        }
        //...
    }
    ```

5. ```propTypes```在运行阶段检查, typescript 在编译阶段检查.

5. 现实开发中一般不限制```props```的类型和必要性, 也不设置默认值. 组件要传递的属性太多时, 写起来比较麻烦...

6. 传递标签属性的方式:
    ```jsx
    ReactDOM.render(<Person name={persons.name} gender={person.gender} age={person.age}/>, document.getElementById("example1"));  // 挨个传递
    ReactDOM.render(<Person/>, document.getElementById("example2"));  // 省略某些属性的传递
    ReactDOM.render(<Person {...person}/>, document.getElementById("example3"));  // 一起传展开传递: Object
    ```

7. ES8语法: 浅复制一个对象: ```personCopy = {...person}```  
    注意,对象默认不可迭代, 不可直接通过扩展运算符展开(```console.log(...person);  /// error```).

8. 虽然对象默认不可展开, 但我们在类标签中依然可以使用```...person```, 这是 react 和 babel 配合的结果. 在 js 脚本中这种写法会报错. 在 babel 脚本中, 标签之外这种写法不会报错, 也不会输出. 在标签内写可以一下子将属性批量传递过去.  
    * 传参:  
        ```jsx
        comment={id:"", name:"", content:""}
        return <Item {...comment}/>;
        ```
    * 使用:
        ```jsx
        let {id, name, content} = this.props;
        ```

9. 工厂函数组件与```props```.  
    为工厂函数添加参数, 组件标签的属性就会被传递到工厂函数内. 传递标签属性的方式与 6. 相同.  
    ```jsx
    function Person({name, age, sex}) {
        return (
            <ul>
                <li>name</li>
                <li>age</li>
                <li>sex</li>
            </ul>
        )
    }
    ```
10. 要传递的属性名不能为 key. key 有[特殊作用](#react-中的-key), 会被底层拦截. 类标签的```key```属性不会放到```props```中.  
    ```jsx
    <Item key="213213"/>
    ```

## 组件属性: refs与事件处理

1. react 中并不建议使用ref, 更推荐使用数据驱动的方式编写代码, 尽量不要直接操作 DOM.

1. 可以为标签增加一个```ref```属性, 其值唯一, 用于引用. 组件类的实例就会将该标签的DOM元素保存到自己的```refs```属性中. 这样就可以在方法中引用相应的标签的DOM元素了. 假设一个标签的```id```和```ref```值都为```input```, 则:   
    ```jsx
    console.log(document.getElementById("input1") === this.refs.input1);  // true
    ```

2. 定义和使用 ref 的三种语法:
    1. 字符串 ref 方式(方便, 不推荐) 
        1. 在```render```方法中定义:  
            ```jsx
            <input type="text" ref="input1">
            ```
        2. 类方法中使用引用的 DOM 元素
            ```javascript
            this.refs.input1
            ```
    2. 回调 ref 方式  
        1. 在```render```方法中定义:  
            ```jsx
            <input type="text" ref={(input) => {this.input1 = input}}/>
            ```  
        2. 类方法中使用引用的 DOM 元素:
            ```javascript
            this.input1
            ```
         令```ref```为一个回调函数. 回调函数在渲染时由 React 自动调用, 其参数是该标签的 DOM 元素本身. 标签不是放在组件实例的```refs```属性中, 而是直接作为组件实例的一个顶层的属性. ref 回调函数中, ```this```是组件类类实例.
    3. createRef 方式(推荐)
        1. 类中定义成员变量:
            ```jsx
            // 创建 ref 容器. 专人专用
            input1 = React.createRef();
            ```
        2. ```render```方法中引用  
            ```jsx
            // 不是为 ref 赋值, 而是将 ref 所在的标签元素放入定义好的容器中
            <input type="text" ref={this.input1}>
            ```
        3. 类方法中使用引用的 DOM 元素
            ```javascript
            this.input1.current
            ```
        将标签元素放入定义好的 ref 容器中.

3. 事件处理函数可以有一个参数```event```, 代表当前事件(参考```DOM```). 能用它处理, **就不要用**```ref```.
    * 函数定义:
        ```javascript
        onClickHandler = (event) => {
            alert(event.target.value);
        }
        ```
    * 函数引用:
        ```jsx
        <Button onClick={this.onClickHandler}>button</Button>
        ```
4. 如果事件处理函数还想要传递其它参数, 则需要再加个箭头函数的壳, 或是利用bind方法.
    * 函数定义:
        ```javascript
        onClickHandler = (id)=>{
            console.log(id);
        }
        ```
    * 函数引用(箭头函数):  
        ```jsx
        render() {
            let {id} = this.props;
            return <Button onClick={()=>{this.onClickHandler(id)}}>button</Button>;
        }
        ```
    * 函数引用(bind)(最好在constructor里bind. 下面这样会影响性能):
        ```jsx
        render() {
            let {id} = this.props;
            return <Button onClick={this.onClickHandler.bind(this, id)}>button</Button>;
        }
        ```

5. 自定义的组件并不是一个真实的DOM元素，不是最终渲染的页面的元素, 不存在事件。所有的事件处理函数都必须要绑定到真实的DOM上。如果试图为组件添加```onClick={this.onClickHandler}```，组件只会认为它是个名为onClick的```prop```.


## 组件: 组合使用

1. 功能界面的组件化编码流程(**通用/无比重要**)
    1. 拆分组件: 拆分界面, 抽取组件(有交互的单独拆分组件, 没交互的放到外壳中)
    2. 实现静态组件: 使用组件实现静态页面效果
    3. 实现动态组件 
        1. 动态显示初始化数据
            1. 数据类型
            2. 数据名称
            3. 保存在哪个组件
        2. 交互(从绑定监听事件开始)

2. 组件之间传递数据(一般利用[组件之间的通信技术]()):
    * 父组件向子组件传递数据  
        利用[props](#组件属性:-props)
    * 子组件接收父组件数据  
        * 不需要修改```state```等操作, 可以直接在```render```中通过```this.props```来接收
        * 需要修改```state```或其它操作, 要在[componentWillReiceiveProps](#组件-生命周期)中接收, 可以在里面利用```props```修改```state```并重新渲染组件.
    * 子组件向父组件传递数据  
        子组件不可直接向父组件传递数据. 子组件要想修改父组件的属性, 就需要父组件先自己有一个修改该属性的方法, 绑定上父组件实例(```this```)后, 将该方法传递给子组件. 然后子组件再通过调用该方法修改父组件的属性(如```state```).
    * 父组件接受子组件的数据  
        需要将一个子组件的数据传递给另一个子组件时, 需要新建```state```属性来保存. (```state```发生变化后会重新渲染, 此时通过```props```将```state```中的数据传递过去即可)

3. 单向数据流, 父组件可以向子组件传递数据, 子组件可以读取但不要直接对数据进行修改. 修改时react会报只读错误. 这样方便维护和debug, 若渲染和期望不一致直接去父组件找问题就可以, 不需要挨个查子组件是否也有问题.
    
4. jsx表达式中出现数组, 会将数组中的每一项都进行渲染. 一般这里会用到```map```函数将js数组里的每一项转化成jsx表达式, 并且要[为每一项设置一个key](#react-中的-key).  
    ```jsx
    <ul className="list-group">
        {comments.map( comment => <Item key={comment.id} {...comment}/>)}
    </ul>
    ```

5. react只是视图层框架, 有时还需借助数据层框架. 对于复杂的项目, 两个组件之间的通信可能会很麻烦, 需要从一个组件沿着虚拟dom树一层一层向上找到公共的组件组件, 再向下传递到另一个组件. 为了解决这个问题, 就产生了flux, redux等工具.

6. 子组件render被重新调用(重新生成虚拟DOM节点, 不一定会更新真实DOM节点)的两种情况: 父组件重新渲染, 或者给子组件传入新的props. (父组件不会直接改变子组件的state)
   

## 组件: 受控组件与非受控组件

1. 受控组件和非受控组件的区别在于: 是否将```html <input> ```, ```html <textarea> ```, ```html <select> ``` 等值可变的标签的值,自动维护到```state```中. 即在表单中, 受控组件可以在输入过程中自动收集数据, 非受控组件要在提交时手动读取数据.  

2. 设置受控组件:
    1. 为相应标签设置```onchange()```
        ```jsx
            <input type="password" onChange=(this.onChangeHandler)/>
        ```
    2. 设置```state```用于保存标签的值
        ```jsx
            state = {
                password: ""
            }
        ```
    3. 创建```onChangeHandler```方法, 当标签的```value```发生改变时, 存入```state```中
        ```javascript
            onChangeHandler = (event)=>{
                let password = event.target.value;
                this.setState({password: password});
            }
        ```


## 组件: 生命周期

1. 组件生命周期: 挂载 -> 更新 -> 卸载

2. 生命周期函数 == 生命周期钩子 == 生命周期回调函数 == 生命周期回调钩子(叫钩子是因为React底层有一个监视组件的监视者, 每当组件处于不同状态时, 就将这些特定函数钩到主线程来执行)

3. 生命周期(旧)  
    ![生命周期(旧)]({{page.resource_path}}/life_cycle_old.png)  
    1. 挂载  
        * 触发条件: ```jsx ReactDOM.render(<MyComponent/>```  
        * 流程: ```constructor()``` -> ```componentWillMount()``` -> *```render()```* -> ```compnentDidMount()```
    2. 更新  
        * 触发条件:  
            * 传入新的```Props```(父组件重新 render 子组件): 从```componentWillReceiveProps()```开始走流程
            * ```setState()```: 从```shouldComponentUpdate()```开始走流程
            * ```forceUpdate()```: 从```componentWillUpdate()```开始走流程  
        * 流程: ```componentWillReceiveProps()``` -> ```shouldComponentUpdate()``` -> ```componentWillUpdate()``` -> *```render()```* -> ```componentDidUpdate()```
    3. 卸载
        * 触发条件: ```ReactDOM.unmountComponentAtNode()```
        * 流程: ```componentWillUnmount()```

4. 生命周期(新)  
    ![生命周期(新)]({{page.resource_path}}/life_cycle_new.png)  
    1. 挂载  
        * 与旧版区别: ```getDerivedStateFromProps()```取代```componentWillMount()```
        * 触发条件: ```jsx ReactDOM.render(<MyComponent/>```  
        * 流程: ```constructor()``` -> **```getDerivedStateFromProps()```** -> *```render()```* -> ```compnentDidMount()```
    2. 更新  
        * 与旧版区别: 
            * ```getDerivedStateFromProps()```取代```componentWillReceiveProps()```和```componentWillUpdate```: 挂载和更新都需要通过这个钩子.
            * 将```shouldComponentUpdate()```放到```getDerivedStateFromProps()```与```render()```之间: 先修改状态, 再决定是否需要更新.
            * ```componentDidUpdate()```前增加```getSnapshotBeforeUpdate()```
        * 触发条件:  
            * 再次更新```props```(父组件重新 render)和```setState()```: 走完整个流程.
            * ```forceUpdate()```: 跳过```shouldComponentUpdate()```
        * 流程: **```getDerivedStateFromProps```** (-> ```shouldComponentUpdate()```) -> *```render()```* -> **```getSnapshotBeforeUpdate()```** -> ```componentDidUpdate()```
    3. 卸载
        * 触发条件: ```ReactDOM.unmountComponentAtNode()```
        * 流程: ```componentWillUnmount()```

5. 旧版生命周期函数  

    生命周期函数|描述  
    :-|:-  
    ```componentWillMount()```|**即将过时**. 已重命名为```UNSAFE_componentWillMount```. 组件将要被挂载,(初次渲染)  
    ```componentDidMount()```|组件挂载完毕. 只执行一次. 一般在此方法里开定时器, 发送Ajax请求等.  
    ```shouldComponentUpdate(props, state): boolean```|组件更新前判断是否允许执行更新. 返回一个布尔值用来说明是否更新.  
    ```componentWillUpdate(props, state)```|**即将过时**. 已重命名为```UNSAFE_componentWillUpdate```. 组件将要被更新(重新渲染)  
    ```componentDidUpdate(props, state[, value])```|组件更新完毕. 在旧版生命周期中使用时, 无```value```参数. 在新版中使用时, ```value```是```getSnapshotBeforeUpdate```方法的返回值.  
    ```componentWillUnmount()```|一般在此方法里做一些收尾和收集工作.  
    ```componentWillReceiveProps(props)```|**即将过时**. 已重命名为```UNSAFE_comopnentWillReceiveProps```. 组件将要接收**新的**参数. 挂载时不会触发. 从第二次传参开始被调用.  

6. 新版生命周期函数  

    生命周期函数|描述
    :-|:-
    ```static getDerivedStateProps(props, state)```|取代了旧版中的```componentWillMount```, ```componentWillReceiveProps``` 以及 ```componentWillUpdate```. 需要设为静态方法. 有两个参数和返回值. 返回值是新的```state```, 会作为下一步```setState(state)```的参数.
    ```getSnapshotBeforeUpdate(props, state): any```|新增的放置在```render```与```comopnentDidUpdate```之间的钩子. 需要返回一个任意类型的值. 必须与```componentDidUpdate(props, state, value)```一起使用, 返回值会作为其```value```参数.


6. componentWillxxx: 组件将要xxx. componentDidxxx: 组件做完了xxx. shouldComponentxxx: 组件是否应该xxx. 除了最后删除组件的钩子, 其它三个 componentWillxxx 即将过时.

7. 同```render()```, 旧版的生命周期函数不需要自己绑定 ```this```. ```getDerivedStateProps()```需要设为静态方法.

8. ```state```与页面的更新
    * ```state```更新, 页面不更新:  
        旧版: 令```shouldComponentUpdate()```返回```false```.  
        新版: 令```getDerivedStateProps()```返回空对象.
    * ```state```不更新, 页面更新:  
        方法中调用```this.forceUpdate()```或更新```props```(父组件重新render).
9. 为什么要在```componentDidUpdate(props, state)```之前添加一个```getSnapshotBeforeUpdate(props, state): any```, 并且传递一个```value```?  
    使得组件能在发生更改之前从 DOM 中捕获一些信息(例如:滚动位置). 此生命周期的任何返回值将作为参数传递给```componentDidUpdate()```


## 虚拟 DOM 与 DOM Diff 算法

1. 基本原理  
    1. 初始化显示界面
        1. 数据(state) + 模板(JSX) => 虚拟 DOM 树(一般 js 对象, 比[DocumentFragment](https://developer.mozilla.org/en-US/docs/Web/API/DocumentFragment)还简单)
        2. 虚拟 DOM 树 => 真实 DOM 树
        3. 绘制界面显示
    2. 更新界面
        1. ```setState()```/```forceUpdate```/new props
        2. 创建新的虚拟 DOM 树
        3. 新/旧虚拟 DOM 树比较差异, 得到 Patch(s)(需要修改的部分)
        4. 更新差异对应的真实 DOM
        5. 局部界面重绘

2. 虚拟 DOM + DOM Diff 算法: 最小化页面重绘.

3. 用js生成生成一个js对象(虚拟DOM), 代价是很小的; 生成一个 DOM 元素, 需要调用 web application 级别的API, 代价是非常大的. 生成虚拟 DOM 的时间 + DOM diff + 更新差异 DOM 节点的时间, 大多情况下小于直接更新整个DOM树的时间.

4. 重绘最小粒度为标签(DOM 节点).

5. 若父虚拟 DOM 节点发生变化, 子节点未发生变化, 则子虚拟 DOM 节点会继续重复使用(父节点变化, 子节点的```render```也会被调用, 但r```ender```后生成的是虚拟 DOM, 真实页面上子节点的DOM不一定会改变)。

6. 虚拟 DOM 的比对是一层一层的同层比对, 一旦发现一个节点不同, 就会替换以该节点为根节点的子真实 DOM 树的所有节点.

8. 虚拟DOM优点:
    1. 性能提升了
    2. 使得跨端应用得以实现. 相当于把虚拟DOM树作为中间语言. React Native


## React 中的 key

1. React Diff 算法依赖于 key. 

2. react 与 vue 里的 key 一样.

3. 虚拟 DOM 的 key 的作用?
    * 简单的说: key 是虚拟 DOM 对象的标识, 在更新显示时 key 起着极其重要的作用.
    * 详细的说: 当状态数据发生变化时, React 会生成新的虚拟 DOM, 随后 React 将之前的旧虚拟 DOM 与新的虚拟 DOM 进行 diff 比较.   
        以新的虚拟 DOM 树为基准， 对其中的每一个节点, 在旧的虚拟 DOM 树中寻找是否存在具有相同 key 的节点(一层一层的比对):  
        * 旧的虚拟 DOM 中**找到了**相同的 key:
            * 若虚拟 DOM 没变, 直接使用原来的真实 DOM , 不去刷新页面的真实 DOM.
            * 若虚拟 DOM 变了, 先更新虚拟 DOM, 随后刷新页面的真实 DOM. (子节点另外观察, 父节点更新, 子节点不一定更新)
        * 旧的虚拟 DOM 中**未找到**相同的 key:  
            * 根据 item 树创建新的DOM, 随后渲染真实DOM到页面

4. 为什么列表的 key 尽量不要用 index?  
    * 添加/删除/排序 => 会产生没有必要的真实DOM更新 => 界面效果没问题, 但效率低.  
        eg. 在列表的最前端添加一.这样原来的第一项就变成了第二项，第二项就变成了第三项...整个列表里的每一项的 key 都发生了变化, 都要重新渲染。
    * 如果 item 界面还有输入类的 DOM(输入框, 选择框等) => 产生错误的真实 DOM 更新 => 界面有问题  

    注意: 如果不存在 添加/删除/排序 操作, 仅用于渲染列表用于展示, 使用 index 作为 key 没有问题.  
    后端对于列表数据里的每一项都应设一个唯一标识, 用来作为 key. 若没有就去找后端沟通.

5. 虚拟 DOM 树以 key 为索引来构造. 假设存在一个 key 为 1 的虚拟 DOM 节点, 并有若干子节点. 若将它的 key 改为其他值, 并将另一个虚拟节点的 key 改为 1. 那么原来的那些子真实节点会原封不动的挂载到新的 key 为 1 的虚拟节点对应的真实节点上.  
    ![]({{page.resource_path}}/key_1.png) 
    ![]({{page.resource_path}}/key_2.png)

6. 一个可以用来全球唯一的字符串id的库: [UUID](https://baike.baidu.com/item/UUID/5921266?fr=aladdin).  
    * 安装:  
        ```bash
        yarn add uuid
        ```
    * 引入(一般都是默认暴露):  
        ```
        import 'uuid' from 'uuid'
        ```
    * 使用:
        ```
        let id = uuid();
        ```
