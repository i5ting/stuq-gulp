# StuQ-Gulp实战和原理解析

大家好，我是桑世龙，github和cnodejs上的i5ting，目前在天津创业，公司目前使用技术主要是nodejs，算所谓的MEAN（mongodb  + express + angular + node），所有代码都部署在阿里云上

我的名字i5ting，原因是我媳妇叫张婷，哈哈

闲言少叙，下面开始正题 《Gulp实战和原理解析》

本次主要讲3部分

- 什么gulp和核心概念、用法
- 以微信前端组的weui为例子，讲解gulp在真实项目里如何使用（包括浏览器实时刷新，watch，less编译，压缩，生成sourcemap、release等）
- 最后讲解一下stream原理，给出更多实践

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

- 基于task，定义简单
- task有串行，并行，作业依赖等控制方式
- 通过xxxfile来定义task

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

定义好task，下面运行一些

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


前端本身就对js非常了解，gulp可以说是plain js，语法都再简单不过，所以大家比较容易接受

### 基于nodejs

而且基本nodejs的小而美哲学，写gulp task是件很愉快的事儿

而且还可以结合nodejs世界里最好的包管理器npm，写cli或者模块特别简单

而且可以写c/c++ addon，给你无限可能

而且还有shelljs这样的库，结合shell，给你无限可能

### 易于使用

通过代码优于配置的策略，Gulp 让简单的任务简单，复杂的任务可管理。

而且对ant，rake，make等了解的人非常容易上手

### 构建快速

利用 Node.js stream的威力，你可以快速构建项目并减少频繁的 IO 操作。

而且gulp的核心代码可以高效利用你所有核的cpu，高效运行，尤其是大文件处理

###  插件机制

Gulp 严格的插件指南确保插件如你期望的那样简洁高质得工作。

### 易于学习

通过最少的 API，掌握 Gulp 毫不费力

这得益于gulp的设计，api简单，调用关系和依赖清晰，当然如果理解linux 管道或者其他类似的构建工具会容易

而且每个任务都拆分成一个task，结构清晰，利用stream和pipe写法，组成task，也是非常容易学习的。

### 总结

这里说了gulp的各种好处，它通用，高效，使用简单，学习成本低，总之，我就是要告诉你，它是非常好的构建工具，而且是长久看好的。

它也有缺点，这样说也不太合适，更准确的说，它是一个通用构建工具，所以在构建实践上，需要写的人有好的实践

像fib3这样的，只是基于比较好的构建实践而已，其他方面不是能和gulp相提并论的。

## gulp快速入门

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

自己定义一个task就是这么简单

### 依赖

```
gulp.task('default',['watch'],function() {
    console.log('default task');
});
```

这里的task有3个参数，

default是方法名称，只有default比较奇怪，会默认调用。

相当于c里的main方法

['watch']这是依赖的作业列表，它们是由顺序的，按数组顺序依次执行
第三个参数是成功执行完上面的依赖作业后执行的回调函数

这里要强大，依赖作业数组里的都执行完了，才会执行第三个参数，即当前作业具体内容


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


pipe是管道的意思，也是stream里核心概念，也就是说：上一个的输入，是下一个的输入。

src里所有js，经过处理1，处理2，然后压缩变成min.js,中间的处理pipe可以1步，也可以是n步

反正第一步处理的结果是第二步的输入，以此类推，就像生产线一样，每一步都是一个task是不是很好理解呢？

每个独立操作单元都是一个task，使用pipe来组装tasks

于是gulp就变成了基于task的积木式的工具

好吧，是时候总结一下了

### 总结

- 每一个作业都是独立，定义为task
- 通过stream的机制，上一个的输入，作为下一个的输入
- 通过pipe方法组装task，完成我们想要的业务逻辑


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

![](img/1/png)

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

至此就准备玩了，下面看一下gulpfile.js

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

- 这是最简单的task定义，无任何依赖作业
- 作业里面使用nodejs写的yargs模块，用户处理cli参数

