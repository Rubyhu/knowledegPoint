

css的性能优化：


1.内联首屏关键CSS

通过link标签引用外部css文件。需要在HTML文档下载完成后才知道所要引用的CSS文件，然后才下载它们。所以说，内联CSS能够使浏览器开始页面渲染的时间提前。但是这种方式不适用于内联较大的CSS文件。因为初始拥塞窗口。如果内联CSS后的文件超出了这一限制，系统就需要在服务器和浏览器之间进行更多次的往返，这样并不能提前页面渲染时间。因此，我们应当只将渲染首屏内容所需的关键CSS内联到HTML中。


2.异步加载CSS


CSS会阻塞渲染，在CSS文件请求、下载、解析完成之前，浏览器将不会渲染任何已处理的内容。有时，这种阻塞是必须的，因为我们并不希望在所需的CSS加载之前，浏览器就开始渲染页面。那么将首屏关键CSS内联后，剩余的CSS内容的阻塞渲染就不是必需的了，可以使用外部CSS，并且异步加载。


3.文件压缩


文件的大小会直接影响浏览器的加载速度，因此我们可以css进行压缩，现在的构建工具，如webpack、gulp/grunt、等也都支持CSS压缩功能。压缩后的文件能够明显减小，可以大大降低了浏览器的加载时间。


4.有选择地使用选择器


CSS选择器的匹配是从右向左进行的，这一策略导致了不同种类的选择器之间的性能也存在差异。在使用选择器的时：
·  保持简单，不要使用嵌套过多过于复杂的选择器。
·  通配符和属性选择器效率最低，需要匹配的元素最多，尽量避免使用。
·  不要使用类选择器和ID选择器修饰元素标签，如h3#markdown-content，这样多此一举，还会降低效率。
·  不要为了追求速度而放弃可读性与可维护性。


5.不要使用@import


首先，使用@import引入CSS会影响浏览器的并行下载。使用@import引用的CSS文件只有在引用它的那个css文件被下载、解析之后，浏览器才会知道还有另外一个css需要下载，这时才去下载，然后下载后开始解析、构建render tree等一系列操作。这就导致浏览器无法并行下载所需的样式文件。
其次，多个@import会导致下载顺序紊乱。在IE中，@import会引发资源文件的下载顺序被打乱，即排列在@import后面的js文件先于@import下载，并且打乱甚至破坏@import自身的并行下载。




Js性能优化


1、 </body>闭合标签之前，将所有的<script> 标签放在页面底部，确保在脚步执行之前页面已经完成渲染。


2、 合并脚本。


下载单个 100KB 的文件将比下载 4 个 25KB 的文件更快，因此页面标签的<script>标签越少，加载也就越快，响应也越迅速。无论外链文件或者内嵌脚本。


3、使用无阻塞下载 javascript 的方法，即在页面加载完成后才加载 Javascript 代码。
以下有几种无阻塞下载方法：


3.1使用<script>的 defer 属性


<script src="file.js" defer></script>复制代码
3.2使用动态创建的<script>元素来下载并执行代码



3.3使用 XHR 对象下载 javascript代码并注入页面中





4.将经常使用的全局变量引用储存在一个局部变量里。


在函数执行过程中，每遇到一个变量，都会搜索函数的作用域链找到第一个匹配的变量，首先查找函数内部的变量，之后再沿着作用域链逐层寻找。因此，若我们要访问最外层的变量（全局变量），则相比直接访问内部的变量而言，会带来比较大的性能损耗。


5.对象的读取（减少对象成员的查找次数和嵌套深度）


javascript中，主要分为字面量、局部变量、数组元素和对象这四种。访问字面量和局部变量的速度最快，而访问数组元素和对象成员相对较慢。而访问对象成员的时候，就和作用域链一样，是在原型链上进行查找。因此，若查找的成员在原型链位置太深，则访问速度越慢。因此，我们应该尽可能的减少对象成员的查找次数和嵌套深度


6.最小化DOM的操作次数，尽可能的用javascript来处理，并且尽可能的使用局部变量储存DOM节点


7.减少重绘和重排，批量修改样式时，“离线”操作 DOM 树，使用缓存，并减少访问布局信息的次数。
减少“重绘”和“重排”


1、使元素脱离文档流


2、对其应用多次改变


3、把元素带回文档中


// 第一种方式：隐藏元素，应用修改，重新显示let ul = document.getElementById('nyList');


ul.style.display = 'none';// 向ul中添加附加数据


appendDataToElement(ul, data);


ul.style.display = 'block';


// 第二种方式：使用文档片段（document fragment）在当前DOM之外构建一个子树，再把它拷贝到文档(推荐)let 


fragment = document.createDocumentFragment();// 向fragment中添加附加数据


appendDataToElement(ul, data);document.getElementById('myList').appendChild(fragment);


// 第三种方式：将原始元素拷贝到一个脱离文档的节点中，修改副本，完成后再替换原始元素


let old = document.getElementById('myList');let clone = old.cloneNode(true);// 向clone中添加附加数据


appendDataToElement(ul, data);


old.parentNode.replaceChild(clone, old);


8.使用事件委托减少事件处理器的数量


事件委托就是将目标节点的事件移到父节点来处理，由于浏览器冒泡的特点，当目标节点触发了该事件的时候，父节点也会触发该事件。因此，由父节点来负责监听和处理该事件。


function handleClick(target) { 
    

// 点击列表项的处理事件 


}


 function delegate (e) { 
     

// 判断目标对象是否为列表项 


if (e.target.nodeName === 'LI') { 
    

handleClick(e.target); }


 } 
 

const parent = document.getElementById('parent'); parent.addEventListener('click', delegate);







9.使用 Web Worker 来处理复杂的计算


JavaScript 是单线程的，因此 JavaScript 在执行复杂计算的时候很可能会阻塞线程，导致页面假死。但 Web Worker 的出现，以另外一种方式给了我们多线程的能力，可以将复杂计算放在 worker 中进行，当计算完成后，以postMessage的形式将结果传回来。
对于单个函数，因为 Web Worker 接受一个脚本的 url 作为参数，使用 URL.createObjectURL 方法，我们可以将一个函数的内容转换为 url，利用它创建一个 worker。


var workerContent = `

 self.onmessage = function(evt){ 
     

// ... 


// 在这里进行复杂计算


 var result = complexFunc(); 
 

// 将结果传回 self.postMessage(result); };` 


// 得到 url 


var blob = new Blob([workerContent]);


 var url = window.URL.createObjectURL(blob);
 

 // 创建 worker var worker = new Worker(url);
 



10.对高频触发的事件进行节流或消抖


函数防抖（debounce）：当持续触发事件时，一定时间段内没有再触发事件，事件处理函数才会执行一次，如果设定的时间到来之前，又一次触发了事件，就重新开始延时。
将几次操作合并为一此操作进行。原理是维护一个计时器，规定在delay时间后触发函数，但是在delay时间内再次触发的话，就会取消之前的计时器而重新设置。这样一来，只有最后一次操作能被触发。



函数节流（throttle）：当持续触发事件时，保证一定时间段内只调用一次事件处理函数。
使得一定时间内只触发一次函数。原理是通过判断是否到达一定时间来触发函数。





区别： 函数节流不管事件触发有多频繁，都会保证在规定时间内一定会执行一次真正的事件处理函数，而函数防抖只是在最后一次事件后才触发一次函数。 比如在页面的无限加载场景下，我们需要用户在滚动页面时，每隔一段时间发一次 Ajax 请求，而不是在用户停下滚动页面操作时才去请求数据。这样的场景，就适合用节流技术来实现。

