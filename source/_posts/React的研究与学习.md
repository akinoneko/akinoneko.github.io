---
title: React的研究与学习
date: 2016-08-25 16:41:36
categories:
  - 技术学习
tags:
  - React.js
  - 前端
  - 虚拟DOM
  - 组件化
---
## React简介
### 前言
　　最近开始学习React，做点笔记给自己留下点印象。React使用组件化的思想进行设计，可以通过多个小组件的组合来完成一个独立模块的功能。使用虚拟DOM的概念，React在内存数据中操作组件的内容，并通过React diff算法更新改变了的DOM节点，由于React使用的虚拟DOM是内存数据，所以具有较高的性能。
### 组件化
　　![](http://i.imgur.com/MtVxuIA.png)
　　1. React的组件有如下几点特性：
　　2. 可组合性：组件之间可以嵌套使用。
　　2. 可重用性：每个组件可具有独立的功能，可以在多个UI场景中进行使用。
　　3. 可维护性：每个组件仅含有自身的逻辑，可以方便得进行维护。
### JSX语法
　　JSX允许HTML语言可以脱离引号直接出现在Javascript语言中。具体的代码如下：
``` html
<!DOCTYPE html>
<html>
  <head>
  	<meta charset="utf-8" />
	<!-- 引入需要的库文件3个 -->
	<script type="text/javascript" src="js/react.js" ></script>
	<script type="text/javascript" src="js/react-dom.js" ></script>
	<script type="text/javascript" src="js/browser.min.js" ></script>
	<title>React Demo</title>
  </head>
  <body>
	<div id="demo"></div>
	<script type="text/babel">
		var Helloworld = React.createClass({
			render:function(){
				return(
					<div>
						<h1>Hello World!</h1>
					</div>
				);
			}
		});		
		React.render(
			<Helloworld />,
			document.getElementById('demo')
		);
	</script>	
  </body>
</html>
```

## React.js组件学习
### 组件属性
　　React允许我们对普通的HTML代码进行包装，行程一个新的组件，组件可以具有一定的属性，在网页上可以和插入HTML标签一样在页面中插入组件。在React中通过React.creatClass方法来创建一个新的组件。以下是一个运用组件属性的例子：
``` javascript
var Comment = React.createClass({
	render: function(){
		return (
			<div>
				<h2>
					{this.props.author}
				</h2>
				{this.props.children}
			</div>
		);
	}
});
ReactDOM.render(
  <Comment author='akinoneko'>这是一个Comment</Comment>,
  document.getElementById('demo')
);
```
　　通过props，可以获取组件的属性值，使用方式是通过**this.props.属性名**来取得对应属性的值。
　　在创建组件时需要注意以下几点：
　　1. 组件的名称必须**首字母大写**；
　　2. 组件中的元素需要添加CSS时，**使用clssName不能使用class**，因为class是js中的关键字；
　　3. 组件中的元素不能直接使用style设置inline CSS的属性，	需要使用**style={ {属性名:属性值} }**这种方式；
### 组件状态
　　React中的组件都有自己的状态，当与用户进行交互的时候，组件的状态发生了改变，React会捕捉到组件的变化，并且与DOM进行对比，使用修改最少UI资源的方式更新UI界面。以下是一个示例的代码：
``` javascript
var Text = React.createClass({
	getInitialState:function(){
		return {textvalue:""};
	},
	handleChange:function(e){
		this.setState({textvalue:e.target.value});
	},
	render: function(){
		return (
			<div>
				<h2>文本输入</h2>
				<input type="text" onChange={this.handleChange} />
				<h3>{this.state.textvalue}</h3>
			</div>
		);
	}
});
ReactDOM.render(
  <Text author='Yoo酱'/>,
  document.getElementById('demo')
);
```
　　在上述的代码中，我们使用了一个getInitialState方法，该方法在组件初始化的时候会被执行，且必须返回一个NULL或者一个对象。另外我们通过将handleChange事件与输入框绑定来调用setState方法修改this.state.textvalue的值，每次改完后，会自动调用this.render方法，重新渲染组件。
### 组件生命周期
　　组件的生命周期分为三个阶段Mounting、Updating和Unmounting，React 为每个状态都提供了两种处理函数，will 函数在进入状态之前调用，did 函数在进入状态之后调用，三种状态共计五种处理函数。
* componentWillMount()
* componentDidMount()
* componentWillUpdate(object nextProps, object nextState)
* componentDidUpdate(object prevProps, object prevState)
* componentWillUnmount()

### 组件嵌套
　　React的组件可以进行复用，多个组件可以进行嵌套行程一个独立的模块。
![TODO-APP](http://i.imgur.com/aGHClm2.jpg)
　　这是一个利用组件嵌套制作的TODO-APP，代码如下：
``` javascript
//List列表
var TodoList = React.createClass({
	handleClick: function(){
		console.log("son click");
	},
	render: function(){
		var createItem = function(nextItem){
				return (
					<li>{nextItem}</li>
				)
			}
		//遍历输出列表数组中的值
		return (
			<ul>{this.props.items.map(createItem)}</ul>
		);
	}
});
//Todo组件
var TodoApp = React.createClass({
	getInitialState: function(){
		return {items: [],text: ''};
	},
	handleSubmit:function(event){
		//阻止表单提交
		event.preventDefault();
		var nextText = this.state.text;
		//防止空数据提交
		if(nextText==''){
			return ;
		}
		//添加内容到列表数组中
		var nextItems = this.state.items.concat([nextText]);
		nextText = '';
		this.setState({items:nextItems,text:nextText});
	},
	handleOnChange:function(event){
		this.setState({text: event.target.value});
	},
	render: function(){
		return (
			<div>
				<h2 style={{color:'red'}}>待办事项</h2>
				<TodoList items={this.state.items} />
				<form onSubmit={this.handleSubmit}>
					<input type="text" value={this.state.text} onChange={this.handleOnChange} />
					<button>{'添加事项' + (this.state.items.length+1)}</button>
				</form>
			</div>
		);
	}
});
ReactDOM.render(
  <TodoApp />,
  document.getElementById('demo')
);
```

