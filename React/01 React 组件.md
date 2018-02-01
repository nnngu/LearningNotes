# 01 React 组件

Github：[https://github.com/nnngu/LearningNotes](https://github.com/nnngu/LearningNotes)

---

本篇文章我们来总结 React 组件，接下来我们封装一个输出 "Hello World！" 的组件，组件名为 HelloMessage

## HelloMessage组件

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
            return <h1>Hello World！</h1>;
        }
    });

    ReactDOM.render(
        <HelloMessage />,
        document.getElementById('example')
    );
</script>
</body>
</html>
```

### 解析

React.createClass 方法用于生成一个组件类 HelloMessage

<HelloMessage /> 实例化组件类并输出信息。

**注意，原生 HTML 元素名以小写字母开头，而自定义的 React 类名以大写字母开头，比如 HelloMessage 不能写成 helloMessage。除此之外还需要注意组件类只能包含一个顶层标签，否则也会报错。**

## 向组件传递参数

如果我们需要向组件传递参数，可以使用 this.props 对象，实例如下：

```html
<body>
<div id="example"></div>
<script type="text/babel">
    var HelloMessage = React.createClass({
        render: function() {
            return <h1>Hello {this.props.name}</h1>;
        }
    });

    ReactDOM.render(
        <HelloMessage name="React！！" />,
        document.getElementById('example')
    );
</script>
</body>
```

## 复合组件

我们可以通过创建多个组件来合成一个组件，即把组件的不同功能进行分离。

以下例子实现了输出网站名字和网址的组件

```html
<body>
<div id="example"></div>
<script type="text/babel">
    var WebSite = React.createClass({
        render: function() {
            return (
                <div>
                    <Name name={this.props.name} />
                    <Link site={this.props.site} />
                </div>
            );
        }
    });

    var Name = React.createClass({
        render: function() {
            return (
                <h1>{this.props.name}</h1>
            );
        }
    });

    var Link = React.createClass({
        render: function() {
            return (
                <a href={this.props.site}>
                    {this.props.site}
                </a>
            );
        }
    });

    ReactDOM.render(
        <WebSite name="LearningNotes" site="https://github.com/nnngu/LearningNotes" />,
        document.getElementById('example')
    );
</script>
</body>
```

上面的例子中，WebSite 组件使用了 Name 和 Link 组件来输出对应的信息，也就是说 WebSite 拥有 Name 和 Link 的实例。
