
今年因为工作的原因，接触到了很多关于前端性能优化的知识。乘此机会在前人的基础上做了些总结与归纳。。。。

#输入网址后

##1.输入网址 --- 告诉浏览器你要去哪里

##2.浏览器查找DNS --- 网络世界是IP地址的世界，DNS就是ip地址的别名。从本地DNS到最顶级DNS一步一步的网上爬，直到命中需要访问的IP地址

   ·  DNS预解析 --- 使用CDN缓存，加快解析CDN寻找到目标地址（dns-prefetch）

##3.客户端和服务器建立连接 --- 建立TCP的安全通道，3次握手

   ·  CDN加速 --- 使用内容分发网络，让用户更快的获取到所要内容

   ·  启用压缩 --- 在http协议中，使用类似Gzip压缩的方案（对服务器资源不足的时候进行权衡）

   ·  使用HTTP/2协议 --- http2.0针对1.0优化了很多东西，包括异步连接复用，头压缩等等，使传输更快

##4.浏览器发送http请求 --- 默认长连接（复用一个tcp通道，短连接：每次连接完就销毁）

 ·  减少http请求 --- 每个请求从创建到销毁都会消耗很多资源和时间，减少请求就可以相对来说更快展示内容

 ·  压缩合并js文件以及css文件

 ·  针对图片，可将图片进行合并然后下载，通过css Sprites切割展示（控制大小，太大的话反而适得其反）

 ·  使用http缓存 --- 缓存原则：越多越好，越久越好。让客户端发送更少请求，直接从本地获取，加快性能。

 ·  减少cookie请求 --- 针对非必要数据（静态资源）请求，进行跨域隔离，减少传输内容大小。

 ·  预加载请求 --- 针对一些业务中场景可预加载的内容，提前加载，在之后的用户操作中更少的请求，更快的响应

 ·  选择get和post --- 在http定义的时候，get本质上就是获取数据，post是发送数据的。get可以在一个TCP报文完成请求，但是post先发header，再发送数据。

 ·  缓存方案选型 --- 递进式缓存更新（防止一次性丢失大量缓存，导致负载骤多）

##5.服务器响应请求 --- tomcat、IIS等服务器通过本地映射文件关系找到地址或者通过数据库查找到数据，处理完成返回给浏览器

 ·  后端框架选型 --- 更快的响应，前端更快的操作。

 ·  数据库选型和优化 --- 更快的响应，前端更快的操作。

##6.浏览器接受响应 --- 浏览器根据报文头里面的数据进行不同的响应处理

 ·  解耦第三方依赖 --- 越多的第三方的不确定因素，会导致web的不稳定性和不确定性

 ·  避免404资源 --- 请求资源不到浪费了从请求到接受的所有资源

##7.浏览器渲染顺序

 ·  HTML解析开始构建dom树

 ·  外部脚本和样式表加载完毕

 ·  尽快加载css，首先将CSSOM对象渲染出来，然后进行页面渲染，否则导致页面闪屏，用户体验差

   css选择器是从右往左解析的，so类似#test a {color: #444},css解析器会查找所有a标签的祖先节点，所以效率不是那么高

   在css的媒介查询中，最好不要直接和任何css规则直接相关。最好写到link标签中，告诉浏览器，只有在这个媒介下，加载指定这个css

 · 脚本在文档内解析并执行

   按需加载脚本，例如现在的webpack就可以打包和按需加载js脚本

   将脚本标记为异步，不阻塞页面渲染，获得最佳启动，保证无关主要的脚本不会阻塞页

   慎重选型框架和类库，避免只是用类库和框架的一个功能或者函数，而引用整个文件。

 · HTML DOM完全构造起来

  DOM 的多个读操作（或多个写操作），应该放在一起。原则：统一读、统一写。

 . 图片和外部内容加载

  对多媒体内容进行适当优化，包括恰当使用文件格式，文件处理、渐进式渲染等

  避免空的src，空的src仍然会发送请求到服务器

  避免在html内容中缩放图片，如果你需要使用小图，则直接使用小图

. 网页完成加载

  服务端渲染，特别针对首屏加载很重要的网站，可以考虑这个方案。后端渲染结束，前端接管展示。








#css的性能优化：


##1.内联首屏关键CSS

通过link标签引用外部css文件。需要在HTML文档下载完成后才知道所要引用的CSS文件，然后才下载它们。所以说，内联CSS能够使浏览器开始页面渲染的时间提前。但是这种方式不适用于内联较大的CSS文件。因为初始拥塞窗口。如果内联CSS后的文件超出了这一限制，系统就需要在服务器和浏览器之间进行更多次的往返，这样并不能提前页面渲染时间。因此，我们应当只将渲染首屏内容所需的关键CSS内联到HTML中。


