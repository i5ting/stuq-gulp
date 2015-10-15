# StuQ-Gulp实战和原理解析

大家好，我是桑世龙，github和cnodejs上的i5ting，目前在天津创业，公司目前使用技术主要是nodejs，算所谓的MEAN（mongodb  + express + angular + node），所有代码都部署在阿里云上

我的名字i5ting，原因是我媳妇叫张婷，哈哈

闲言少叙，下面开始正题 《Gulp实战和原理解析》

本次主要讲3部分

- 1)什么gulp和核心概念、用法
- 2)以微信前端组的weui为例子，讲解gulp在真实项目里如何使用（包括浏览器实时刷新，watch，less编译，压缩，生成sourcemap、release等）
- 3)最后讲解一下stream原理，给出更多实践

## 什么是gulp？

简单的讲，gulp是一个构建工具，一个streaming构建工具，一个nodejs写的构建工具

总之，它是一个构建工具

那么什么是构建工具呢？

构建工具本质就是为了自动化构建，解放程序员、提供程序员效率的工具

我们来举个例子，最早的make，因为每次都cc编译，太恶心了，而且当文件特别多的时候，编译速度又慢下来，能不能按需编译，增量编译？

make是通过makefile文件来描述源程序之间的相互关系并自动维护编译工作的

例子就不举了，写过c的人多少都知道点

其实编译在每个语言世界里，都是痛，骨子里的风湿一样，于是产生了make类似的东西

- 比如java里的ant，c#里的NAnt
- 比如ruby里rake
- 比如coffeescript里有cake

它们的共同特点

- 1)基于task，定义简单
- 2)task有串行，并行，作业依赖等控制方式
- 3)通过xxxfile来定义task

如此看来，nodejs的构建系统也应该是这样的，可以说gulp是node世界里和上面几个构建工具最像的一个，它们太像了，以至于学习起来特别简单

其实上面还提了一个streaming，是流式的意思，后面讲原理的时候会深入讲解


## 入门指南

说是入门指南，还是得从npm开始科普

### 1. 安装 gulp：

建议全局安装

```sh
$ npm install --global gulp
```

但最多的是作为项目的开发依赖（devDependencies）安装：

```sh
$ npm install --save-dev gulp
```

### 2. 创建gulpfile

在项目根目录下创建一个名为 `gulpfile.js` 的文件：

```js
var gulp = require('gulp');

gulp.task('default', function() {
  // 将你的默认的任务代码放在这
});
```

gulpfile和makefile、build.xml等是类似的，定义task的地方。

定义好task，下面运行一下

### 3. 运行 gulp：

```sh
$ gulp
```

默认的名为 default 的任务（task）将会被运行，在这里，这个任务并未做任何事情。

想要单独执行特定的任务（task），请输入 `gulp <task> <othertask>`。
  
是不是很简单？

下面比较一下grunt和gulp

## gulp vs grunt

Gulp 和 Grunt 类似。但相比于 Grunt 的频繁的 IO 操作，Gulp 的流操作，能更快地完成构建。

grunt是基于dsl声明式写法，出来的早，但写起来比较恶心，可读性非常差。。。。

nodejs里sails的因为基于grunt被鄙视，ionic里也是，好在ionic现在也是gulp的

鄙视完grunt，就该吹吹gulp了

话说2014年gulp火了之后，很快风头就盖过了grunt，好不好，试了才知道

## why gulp？


前端本身就对js非常了解，gulp可以说是plain 
js，语法都再简单不过，所以大家比较容易接受


### 1. 基于nodejs

基于nodejs的小而美哲学，写gulp task是件很愉快的事儿

而且还可以结合nodejs世界里最好的包管理器npm，写cli或者模块特别简单

而且可以写c/c++ addon，给你无限可能

而且还有shelljs这样的库，结合shell，给你无限可能

### 2. 易于使用

通过代码优于配置的策略，Gulp 让简单的任务简单，复杂的任务可管理。

而且对ant，rake，make等了解的人非常容易上手

### 3. 构建快速

利用 Node.js stream的威力，你可以快速构建项目并减少频繁的 IO 操作。

而且gulp的核心代码可以高效利用你所有核的cpu，高效运行，尤其是大文件处理，后面会讲到

###  4. 插件机制

