## styled-components 是什么？
* styled-components 是一个常用的 css in js 类库。和所有同类型的类库一样，通过 js 赋能解决了原生 css 所不具备的能力，比如变量、循环、函数等。

## 相对于其他预处理有什么优点？
* 诸如 sass&less 等预处理可以解决部分 css 的局限性，但还是要学习新的语法，而且需要对其编译，其复杂的 webpack 配置也总是让开发者抵触。
* 如果有过sass&less的经验，也能很快的切换到styled-components，因为大部分语法都类似，比如嵌套，& 继承等， styled-componens 很好的解决了学习成本与开发环境问题，很适合 React 技术栈 && React Native 的项目开发。

## 解决了什么问题？
* className 的写法会让原本写css的写法十分难以接受
* 如果通过导入css的方式 会导致变量泄露成为全局 需要配置webpack让其模块化
* 以及上面提到的解决了原生 css 所不具备的能力，能够加速项目的快速开发

## 官方文档
<https://www.styled-components.com/docs>

## 安装
`npm install --save styled-components`

## 使用

### 基础
```
import styled from 'styled-components'

const Title = styled.h1`
    font-size: 1.5em;
    text-align: center;
    color: palevioletred;
`;
// 相当于  const Title = styled.h1(xx)
const Wrapper = styled.section`
    padding: 4em;
    background: papayawhip;
`;
    render () {
        return (
            <Wrapper>
                <Title>Hello styled-components</Title>
            </Wrapper>
        )
    }
```
此时我们可以看到控制台中输出了一个随机的className，这是styled-components帮我们完成的. 注意: 组件名要以大些开头 不然会被解析成普通标签

### props传递
```
const Button = styled.button`
    background: ${props => props.primary ? 'palevioletred' : 'white'};
    color: ${props => props.primary ? 'white' : 'palevioletred'};
    font-size: 1em;
    margin: 1em;
    padding: 0.25em 1em;
    border: 2px solid palevioletred;
    border-radius: 3px;
`
render(
    <div>
        <Button>Normal</Button>
        <Button primary>Primary</Button>
    </div>
);
```
在组件传递的props都可以在定义组件时获取到，这样就很容易实现定制某些风格组件

### props高级用法
设置默认值，在未设定必须传值的情况下我们会给一个默认值(defaultProps)
```
export default class ALbum extends React.Component {

    constructor (props) {
        super(props)
        this.state = {
            // 接收传递的值
            imgSrc: props.imgSrc
        }
    }
    
    render () {
        const {imgSrc} = this.state
        return (
            <Container imgSrc={imgSrc}>
            </Container>
        )
    }
}

// 在这里是可以拿到props的 
const Container = styled.div`
    background-size: cover;
    background-image: url(${props =>  props.imgSrc});
    width: 100%;    
    height: 300px;
`

// 当然没传值也没关系  我们设置默认值
Container.defaultProps = {
    imgSrc: Cover
}
```
### 添加类名
有时，会在为元素添加类名，并在该类名下设置样式的需要
```
<Wrap className="test">

const Wrap= styled.div`
  &.test{
　　color: white;
  }
`
```
或者覆盖组件内部样式
```
const Wrapper = styled.div`
 & .detail {
   color: #ccc;
 }
`;

<Wrapper>
  <h4>Hello Word</h4>
  <div className="detail"></div>
</Wrapper>
```
### 塑造组件
你可能会遇到一些原本就已经是组件了 但是你要为他添加一些样式，这时候该怎么办呢 ?
```
// 传递className 在react-native 中要使用 style
const Link = ({className , children}) => (
    <a className={className}>
        {children}
    </a>
)

const StyledLink = styled(Link)`
    color: palevioletred;
`
render(
    <div>
        <Link>普通组件</Link>
        <StyledLink>有颜色吗？</StyledLink>
    </div>
);
```

### 组件样式继承
```
const Button = styled.button`
    color: palevioletred;
    font-size: 1em;
    margin: 1em;
    padding: 0.25em 1em;
    border: 2px solid palevioletred;
    border-radius: 3px;
`;
const TomatoButton = Button.extend`
    color: tomato;
    border-color: tomato;
`;
// TomatoButton 部分样式继承自 Button 这种情况下不会生成两个class
```

### 改变组件标签
如果我们想要改变组件的标签 比如把 button 变成 a 标签
* 利用上面定义的 Button 组件 调用 withComponent 方法
```
const Link = Button.withComponent('a')
```
* "as" polymorphic prop来改变组件标签 
```
const Button = styled.button`
  display: inline-block;
  color: palevioletred;
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;
`;

const TomatoButton = styled(Button)`
  color: tomato;
  border-color: tomato;
`;

render(
  <div>
    <Button>Normal Button</Button>
    <!-- 此处渲染为a标签 -->
    <Button as="a" href="/">Link with Button styles</Button>
    <TomatoButton as="a" href="/">Link with Tomato Button styles</TomatoButton> 
  </div>
);
```

