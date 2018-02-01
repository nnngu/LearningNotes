# 04 React 组件 API

Github：[https://github.com/nnngu/LearningNotes](https://github.com/nnngu/LearningNotes)

---

在这篇文章中我们将讨论 React 组件 API。我们将讲解以下7个方法：

* 设置状态：setState
* 替换状态：replaceState
* 设置属性：setProps
* 替换属性：replaceProps
* 强制更新：forceUpdate
* 获取DOM节点：findDOMNode
* 判断组件挂载状态：isMounted

## 设置状态：setState

`setState(object nextState[, function callback])`

### 参数说明

* nextState，将要设置的新状态，该状态会和当前的state合并
* callback，可选参数，回调函数。该函数会在setState设置成功，且组件重新渲染后调用。

合并nextState和当前state，并重新渲染组件。setState是React事件处理函数中和请求回调函数中触发UI更新的主要方法。

### 关于setState

不能在组件内部通过this.state修改状态，因为该状态会在调用setState()后被替换。

setState()并不会立即改变this.state，而是创建一个即将处理的state。setState()并不一定是同步的，为了提升性能React会批量执行state和DOM渲染。

setState()总是会触发一次组件重绘，除非在shouldComponentUpdate()中实现了一些条件渲染逻辑。

```html
<body>
<div id="message" align="center"></div>

<script type="text/babel">
    var Counter = React.createClass({
        getInitialState: function () {
            return { clickCount: 0 };
        },
        handleClick: function () {
            this.setState(function(state) {
                return {clickCount: state.clickCount + 1};
            });
        },
        render: function () {
            return (<h2 onClick={this.handleClick}>点我！点击次数为: {this.state.clickCount}</h2>);
        }
    });
    ReactDOM.render(
        <Counter />,
        document.getElementById('message')
    );
</script>
</body>
```

通过点击 h2 标签来使得点击计数器加 1。

## 替换状态：replaceState

`replaceState(object nextState[, function callback])`



## 设置属性：setProps


## 替换属性：replaceProps


## 强制更新：forceUpdate


## 获取DOM节点：findDOMNode


## 判断组件挂载状态：isMounted