Gulp 严格的插件指南确保插件如你期望的那样简洁高质得工作。

### 5. 易于学习

通过最少的 API，掌握 Gulp 毫不费力

这得益于gulp的设计，api简单，调用关系和依赖清晰，当然如果理解linux 管道或者其他类似的构建工具会容易

而且每个任务都拆分成一个task，结构清晰，利用stream和pipe写法，组成task，也是非常容易学习的。

### 总结

这里说了gulp的各种好处，它通用，高效，使用简单，学习成本低，总之，我就是要告诉你：它是非常好的构建工具，而且是长久看好的。

它也有缺点，这样说也不太合适，更准确的说，它是一个通用构建工具，所以在构建实践上，需要写的人有好的实践

像fib3这样的，只是基于比较好的构建实践而已，其他方面是不能和gulp相提并论的。

## gulp快速入门

都说得来个hello world

### hello world

创建gulpfile.js

```
var gulp = require('gulp');

gulp.task('default', function() {
    console.log('hello world')
});

```

然后只需执行命令

```
gulp
```

定义作业就是这么加单

- default是名字
- 后面的匿名函数是它的实现

### 自定义作业

custom task with name stuq

```
var gulp = require('gulp');

gulp.task('stuq', function() {
    console.log('hello stuQ!')
});

```

then run

```
gulp stuq
```

这里的stuq是作业名称

自己定义一个task就是这么简单

### 依赖作业

```
gulp.task('default',['watch'],function() {
    console.log('default task');
});
```

这里的task有3个参数

default是方法名称，只有default比较奇怪，会默认调用。

相当于c里的main方法

['watch']这是依赖的作业列表，它们是由顺序的，按数组顺序依次执行
第三个参数是成功执行完上面的依赖作业后执行的回调函数

这里要强调，依赖作业数组里的都执行完了，才会执行第三个参数，即当前作业具体内容


我们不妨改一下，看看多个依赖如何定义

```
gulp.task('default',['watch','task_2','task_3'],function() {
    console.log('依赖作业终于执行完了，下面是我的舞台....');
});
```

是不是很简单？放心，这只是入门，下面看一下流式处理

### 流式处理

比如混淆压缩js，使用gulp-uglify插件

```
var gulp = require('gulp');
var uglify = require('gulp-uglify');

gulp.task('default', function() {
    gulp.src('src/*.js')
        .pipe(uglify())   //压缩js
        .pipe(gulp.dest('dist'))
});
```

- src是输入
- dest是输出


pipe是管道的意思，也是stream里核心概念，也就是说：上一个的输出，是下一个的输入。

src里所有js，经过处理1，处理2，然后压缩变成min.js,中间的处理pipe可以1步，也可以是n步

反正第一步处理的结果是第二步的输入，以此类推，就像生产线一样，每一步都是一个task是不是很好理解呢？

每个独立操作单元都是一个task，使用pipe来组装tasks

于是gulp就变成了基于task的积木式的工具

好吧，是时候总结一下了

### 总结

- 1）每一个作业都是独立，定义为task
- 2）通过stream的机制，上一个的输出，作为下一个的输入
- 3）通过pipe方法组装task，完成我们想要的业务逻辑


至此，task相关的概念都讲完了，你已经会使用gulp了，找几个插件就足以工作了

如果想高级玩法，可以自己写插件，可以尝试gulp 4的一些api，比如并行等。。。


大家有任何不明白或者想提问的可以随时回复到StuQ微信公众号后台，我会在答疑阶段统一回答


下面看以项目实战，以目前最火的微信前端组的weui项目为例，看看gulp是如何被使用的（其实如何使用gulp构建前端项目）

## 项目实战

先说一下weui是一个什么项目：

