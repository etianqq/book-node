## 异步编程

异步编程的三种解决方案：

* 事件发布/订阅模式
* Promise/Deferred模式
* 流程控制库

#### 事件发布/订阅模式

```javascript
//事件监听
res.on('data', chunk=>console.log('body:'+'chunk'));
res.on('end', ()=>console.log('end'));
res.on('error', (error)=>console.log(error));
```

#### Promise/Deferred模式

![](https://cn.bing.com/th?id=OIP.v_4CFTuDkfvtzO_PA0ArfQHaCT&pid=Api&rs=1)

* Deferred主要用于内部，维护异步模型的状态
* Promise用于外部，通过`then`方法暴露给外部以添加自定义逻辑。

把上面的代码改装为 Promise/Deferred模式。

```javascript
var promisify = function(res) {
  var deferred = new Deferred();
  var result = '';
  res.on('data', chunk=>{
    result += chunk;
    deferred.progress(chunk);
  });
  res.on('end', ()=>deferred.resolve(result));
  res.on('error', (error)=>deferred.reject(error));
}

promisify(res).then((data)=>{}, (error)=>{}, (chunk)=>{})
```

#### 流程控制库

