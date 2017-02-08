#exports

![](/assets/exports-definition.png)

####exports使用

```
foo.js:
exports.a = function(){
    console.log('a')
}
exports.a = 1 
 
test.js
var x = require('./foo');
console.log(x.a); // 1
```

####module.exports与exports的区别

注意：每一个node.js执行文件，都自动创建一个module对象，同时，module对象会创建一个叫exports的属性，初始化的值是 {}

* module.exports 初始值为一个空对象 {}
* exports 是指向的 module.exports 的引用
* require() 返回的是 module.exports 而不是 exports（如果运行时让exports和module.exports指向不同的对象，只有module.exports指向的对象才回被导出）
* module.exports才是真正的接口，exports只不过是它的一个辅助工具