[WeUI](https://github.com/weui/weui) 为微信 Web 服务量身设计的h5框架

WeUI是一套同微信原生视觉体验一致的基础样式库，为微信 Web 开发量身设计，可以令用户的使用感知更加统一。包含button、cell、dialog、toast、article、icon等各式元素。

它是使用less编写，最终编译成css，压缩成weui.min.css的，当然这里面还使用了一下比较好的开发实践

- web server
- livereload
- watch实时监控
- less：css预处理器
- minify 压缩
- sourcemap 生成

下面我们来扒光它

### 准备工作

首先看目录结构

![](img/1.png)

- 有package.json，说明它是一个node模块或者说它依赖了node模块
- 有gulpfile.js，说明它是使用gulp作为构建工具的。

还有src和dist目录，一般src是源码，dist是构建后生成的目录，至此，我们似乎明白了点什么

首先clone代码

```
git clone https://github.com/weui/weui.git
```

上面说了，有package.json，此时需要安装依赖模块

```
npm install
```

至此就准备完了，下面看一下gulpfile.js

### 看一下它有哪些tasks

看一下它有哪些tasks，查看命令是`gulp -T`

```
➜  weui git:(master) ✗ gulp -T
[14:04:18] Using gulpfile ~/workspace/github/weui/gulpfile.js
[14:04:18] Tasks for ~/workspace/github/weui/gulpfile.js
[14:04:18] ├── source
[14:04:18] ├─┬ styles
[14:04:18] │ └── source
[14:04:18] ├─┬ release
[14:04:18] │ └── styles
[14:04:18] ├── watch
[14:04:18] ├── server
[14:04:18] └── default
```

看这个的目的，其实就为了了解当前gulpfile里tasks，以便让大家有一个概况了解

大概有7个task，其中styles和release是有依赖作业的。

也就是说，整个项目目前的task比较少，比较适合讲解，而且是腾讯公司的项目，大家应该会比较认可一些

ok，下面我们分别看一下每个task

上面讲过，所有的task定义在gulpfile.js里，那么我们就结合源码，看一下gulpfile.js的task是如何定义，以及如何应用的

### default

```
// 参数说明
//  -w: 实时监听
//  -s: 启动服务器
//  -p: 服务器启动端口，默认8080
gulp.task('default', function () {
    if (yargs.s) {
        gulp.start('server');
    }
    if (yargs.w) {
        gulp.start('release');
        gulp.start('watch');
    } else {
        gulp.start('release');
    }
});
```

这是默认作业，也就是在根目录，执行

```
gulp
```

相当于

```
gulp default
```

其实make，rake，ant等都有类似约定。

这里面值得说明的是

- 1) 这是最简单的task定义，无任何依赖作业
- 2) 作业里面使用nodejs写的yargs模块，用户处理cli参数

比如此时可以执行`gulp -s`后者`gulp default -s`

```
➜  weui git:(master) ✗ gulp -s
[14:14:09] Using gulpfile ~/workspace/github/weui/gulpfile.js
[14:14:09] Starting 'default'...
[14:14:09] Starting 'server'...
[14:14:09] Finished 'server' after 44 ms
[14:14:09] Starting 'source'...
[14:14:09] Finished 'source' after 9.24 ms
[14:14:09] Starting 'styles'...
[14:14:09] Finished 'styles' after 14 ms
[14:14:09] Starting 'release'...
[14:14:09] Finished 'release' after 4.62 μs
[14:14:09] Finished 'default' after 71 ms
[BS] 1 file changed (example.css)
[BS] 1 file changed (weui.min.css)
```

然后它就会打开网页，跳转到http://localhost:8080/example/

从task定义里可知

```
    if (yargs.s) {
        gulp.start('server');
    }
```

说明server是一个task，这里的start就相当于call或者invoke某个task的方法。

注：yargs是一个nodejs模块，目前最好的解析cli参数的库

当然，如果这样，是不是太简单了呢？而且亲，你还有2个参数没说呢

是的

- -w: 实时监听
- -p: 服务器启动端口，默认8080

这里要说的就是一个开发惯例，

`-p`很好理解，就是httpserver的短口port，如果指定是7070那就是7070，如果没指定就是8080
给程序员自己定制的空间，谁还没有个端口自定义权利呢？

`-w`比较特殊，这里的w是watch的意思，就是监控某个文件或目录，只要有变化就触发xx动作，一般用于编译，比如coffee，typescript，less，sass等

看一下定义

```
    if (yargs.w) {
        gulp.start('release');
        gulp.start('watch');
```

这里的意思的如果有w参数，就先调release task，然后watch作业。

这里牵连出3个task，有server，watch，release，容我慢慢道来

btw：这里的`if (yargs.w) {`怎么看逻辑都怪怪的，既然有无w都执行release task，这样写法还是有待商榷的。

### server

server task一看就知道是启动服务器，一般前端开发，都是起一个服务器在浏览器里测试

