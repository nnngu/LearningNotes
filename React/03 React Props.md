# 03 React Props

Github：[https://github.com/nnngu/LearningNotes](https://github.com/nnngu/LearningNotes)

---

state 和 props 主要的区别在于 props 是不可变的，而 state 可以根据与用户交互来改变。这就是为什么有些容器组件需要定义 state 来更新和修改数据。 而子组件只能通过 props 来传递数据。

## 使用 Props

下面的例子演示了如何在组件中使用 props：

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
    var HelloMessage = React.createClass({
        render: function() {
            return <h1>Hello {this.props.name}</h1>;
        }
    });
    
    ReactDOM.render(
        <HelloMessage name="nnngu"/>,
        document.getElementById('example')
    );
</script>
</body>

</html>
```

## 默认 Props