##2.异步加载CSS

CSS会阻塞渲染，在CSS文件请求、下载、解析完成之前，浏览器将不会渲染任何已处理的内容。有时，这种阻塞是必须的，因为我们并不希望在所需的CSS加载之前，浏览器就开始渲染页面。那么将首屏关键CSS内联后，剩余的CSS内容的阻塞渲染就不是必需的了，可以使用外部CSS，并且异步加载。


##3.文件压缩

文件的大小会直接影响浏览器的加载速度，因此我们可以css进行压缩，现在的构建工具，如webpack、gulp/grunt、等也都支持CSS压缩功能。压缩后的文件能够明显减小，可以大大降低了浏览器的加载时间。


##4.有选择地使用选择器

CSS选择器的匹配是从右向左进行的，这一策略导致了不同种类的选择器之间的性能也存在差异。在使用选择器的时：

·  保持简单，不要使用嵌套过多过于复杂的选择器。

·  通配符和属性选择器效率最低，需要匹配的元素最多，尽量避免使用。

·  不要使用类选择器和ID选择器修饰元素标签，如h3#markdown-content，这样多此一举，还会降低效率。

·  不要为了追求速度而放弃可读性与可维护性。


##5.不要使用@import

首先，使用@import引入CSS会影响浏览器的并行下载。使用@import引用的CSS文件只有在引用它的那个css文件被下载、解析之后，浏览器才会知道还有另外一个css需要下载，这时才去下载，然后下载后开始解析、构建render tree等一系列操作。这就导致浏览器无法并行下载所需的样式文件。
其次，多个@import会导致下载顺序紊乱。在IE中，@import会引发资源文件的下载顺序被打乱，即排列在@import后面的js文件先于@import下载，并且打乱甚至破坏@import自身的并行下载。











#Js性能优化


##1、 </body>闭合标签之前，将所有的<script> 标签放在页面底部，确保在脚步执行之前页面已经完成渲染。

##2、 合并脚本。
下载单个 100KB 的文件将比下载 4 个 25KB 的文件更快，因此页面标签的<script>标签越少，加载也就越快，响应也越迅速。无论外链文件或者内嵌脚本。


##3、使用无阻塞下载 javascript 的方法，即在页面加载完成后才加载 Javascript 代码。

以下有几种无阻塞下载方法：

3.1使用<script>的 defer 属性
<script src="file.js" defer></script>

3.2使用动态创建的<script>元素来下载并执行代码

// 通过监听script元素接收完成事件来获得脚本加载完成时的状态// 封装标准和IE特有的实现方法函数function loadscript(url, callback) {
  let script = document.createElement('script');
  if (script.readyState) {
    //IE
    script.onreadystatechange = function() {
      if (script.readyState === 'loaded' || script.readyState === 'complete') {
        script.onreadystatechange = null;
        callback();
      }
    };
  } else {
    // 其他浏览器
    scripy.onload = function() {
      callback();
    };
  }
  script.src = url;
  document.getElementsByTagName('head')[0].appendchild(script);
}// 使用上述封装函数加载文件
loadscript('file.js', function() {
  console.log('file is loaded ～');
});

3.3使用 XHR 对象下载 javascript代码并注入页面中

let xhr = new XMLHttpRequest();
xhr.open('get', 'file.js', true);
xhr.onreadystatechange = function() {
  if (xhr.readyState === 4) {
    if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304) {
      let script = document.createElement('script');
      script.text = xhr.responseText;
      document.body.appendChild(script);
    }
  }
};

##4.将经常使用的全局变量引用储存在一个局部变量里。

在函数执行过程中，每遇到一个变量，都会搜索函数的作用域链找到第一个匹配的变量，首先查找函数内部的变量，之后再沿着作用域链逐层寻找。因此，若我们要访问最外层的变量（全局变量），则相比直接访问内部的变量而言，会带来比较大的性能损耗。
##5.对象的读取（减少对象成员的查找次数和嵌套深度）

javascript中，主要分为字面量、局部变量、数组元素和对象这四种。访问字面量和局部变量的速度最快，而访问数组元素和对象成员相对较慢。而访问对象成员的时候，就和作用域链一样，是在原型链上进行查找。因此，若查找的成员在原型链位置太深，则访问速度越慢。因此，我们应该尽可能的减少对象成员的查找次数和嵌套深度

##6.最小化DOM的操作次数，尽可能的用javascript来处理，并且尽可能的使用局部变量储存DOM节点


##7.减少重绘和重排，批量修改样式时，“离线”操作 DOM 树，使用缓存，并减少访问布局信息的次数。

减少“重绘”和“重排”