所以还是比较容易理解

看代码

```
gulp.task('server', function () {
    browserSync.init({
        server: {
            baseDir: "./dist"
        },
        ui: {
            port: 8081,
            weinre: {
                port: 9090
            }
        },
        port: 8080,
        startPath: '/example'
    });
});
```

代码里的几个关键词

- browserSync
- server
- port
- startPath
- weinre

[browserSync](http://www.browsersync.io/docs/gulp/)是一个nodejs模块，专门做的是livereload的事儿，也就是说，我们在编辑器里写代码，保存了，文件就会变动，文件变动了就会触发操作系统的监控事件，这时让浏览器刷新

于是，代码变了，不用刷新浏览器就能看到效果。。。

这其实就是传说中得livereload...

又可以偷懒了，祭奠f5吧！！！

其他（server，port，startPath）是browserSync的配置项，有兴趣自己扒文件吧

这里稍稍提一下weinre，因为weui这个项目是移动端h5页面，所以才会用到weinre调试的，是远程调试h5的利器

http://people.apache.org/~pmuellr/weinre-docs/latest/


总结一下

整个server就是browserSync提供的3个功能

- 1）起了一个server
- 2）支持livereload
- 3) 自动打开网页

还不错吧，下面看一下更实用的一个task： watch监控

### watch

```
gulp.task('watch', function () {
    gulp.watch('src/**/*.less', ['styles']);
    gulp.watch('src/example/**/*.{html,js}', ['source'], function () {
        browserSync.reload();
    });
});
```

watch其实就干了2件事儿

- 1）如果'src/**/*.less'变动，执行styles task
- 2）如果'src/example/**/*.{html,js}'变动，先执行'source' task，然后livereload通知浏览器

大家伙只要了解文件变动能干坏事即可，其他可自由发挥

如果gulp内置的watch无法满足，你还可以使用gulp-watch这个单独模块，哈哈，如果有兴趣还可以研究一下操作系统底层监控文件变动接口，有点意思

### release

release是发布最终css的task

```
gulp.task('release', ['styles']);
```

release只是依赖styles task，相当于styles的别名。

值得说明的是，weui是less写的，需要编译成css，然后最终发布的是css文件

那么，

- 如果js是用coffeescript，typescript写的呢？
- 如果css是用less，sass，stylus写的呢？

其实都是一样的思路，编译成js或css，然后发布

这些预处理器，让开发方便，高效的同时，也增加了前端的复杂度，真是老子那句话

福兮祸所伏，祸兮福所倚...

阿门。。。
阿弥托佛。。。

### source

下面一个source task

上面的都比较简单，只是作业定义和作业依赖定义而已，下面看一下真实的流式处理

```
gulp.task('source', function(){
    gulp.src('src/example/**/*.!(less)', option)
        .pipe(gulp.dest(dist))
        .pipe(browserSync.reload({stream: true}));
});
```

回故一下，上面讲的流式内容

- src是输入
- dest是输出
- pipe是管道的意思，也是stream里核心概念，也就是说上一个的输出，是下一个的输入。


这代码里的src里的，所有不是less的文件，都丢到dist目录

```
    gulp.src('src/example/**/*.!(less)', option)
        .pipe(gulp.dest(dist))
```

然后，它又pipe一个，仅仅是为了表示顺序，无上下文传递关系（偷懒做法而已，不可取）

这样写起来是不是非常简单？

我知道你会回答是，下面我们来

讲个不简单的

### styles

下面是关于样式处理的task

```
gulp.task('styles', ['source'], function () {
    gulp.src('src/example/example.less', option)
        .pipe(less().on('error', function (e){
            console.error(e.message);
            this.emit('end');
        }))
        .pipe(gulp.dest(dist))
        .pipe(browserSync.reload({stream: true}));

    gulp.src('src/style/weui.less', option)
        .pipe(sourcemaps.init())
        .pipe(less().on('error', function (e) {
            console.error(e.message);
            this.emit('end');
        }))
        .pipe(sourcemaps.write())
        .pipe(autoprefixer())
        .pipe(gulp.dest(dist))
        .pipe(minify())
        .pipe(rename(function (path) {
            path.basename += '.min';
        }))
        .pipe(gulp.dest(dist))
        .pipe(browserSync.reload({stream: true}));
});
```