比如此时执行`gulp -s`后者`gulp default -s`

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

server一看就知道是启动服务器，一般前端开发，都是起一个服务器在浏览器里测试

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

于是，代码变量，不用刷新浏览器就能看到效果。。。

这其实就livereload...

又可以偷懒了，祭奠f5吧！！！

其他（server，port，startPath）是browserSync的配置项，有兴趣自己扒文件吧

这里稍稍提一下weinre，因为weui这个项目是移动端h5页面，所以才会用到weinre调试的，是远程调试h5的利器

http://people.apache.org/~pmuellr/weinre-docs/latest/


总结一下

整个server就是browserSync提供的3个功能

- 起了一个server
- 支持livereload
- 自动打开网页

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

- 如果'src/**/*.less'变动，执行styles task
- 如果'src/example/**/*.{html,js}'变动，先执行'source' task，然后livereload通知浏览器

大家伙主要了解文件变动能干坏事即可，其他可自由发挥

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

都是一样的思路，编译成js或css，然后发布

这些预处理，让开发方便，高效的同时，也增加了前端的复杂度，真是老子那句话

福兮祸所伏，祸兮福所倚...

阿门。。。阿弥托佛

### source

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
- pipe是管道的意思，也是stream里核心概念，也就是说上一个的输入，是下一个的输入。


这代码里的src里的，所有不是less的文件，都丢到dist目录

```
    gulp.src('src/example/**/*.!(less)', option)
        .pipe(gulp.dest(dist))
```

然后，它又pipe一个，仅仅是为了表示顺序，无上下文件传递关系（偷懒做法而已，不可取）

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

- 编译less和生成sourcemap
- 压缩minify
- 触发livereload

### 实战总结

至此，我们就讲完了所有gulpfile里的内容，以及每个task的细节

结论是：这是一个比较典型的gulp项目

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


A stream is an abstract interface implemented by various objects in Node.js. For example a request to an HTTP server is a stream, as is stdout. Streams are readable, writable, or both. All streams are instances of EventEmitter


Stream是nodejs各种对象实现的抽象接口。

比如一个http server的请求是一个stream，stdout也是一个。

Streams可读、可写，或者兼有的。

所有的stream对象都是EventEmitter的实例


好理解么？

不好理解，还是从流式理解吧~

### 什么是流式

上一个的输出，是下一个的输入

上一个的输出，是下一个的输入

上一个的输出，是下一个的输入

### linux pipe

流式和linux pipe是一样的（也可能是最早的起源）

举例

```
ps -ef|grep boot|awk '{print $2}'|xargs kill -9
```

- ps -ef查看进程
- grep boot是过来进程里的和boot相关的所有进程
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


为什么使用Stream

- node中的I/O是异步的
- pipe
- 基于buffer节省内存，适合处理大文件
- 有事件监控

### 推荐阅读

nodejs里有五种基本的Stream：readable，writable，transform，duplex，and "classic” 

时间原因，具体使用请自己查阅api

https://github.com/substack/stream-handbook

### 八卦更多

ruby作者，松本行弘，上半年基本没干啥，写了一个叫streem的语言，

In Streem, a simple cat program looks like this:

stdin | stdout

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

- 每个操作都是是独立作业task
- 作业依赖定义非常简单，决定执行顺序
- 通过pipe组装tasks，完成业务逻辑处理

gulp的核心stream

什么是流式（重要的事情，大声喊三遍）

上一个的输出，是下一个的输入

上一个的输出，是下一个的输入

上一个的输出，是下一个的输入

好了，你已经是gulp高手了

最后让我们来展望一下美好的未来：

gulp是前端世界的主流构建工具，是大多数开源项目的选择，你值得拥有

另外公布一下weui未来会有reactjs版本，还是值得期待的。

谢谢大家，今天讲的内容就到这里，如果有什么讲的不对的、不合理的，请不吝赐教，共同学习

大家有任何不明白或者想提问的可以随时回复到StuQ微信公众号后台，我会在答疑阶段统一回答
