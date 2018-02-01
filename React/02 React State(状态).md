# 02 React State(状态)

Github：[https://github.com/nnngu/LearningNotes](https://github.com/nnngu/LearningNotes)

---

React 把组件看成是一个状态机（State Machines）。通过与用户的交互，实现不同状态，然后渲染 UI，让用户界面和数据保持一致。

React 里，只需更新组件的 state，然后根据新的 state 重新渲染用户界面（不用操作 DOM）。

下面的例子中创建了 LikeButton 组件，getInitialState 方法用于定义初始状态，也就是一个对象，这个对象可以通过 this.state 属性读取。当用户点击组件，导致状态变化，this.setState 方法就修改状态值，每次修改以后，自动调用 this.render 方法，再次渲染组件。

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8" />
    <title>React 学习</title>
    <script src="https://cdn.bootcss.com/react/15.4.2/react.min.js"></script>
    <script src="https://cdn.bootcss.com/react/15.4.2/react-dom.min.js"></script>
    <script src="https://cdn.bootcss.com/babel-standalone/6.22.1/babel.min.js"></script>
</head>

<body>
<div id="example"></div>
<script type="text/babel">
    var LikeButton = React.createClass({
        getInitialState: function() {
            return {liked: false};
        },
        handleClick: function(event) {
            this.setState({liked: !this.state.liked});
        },
        render: function() {
            var text = this.state.liked ? '喜欢' : '不喜欢';
            return (
                <p onClick={this.handleClick}>
                    你<b>{text}</b>我。点我切换状态。
                </p>
            );
        }
    });

    ReactDOM.render(
        <LikeButton />,
        document.getElementById('example')
    );
</script>
</body>

</html>
```

注意：onClick 等事件，on 后面第一个字母是大写的！