这是整个gulpfile里最长的一个task


下面拆成2部分分析一下

- 依赖source，执行完source，然后编译less
- 编译的less有例子和具体要发布的weui.css

part1

```
    gulp.src('src/example/example.less', option)
        .pipe(less().on('error', function (e){
            console.error(e.message);
            this.emit('end');
        }))
        .pipe(gulp.dest(dist))
        .pipe(browserSync.reload({stream: true}));
```

- less()来编译less文件，src和dest大家要看清楚
- 最后pipe了一个livereload触发

和上面的source task类似，只有less编译不一样，这里就不详细讲解了

下面看一下part2

```
    gulp.src('src/style/weui.less', option)
        .pipe(sourcemaps.init())
        .pipe(less().on('error', function (e) {
            console.error(e.message);
            this.emit('end');
        }))
        .pipe(sourcemaps.write())
        .pipe(autoprefixer())
        .pipe(gulp.dest(dist))
        .pipe(minify())
        .pipe(rename(function (path) {
            path.basename += '.min';
        }))
        .pipe(gulp.dest(dist))
        .pipe(browserSync.reload({stream: true}));
```
 
- src是src/style/weui.less
- sourcemaps.init()是初始化sourcemap
- less编译
-  .pipe(sourcemaps.write())是写入sourcemap
- .pipe(autoprefixer())自动增加前缀
- .pipe(gulp.dest(dist)) 输出到dist目录
- .pipe(minify()) 是压缩
- .pipe(rename(function (path) 重命名，因为文件后面要加min
- .pipe(gulp.dest(dist)) 压缩后的文件进行保存
- .pipe(browserSync.reload({stream: true}));是livereload触发

整体是分了3个阶段

- 1）编译less和生成sourcemap
- 2）压缩minify
- 3）触发livereload

### 实战总结

至此，我们就讲完了所有gulpfile里的内容，以及每个task的细节

结论是：这是一个比较典型的gulp项目，还不错

当然它也不是非常完美，比如作业依赖可以优化、代码校验检测、release没有reversion处理等

下面简单看一下package.json

### package.json

这里有2个地方需要注意

```
  "devDependencies": {
    "browser-sync": "^2.9.11",
    "gulp": "^3.8.10",
    "gulp-autoprefixer": "^2.3.1",
    "gulp-less": "^3.0.2",
    "gulp-minify-css": "^0.4.4",
    "gulp-rename": "^1.2.2",
    "gulp-replace": "^0.5.2",
    "gulp-sourcemaps": "^1.6.0",
    "yargs": "^1.3.3"
  }
```

是gulpfile里引用的模块

```
  "scripts": {
    "test": "gulp release"
  },
```

执行npm test即可发布最终css。

有人说gulp只是一些命令，没啥技术含量，大家先笑笑，一会我们就来讲讲

下面看一下gulp的核心原理：nodejs stream

## 核心：stream

http://nodejs.org/api/stream.html

官方文档解释

A stream is an abstract interface implemented by various objects in Node.js. For example a request to an HTTP server is a stream, as is stdout. Streams are readable, writable, or both. All streams are instances of EventEmitter


Stream是nodejs各种对象实现的抽象接口。

比如一个http server的请求是一个stream，stdout也是一个。

Streams可读、可写，或者兼有的。

所有的stream对象都是EventEmitter的实例


好理解么？

不好理解，还是从流式理解吧~

### 什么是流式

```
上一个的输出，是下一个的输入

上一个的输出，是下一个的输入

上一个的输出，是下一个的输入
```
### linux pipe

流式和linux pipe是一样的（也可能是最早的起源）

举例

```
ps -ef|grep boot|awk '{print $2}'|xargs kill -9
```

- ps -ef查看进程
- grep boot是过滤进程里的和boot相关的所有进程
- awk '{print $2}' 取出进程号
- xargs kill -9 杀掉该进程

可以看出，上一个的输出，是下一个的输入

下面理解一下nodejs里的stream

### nodejs里的stream

```
var source = fs.readFileSync('/path/to/source', {encoding: 'utf8'});
fs.writeFileSync('/path/to/dest', source);
```

等于

```
// pipe自动调用了data,end等事件
fs.createReadStream('/path/to/source').pipe(fs.createWriteStream('/path/to/dest'));
```

知道gulp里的pipe是怎么来的了吧？

Stream在nodejs中是EventEmitter的实现，并且有多种实现形式，例如：

- http responses request
- fs read write streams
- zlib streams
- tcp sockets
- child process stdout and stderr

连著名request模块都支持stream


为什么使用Stream呢？

- node中的I/O是异步的
- pipe
- 基于buffer节省内存，适合处理大文件
- 有事件监控

### 推荐阅读

nodejs里有五种基本的Stream：readable，writable，transform，duplex，and "classic” 

时间原因，具体使用请自己查阅api文档

https://github.com/substack/stream-handbook

### 八卦更多

ruby作者，松本行弘，上半年基本没干啥，写了一个叫streem的语言，

In Streem, a simple cat program looks like this:

```
stdin | stdout
```

prototype of stream based programming language

可见stream的上一个输出是下一个输入的应用有多么的深入人心

https://github.com/matz/streem

### orchestrator

下面介绍gulp的核心

orchestrator这是gulp底层依赖的task相关的核心库，它定义了task执行方式和依赖，而且支持最大可能的并发

gulp的高效即来源于此

本身stream对大文件读写就非常棒，再加上上面说的种种特性，使得gulp流行是必然的。

https://github.com/orchestrator/orchestrator

## 更多实践

gulp有非常多插件，它可以做的更多

### i5ting_toc

你好奇本文是如何生成toc和发布到git pages上的么？

这竟然也和gulp有关系？

是的，本文是使用markdown写的，然后使用gulp把markdown生成html，并push到git pages上去


实现思路

- jquery写好toc插件，https://github.com/i5ting/i5ting_ztree_toc
- 写生成器，把markdown按照toc模板生成html，https://github.com/i5ting/tocmd.npm
- 写gulp来构建，使用gulp-gh-pages和shelljs来自动生成

更多参见

如何使用gulp来把markdown生成html文档并上传到git pages上

https://cnodejs.org/topic/5464c7fe88b869cc33a97985


### shipit

Shipit nodejs写的自动化部署工具，是基于gulp的，很不错

群里fundon大神的项目都是使用shipit部署的。

https://github.com/shipitjs/shipit


### slush

我比较讨厌Yeoman（YO），好在有一个替代品slush

官方介绍

The streaming scaffolding system - Gulp as a replacement for Yeoman

https://github.com/slushjs/slush

还是不错的，可惜没有红起来，可能时日尚短吧

## 全文总结

gulp是一个构建工具，一个streaming构建工具，一个nodejs写的构建工具

- 1) 每个操作都是是独立作业task
- 2) 作业依赖定义非常简单，决定执行顺序
- 3) 通过pipe组装tasks，完成业务逻辑处理

