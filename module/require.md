#模块载入

####1.模块分类

* 原生（核心）模块
* 文件模块
    * .js。通过fs模块同步读取js文件并编译执行。
    * .node。通过C/C++进行编写的Addon。通过dlopen方法进行加载。
    * .json。读取文件，调用JSON.parse解析加载。
    
原生模块在Node.js源代码编译的时候编译进了二进制执行文件，加载的速度最快。
文件模块是动态加载的，加载速度比原生模块慢。

但是Node.js对原生模块和文件模块都进行了缓存，于是在第二次require时，是不会有重复开销的。    

####2. require方法中的文件查找策略

![](/assets/require module.jpg)