### 维护其他属性
在某种情况下，我们可能需要用到第三方库样式，我们可以使用这个方法轻松达到
```
const Input = styled.input.attrs({
    // 定义静态 props
    type: 'password',
    // 没传默认使用 1em
    margin: props => props.size || '1em',
    padding: props => props.size || '1em'
})`
    color: palevioletred;
    font-size: 1em;
    border: 2px solid palevioletred;
    border-radius: 3px;
    // 动态计算props
    margin: ${props => props.margin};
    padding: ${props => props.padding}
`
render ( <Input size='1em'></Input>  <Input size='2em'></Input> )
```

### 动画
动画会生成一个随机类名 而不会污染到全局
```
import { keyframes } from 'styled-components'
// CSS 动画
const rotate360 = keyframes`
    from {
        transform: rotate(0);
    }
    to {
        transform: rotate(360deg);
    }
`
const Rotate = Button.extend`
    animation: ${rotate360} 2s linear infinite;
`
render ( <Rotate>  💅  </Rotate> )
```

### 兼容现在已有的 react components 和 css 框架
styled-components 采用的 css-module 的模式有另外一个好处就是可以很好的与其他的主题库进行兼容。因为大部分的 css 框架或者 css 主题都是以 className 的方式进行样式处理的，额外的 className 和主题的 className 并不会有太大的冲突
```
const Styled = styled(Row)`
  position: relative;
  height: 100%;
  .image img {
    width: 100%;
  }
  .content {
    min-height: 30em;
    overflow: auto;
  }

  .content h2 {
    font-size: 1.8em;
    color: black;
    margin-bottom: 1em;
  }
`;
```

### 媒体查询
```
const Content = styled.div`
  background: papayawhip;
  height: 3em;
  width: 3em;

  @media (max-width: 700px) {
    background: palevioletred;
  }
`;

render(
  <Content />
);
```
* 得益于javaScript的函数性质,你可以定义模板来实现媒体查询
```
const sizes = {
  desktop: 992,
  tablet: 768,
  phone: 576,
}

// Iterate through the sizes and create a media template
const media = Object.keys(sizes).reduce((acc, label) => {
  acc[label] = (...args) => css`
    @media (max-width: ${sizes[label] / 16}em) {
      ${css(...args)}
    }
  `

  return acc
}, {})

const Content = styled.div`
  height: 3em;
  width: 3em;
  background: papayawhip;

  /* Now we have our methods on media and can use them instead of raw queries */
  ${media.desktop`background: dodgerblue;`}
  ${media.tablet`background: mediumseagreen;`}
  ${media.phone`background: palevioletred;`}
`;

render(
  <Content />
);
```
* Object.keys：将json对象转化为数组
```
const obj = { a: 1, b: 2 };
const arr = Object.keys(obj); // ['a', 'b']
```
* array.reduce： reduce() 方法接收一个函数作为累加器，数组中的每个值（从左到右）开始缩减，最终为一个值，是ES5中新增的又一个数组逐项处理方法
    * callback（一个在数组中每一项上调用的函数，接受四个函数：）
        1. previousValue（上一次调用回调函数时的返回值，或者初始值） 
        2. currentValue（当前正在处理的数组元素）
        3. currentIndex（当前正在处理的数组元素下标）
        4. array（调用reduce()方法的数组）
    * initialValue（可选的初始值。作为第一次调用回调函数时传给previousValue的值）

### Style Objects
styles-components允许你使用对象来代替字符串编写样式
```
/ Static object
const Box = styled.div({
  background: 'palevioletred',
  height: '50px',
  width: '50px'
});

// Adapting based on props
const PropsBox = styled.div(props => ({
  background: props.background,
  height: '50px',
  width: '50px'
}));

render(
  <div>
    <Box />
    <PropsBox background="blue" />
  </div>
);
```

## API

### styled
使用styled会返回一个函数来创建一个react组件
```
const Button = styled.button`
  background: palevioletred;
  border-radius: 3px;
  border: none;
  color: white;
`

const TomatoButton = styled(Button)`
  background: tomato;
`

render(
  <>
    <Button>I'm purple.</Button>
    <br />
    <TomatoButton>I'm red.</TomatoButton>
  </>
)
```

### attrs
attrs可以接收一个对象将参数传递到组件上
```
const Input = styled.input.attrs({
  type: 'text',
  size: props => (props.small ? 5 : undefined),
})`
  border-radius: 3px;
  border: 1px solid palevioletred;
  display: block;
  margin: 0 0 1em;
  padding: ${props => props.padding};

  ::placeholder {
    color: palevioletred;
  }
`

render(
  <>
    <Input small placeholder="Small" />
    <Input placeholder="Normal" />
    <Input padding="2em" placeholder="Padded" />
  </>
)
```

### withComponent
可以改变组件的标签 比如把 button 变成 a 标签
```
const Link = Button.withComponent('a')
```

### "as" polymorphic prop
可以改变组件的标签 比如把 button 变成 a 标签
```
// import styled from "styled-components";

const Component = styled.div`
  color: red;
`;

render(
  <Component
    as="button"
    onClick={() => alert('It works!')}
  >
    Hello World!
  </Component>
)
```

