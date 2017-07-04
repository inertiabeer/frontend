# jsonp实践



之前一直只是了解jsonp，没有做过例子，对jsonp只是知道了一个大概，幸运的是看到imooc上的王老师的手记

然后自己实现了一下，才对jsonp有了一个清楚的认识

首先要对跨域有个清晰的认识，这点就不细说了，网上到处是解释，只要记住，不同域名，不同端口，都是跨域。

你的web程序是不允许跨域请求别的网站的数据的，但是有时候又的确有这种需求，需要在自己的两个web程序上进行数据的传送，那么也就有了jsonp（利于script标签可以跨域）。

### 前期准备

既然是跨域，起码要有两台web服务器，我用express应用生成器生成了两个应用，都是ejs模板引擎的，jade2html实在是麻烦。一个叫做**jsonptest（请求跨域数据的一方3001端口）**， **res（发送跨域数据的一方3000端口）**

### 初步跨域

在res的public目录下新建js文件

`alert('跨域请求script成功')`

然后**jsonptest中的index.ejs中添加一个script标签，src指向res下的js文件（端口记得不要写错）**

然后打开jsonptest对应的web端口，你就发现程序运行成功

当然这个是肯定成功了，不然都没办法请求cdn的库文件了

### 函数跨域

既然是跨域请求数据，我们可以在**jsonptest**中写一个对数据进行函数，让另一台服务器上，执行一下这个函数，函数里面放上需要的数据。

先约定函数名为myFn,然后在jsonptest中写入

![https://github.com/inertiabeer/frontend/master/img/hello.png]()

`  function myFn(data){	alert(data+' hello');	}`

在res程序的js文件中中写入

`var data='我的数据'
myFn(data);`

这时候依然可以正常运行

### 正式跨域

**浏览器执行到script的src时，会对src所在的web应用程序发一个get请求，既然是get，那你想到了什么没有，完全可以用后台的web app对这个get请求进行处理，返回一个js语句获取js文件。**



这时候将jsonptest的script的src改为http://localhost:3000/jsonp?callback=myFn

在res的app.js中写入

```
app.get('/jsonp',function(req,res){
	var callback=req.query.callback;
	var data='"我只是一个路人"';
	res.send(callback+'('+data+')')
})
```

这时候就可以正常运行了

由于跨域请求可能运行多次，所以在jsonptest封装一个函数，可以根据src去请求数据

```
function myFn(data){
    		alert(data+' hello');
    	}


    	function addjsonp(src)
    	{
    		var script=document.createElement("script");
    		script.setAttribute('src',src);
    		document.body.appendChild(script);
    	}
    	addjsonp('http://localhost:3000/jsonp?callback=myFn')
```

























