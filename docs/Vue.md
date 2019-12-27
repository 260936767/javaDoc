## vue实例

## 1. 声明式渲染

### 01.插值(数据绑定 v -model)

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
	</head>
	<body>
	<div id="app">
		<input v-model="name" />
		<span style="background-color: red">{{name}}</span>
	</div>
	<script>
		var vm = new Vue({
			el: "#app",
			data: {
				name:"zd"
			}
		});
	</script>
	</body>

</html>
```

![image-20191226170329047](.\images\image-20191226170329047.png)

更改name属性 vm.name="change the value"

![image-20191226170536972](.\images\image-20191226170536972.png)

### 02.绑定元素特性（v-bind）

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
	</head>
	<body>
	<div id="app-2">
		<span style="background-color: red"
		v-bind:title="message">
			鼠标悬停几秒钟查看此处动态绑定的提示信息！
		</span>
	</div>
	<script>
		var vm = new Vue({
			el: "#app-2",
			data: {
				message:"页面加载于"+ new Date().toLocaleString()
			}
		});
	</script>
	</body>
</html>
```

![image-20191226170920156](.\images\image-20191226170920156.png)

改变值：**vm.message=new Date().toLocaleString()**

<img src=".\images\image-20191226171044341.png" alt="image-20191226171044341" style="zoom:100%;" />

## 2.条件与循环

### 01.v-if

```html
<body>
	<div id="app-02">
		<p v-if="seen">现在你看到我了</p>
	</div>
	<script>
		var vm = new Vue({
			el: "#app-02",
			data: {
				seen: true
			}
		});
	</script>
	</body>
```

![image-20191226172658230](.\images\image-20191226172658230.png)

### 02.v-for

```html
<div id="app-03">
		 <ol>
		   <li v-for="todo in todos">
			   {{todo.text}}
		   </li>
		  </ol>
	</div>
	<script>
		var vm = new Vue({
			el: "#app-03",
			data: {
				todos:[
					{id:01,text:"测试1"},
					{id:02,text:"测试2"},
					{id:03,text:"测试3"},
				]
			}
		});
	</script>
	</body>
```

![image-20191226181314067](.\images\image-20191226181314067.png)

### 02.v-on 

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
	</head>
	<body>
	<div id="app-04">
		 <p>{{message}}</p>
		 <button v-on:click="reverseMessage">反转消息</button>
	</div>
	<script>
		var vm = new Vue({
			el: "#app-04",
			data: {
				message: "hello，测试数据.~"
			},
			methods:{
				reverseMessage:function(){
					this.message = this.message.split("").reverse().join("")
				}
			}
		});
	</script>
	</body>
</html>

```

![image-20191226175253334](.\images\image-20191226175253334.png)

![image-20191226175309964](.\images\image-20191226175309964.png)

## 3.组件及应用

01.先注册组件 Vue.component()

02.制作数据

03.组件变量数据显示

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<script src="js/vue.js" type="text/javascript" charset="utf-8"></script>
	</head>
	<body>
	<div id="app-05">
		 <todo-item
		 v-for="item in list"
		 v-bind:todo="item"
		 v-bind:key="item.id"
		 ></todo-item>
	</div>
	<script>
		Vue.component(
		"todo-item",{ //id
			props:["todo"],//接收父作用域将数据传到子组件
			template:"<li>{{todo.text}}</li>"
		}
		)
		var vm = new Vue({
			el: "#app-05",
			data: {
				list:[
					{id:001,text:"test001"},
					{id:002,text:"test002"},
					{id:003,text:"test003"},
				]
			}
		});
	</script>
	</body>
</html>

```

![image-20191226181042564](.\images\image-20191226181042564.png)