### ThemeProvider
styled-components暴露了一个<ThemeProvider>容器组件，提供了设置默认主题样式的功能，他类似于react-rudux的顶层组件Provider，通过context实现了从顶层到底层所有样式组件的默认主题共用。
```
const Button = styled.button`
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border-radius: 3px;
  
  /* Color the border and text with theme.main */
  color: ${props => props.theme.main};
  border: 2px solid ${props => props.theme.main};
`;

Button.defaultProps = {
  theme: {
    main: 'palevioletred'
  }
}
// Define what props.theme will look like
const theme = {
  main: 'mediumseagreen'
};

render(
  <div>
    <Button>Normal</Button>
    <ThemeProvider theme={theme}>
      <Button>Themed</Button>
    </ThemeProvider>
  </div>
);
```

### createGlobalStyle
styled-components提供了createGlobalStyle来创建全局css
```
import { createGlobalStyle } from 'styled-components'

const GlobalStyle = createGlobalStyle`
  body {
    color: ${props => (props.whiteColor ? 'white' : 'black')};
  }
`

// later in your app

<React.Fragment>
  <Navigation /> {/* example of other top-level stuff */}
  <GlobalStyle whiteColor />
</React.Fragment>
```
```
import { createGlobalStyle, ThemeProvider } from 'styled-components'

const GlobalStyle = createGlobalStyle`
  body {
    color: ${props => (props.whiteColor ? 'white' : 'black')};
    font-family: ${props => props.theme.fontFamily};
  }
`

// later in your app

<ThemeProvider theme={{ fontFamily: 'Helvetica Neue' }}>
  <React.Fragment>
    <Navigation /> {/* example of other top-level stuff */}
    <GlobalStyle whiteColor />
  </React.Fragment>
</ThemeProvider>
```

### injectGlobal 
通过injectGlobal来创建全局变量，不会返回components
```
injectGlobal`
  @font-face {
    font-family: "Operator Mono";
    src: url("../fonts/Operator-Mono.ttf");
  }

  body {
    margin: 0;
  }
`
```
### css
使用css方法可以返回一个写有样式的字符串
```
import styled, { css } from 'styled-components'

const complexMixin = css`
  color: ${props => (props.whiteColor ? 'white' : 'black')};
`

const StyledComp = styled.div`
  /* This is an example of a nested interpolation */
  ${props => (props.complex ? complexMixin : 'color: blue;')};
`
```

### keyframes
使用keyframes来创建动画
```
import styled, { keyframes } from 'styled-components'

const fadeIn = keyframes`
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
`

const FadeInButton = styled.button`
  animation: 1s ${fadeIn} ease-out;
`
```

### isStyledComponent
验证是否是styled-components 如果是返回true
```
import React from 'react'
import styled, { isStyledComponent } from 'styled-components'
import MaybeStyledComponent from './somewhere-else'

let TargetedComponent = isStyledComponent(MaybeStyledComponent)
  ? MaybeStyledComponent
  : styled(MaybeStyledComponent)``

const ParentComponent = styled.div`
  color: cornflowerblue;

  ${TargetedComponent} {
    color: tomato;
  }
`
```

### withTheme 
返回一个高阶组件提供theme参数
```
import { withTheme } from 'styled-components'

class MyComponent extends React.Component {
  render() {
    console.log('Current theme: ', this.props.theme)
    // ...
  }
}

export default withTheme(MyComponent)
```

### ThemeConsumer
传递theme参数给子函数
```
import { ThemeConsumer } from 'styled-components'

export default class MyComponent extends React.Component {
  render() {
    return (
      <ThemeConsumer>
        {theme => <div>The theme color is {theme.color}.</div>}
      </ThemeConsumer>
    )
  }
}
```

## 单元测试

### find 
从DOM节点的字节点里查找第一个styled component, 返回HTMLDivElement || null
```
import styled, { find } from 'styled-components/test-utils'

const Foo = styled.div`
  color: red;
`

/**
 * Somewhere in your app:
 *
 * ReactDOM.render(
 *   <main>
 *     <Foo />
 *   </main>, document.body
 * );
 */

// retrieves the first instance of "Foo" in the body (querySelector under the hood)
find(document.body, Foo) // HTMLDivElement | null

```

### findAll
从DOM节点的字节点里查找所有styled component, 返回NodeList<HTMLDivElement> || null
```
import styled, { findAll } from 'styled-components/test-utils'

const Foo = styled.div`
  color: ${p => p.color};
`

/**
 * Somewhere in your app:
 *
 * ReactDOM.render(
 *   <main>
 *     <Foo color="red" />
 *     <Foo color="green" />
 *   </main>, document.body
 * );
 */

// retrieves a NodeList of instances of "Foo" in the body (querySelectorAll under the hood)
findAll(document.body, Foo) // NodeList<HTMLDivElement> | null
```

### enzymeFind
A convenience method for finding instances of a particular styled component within an enyzme wrapper.
```
import { mount } from 'enzyme'
import styled, { enzymeFind } from 'styled-components/test-utils'

const Foo = styled.div`
  color: red;
`

const wrapper = mount(
  <div>
    <Foo>bar</Foo>
  </div>
)

enzymeFind(wrapper, Foo)
```