gulp的核心stream

先理解什么是流式（重要的事情，大声喊三遍）

```
上一个的输出，是下一个的输入

上一个的输出，是下一个的输入

上一个的输出，是下一个的输入
```

好了，你已经是gulp高手了

最后让我们来展望一下美好的未来：

```
gulp是前端世界的主流构建工具，是大多数开源项目的选择，你值得拥有
```

另外公布一下weui未来会有reactjs版本，还是值得期待的。

谢谢大家，今天讲的内容就到这里，如果有什么讲的不对的、不合理的，请不吝赐教，共同学习

大家有任何不明白或者想提问的可以随时回复到StuQ微信公众号后台，我会在答疑阶段统一回答


## FAQ

### 1.

请问将gulp部署到生产环境上，开启watch去时时监听每周升级一次，通过rsync过去的文件去自动检测执行。还是每个开发人员都装gulp执行后上传处理过的文件，如果都不是怎么处理？

“开启watch去时时监听”，为啥不用later这样模块去定时处理呢？

另外每个开发都可以随便更新，显然是不靠谱的


### 2.

请问gulp的任务都是同步顺序执行的嘛？流式是不是就代表无法异步？为什么老师还提到可以异步呢？

这个讲的原因是nodejs里的每个函数都是异步，所以pipe的好处相当于顺序执行了。

gulp里的pipe链上的task都是同步顺序执行的。但pipe链外的不好说


### 3.

一个gulp项目有很多的插件所以nodemodule就有很多…然后ide打开跟占用资源 项目都用的是统一的gulp插件 如何在不同的项目中调用本地的 不需要每个项目下面都安装