1、使元素脱离文档流

2、对其应用多次改变

3、把元素带回文档中

// 第一种方式：隐藏元素，应用修改，重新显示let ul = document.getElementById('nyList');

ul.style.display = 'none';// 向ul中添加附加数据

appendDataToElement(ul, data);

ul.style.display = 'block';

// 第二种方式：使用文档片段（document fragment）在当前DOM之外构建一个子树，再把它拷贝到文档(推荐)let 

fragment = document.createDocumentFragment();// 向fragment中添加附加数据

appendDataToElement(ul, data);document.getElementById('myList').appendChild(fragment);

// 第三种方式：将原始元素拷贝到一个脱离文档的节点中，修改副本，完成后再替换原始元素

let old = document.getElementById('myList');let clone = old.cloneNode(true);// 向clone中添加附加数据

appendDataToElement(ul, data);

old.parentNode.replaceChild(clone, old);


##8.使用事件委托减少事件处理器的数量

事件委托就是将目标节点的事件移到父节点来处理，由于浏览器冒泡的特点，当目标节点触发了该事件的时候，父节点也会触发该事件。因此，由父节点来负责监听和处理该事件。

function handleClick(target) { 
    
// 点击列表项的处理事件 

}

 function delegate (e) { 
     
// 判断目标对象是否为列表项 

if (e.target.nodeName === 'LI') { 
    
handleClick(e.target); }

 } 
 
const parent = document.getElementById('parent'); parent.addEventListener('click', delegate);



##9.使用 Web Worker 来处理复杂的计算

JavaScript 是单线程的，因此 JavaScript 在执行复杂计算的时候很可能会阻塞线程，导致页面假死。但 Web Worker 的出现，以另外一种方式给了我们多线程的能力，可以将复杂计算放在 worker 中进行，当计算完成后，以postMessage的形式将结果传回来。
对于单个函数，因为 Web Worker 接受一个脚本的 url 作为参数，使用 URL.createObjectURL 方法，我们可以将一个函数的内容转换为 url，利用它创建一个 worker。
var workerContent = `

 self.onmessage = function(evt){ 
     
// ... 

// 在这里进行复杂计算

 var result = complexFunc(); 
 
// 将结果传回 

self.postMessage(result); };` 

// 得到 url 

var blob = new Blob([workerContent]);

 var url = window.URL.createObjectURL(blob);
 
 // 创建 worker var worker = new Worker(url);
 


##10.对高频触发的事件进行节流或消抖

函数防抖（debounce）：当持续触发事件时，一定时间段内没有再触发事件，事件处理函数才会执行一次，如果设定的时间到来之前，又一次触发了事件，就重新开始延时。
将几次操作合并为一此操作进行。原理是维护一个计时器，规定在delay时间后触发函数，但是在delay时间内再次触发的话，就会取消之前的计时器而重新设置。这样一来，只有最后一次操作能被触发。

防抖debounce代码：

function debounce(fn, wait) {
    var timeout = null;
    return function() {

        if(timeout !== null) 
                clearTimeout(timeout);
        timeout = setTimeout(fn, wait);
    }
}
// 处理函数
function handle() {
    console.log(Math.random()); 
}
// 滚动事件
window.addEventListener('scroll', debounce(handle, 1000));

函数节流（throttle）：当持续触发事件时，保证一定时间段内只调用一次事件处理函数。
使得一定时间内只触发一次函数。原理是通过判断是否到达一定时间来触发函数。

节流throttle代码（时间戳）：

var throttle = function(func, delay) {
            var prev = Date.now();
            return function() {
                var context = this;
                var args = arguments;
                var now = Date.now();
                if (now - prev >= delay) {
                    func.apply(context, args);
                    prev = Date.now();
                }
            }
        }
        function handle() {
            console.log(Math.random());
        }
        window.addEventListener('scroll', throttle(handle, 1000));


       节流throttle代码（定时器）：

 var throttle = function(func, delay) {
            var timer = null;
            return function() {
                var context = this;
                var args = arguments;
                if (!timer) {
                    timer = setTimeout(function() {
                        func.apply(context, args);
                        timer = null;
                    }, delay);
                }
            }
        }
        function handle() {
            console.log(Math.random());
        }
        window.addEventListener('scroll', throttle(handle, 1000));

区别： 函数节流不管事件触发有多频繁，都会保证在规定时间内一定会执行一次真正的事件处理函数，而函数防抖只是在最后一次事件后才触发一次函数。 比如在页面的无限加载场景下，我们需要用户在滚动页面时，每隔一段时间发一次 Ajax 请求，而不是在用户停下滚动页面操作时才去请求数据。这样的场景，就适合用节流技术来实现。