1)区分产品模式和开发模式，开发模式依赖的占资源是没办法的

2）如何在不懂项目调试本地的，可以采用软连接的方式，sails里就是这样用的，所有模块都是sails的nodemodules里，然后连接到当前项目里



### 4.

src里所有js，经过处理1，处理2，然后压缩变成min.js,中间的处理pipe可以1步，也可以是n步 请问关于上面的“然后压缩变成min.js”，必须是min,js么？

这里使用了rename插件，可以随意改的，哈哈


### 5. 

gulp.task(‘release’, [‘styles’]); ``` release只是依赖styles task，相当于styles的别名。 请问为什么要起 style这样的一个别名来？好奇怪呀

语义更明确而已，无他

### 6. 

web ui层选型，weui、ionic、react ，怎么做选型比较呢？

看做什么，微信开发肯定weui，打包app可以考虑ionic这种hybrid方式

react只是view层的解决方案，和移动、pc无关，所以weui才计划出reactjs版本

### 7.

gulp是如何利用多核的？

orchestrator这是gulp底层依赖的task相关的核心库，它定义了task执行方式和依赖，而且支持最大可能的并发

见 https://github.com/orchestrator/orchestrator


### 8.

根据老师讲的流式，上一个输出是下一个的输入，那么流式是不是可以理解为就像jquery的链式操作一样？

类似，相当于pipe调用一次都返回当前对象，这样理解也可以，但输出输入这个就不太好理解

链式操作都是在当前对象上下文的


### 9.

D:\app\weui>gulp -s module.js:338 throw err; ^ Error: Cannot find module 'lodash' at Function.Module._resolveFilename (module.js:336:15) at Function.Module._load (module.js:286:25) at Module.require (module.js:365:17) at require (module.js:384:17) at Object.<anonymous> (D:\app\weui\node_modules\browser-sync\lib\hooks.js:3:20) at Module._compile (module.js:434:26) at Object.Module._extensions..js (module.js:452:10) at Module.load (module.js:355:32) at Function.Module._load (module.js:310:12) at Module.require (module.js:365:17) at require (module.js:384:17)

缺少依赖模块，安装上就好了

另外不建议用windows开发nodejs，问题不好说，我也不太熟悉  

### 10.

我比较讨厌Yeoman（YO），好在有一个替代品slush 请问 为什么“讨厌Yeoman（YO）”？

个人习惯而已，一般我都自己写生成器，又不能，一个生成器搞的那么复杂，至于么？


### 11.

请问如何调试gulp任务

和nodejs调试一样

- node debug
- node-inspector
- tdd/bdd

node-debug 三法三例之node debugger + node inspector

https://cnodejs.org/topic/5463f6e872f405c829029f7e

其实webstorm，vs code都支持的

### 12.

不太懂具体的应用场景，在多人合作的项目里这种构建工具应该什么时候执行呢？准备提测上线的时候统一执行一下？还是开发的过程中每一个人都在执行？如果不是每个人都即时在用的话，watch存在的意义是什么呢？

看你的团队分工，是否真的前后端分离，如果不分离就每个人都的用，如果分离，前端组自动化生成的，放到cdn上，后端只要负责用就好了

watch只在开发场景用，而且你看

```
npm install --save-dev gulp-xxx
```


### 13.

请问gulp和webpack区别和联系？

2者的功能是一样的，插件也都差不多，目前看到的区别都是使用方式、扩展等方面的

gulp是标准、通用构建工具，上面基本都讲了，使用和定义、扩展都非常简单，比webpack简单的多

webpack是新贵，支持loader和plugin机制，非常强大，而且可以把所有资源bundle到一起，目前react、babel等都使用其编译，其他细节，还需要研究，目前我了解的就这些

### 14.

请问sourcemaps是干嘛用的？

调试的，自己查

### 15.

请问推荐学习steam相关教程

文章中有讲到

https://github.com/substack/stream-handbook

有中文版的，自己搜吧

### 16.

桑大，流式和buffer的实际区别有哪些啊？没有适应过buffer

不是一样的东西，buffer可理解成把大象切块，放冰箱

流式，主要是pipe处理，多了事件监控而已，但底层也是用的buffer处理大文件的

@end

目前就这些问题，回答比较仓促，如有问题欢迎私聊
