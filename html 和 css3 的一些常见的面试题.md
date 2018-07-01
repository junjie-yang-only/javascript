#### HTML5 CSS3

1. css3新特性

- border-radius、box-shadow
- 对文字加特效 text-shadow，线性渐变(gradient)，旋转 (transform)
- transform: rotate,translate,skew,scale    // 旋转、定位，倾斜，缩放
- 增加更多的 CSS 选择器 多背景 rgba
- 媒体查询、多栏布局
- border-image

2. HTML 新特性

- 拖拽释放（drap and drop） API
- 语义化更好的内容标签(header,nav,footer,aside,article,section)
- 音频视频 API(audio,video)
- 画布 canvas
- Geolocation 地理API
- 本地存储 localStorage 长期存储数据，浏览器保存后数据不会丢失
- sessionStorage 的数据在浏览器关闭后自动删除
- 新的技术 webWorker,webSocket,Geolocation

#### localStorage、sessionStorage 和 cookie 的区别

Cookies: 服务器和客户端都可以访问，大小只有 4KB 左右，有有效期，过期后将会删除
localStorage: 只有本地浏览器端可以访问数据，服务器不能访问本地存储直到故意通过 POST 或者 GET 的通道发送到服务器；每个域 5MB；没有过期数据
sessionStorage: 用于本地存储的一个会话（session）中的数据，这些数据只有在同一个会话中的页面才能访问并且会话结束后数据也随之销毁。因此 sessionStorage 不是一种持久化的本地存储，仅仅是会话级别的存储。而 localStorage 用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。

webStorage 和 cookie 相似，区别是它是为了更大容量存储设计的。cookie 的大小是受限的，每次请求一个新的页面的时候 Cookie 都会被发送过去，这样无形中是浪费了带宽，另外 cookie 还需要指定作用域，不可以跨域调用。
除此之外，Web Storage 拥有 setItem，getItem，removeItem，clearItem 等方法，不像 cookie 需要前端开发者自己封装；cookie 的作用是与服务器进行交互，作为 HTTP 规范的一部分存在，而 WebStorage 仅仅是为了在本地存储数据而产生的。


#### 对网站文件和资源进行优化

1. 文件合并
2. 文件最小化、文件压缩
3. 使用 CDN 托管
4. 缓存的使用

#### css3 新增的伪类

p:first-of-type 选择属于其父元素的首个 p 元素的每个 p 元素
p:last-of-type  选择属于其父元素的最后 p 元素的每个 p 元素
p:only-of-type  选择属于其父元素唯一的 p 元素的每个 p 元素
p:only-child    选择属于其父元素的唯一子元素的每个 p 元素
p:nth-child(2)  选择属于其父元素的第二子元素的每个 p 标签
:enable、:disabled 控制表单控件的禁用状态
:checked 单选框或复选框被选中

#### 语义化的理解
用正确的标签做正确的事情

html 语义化就是为了让页面的内容结构化，便于对浏览器、搜索引擎解析；
在没有样式 CSS 情况下也已一种文档格式显示，并且是容易阅读的
搜索引擎的爬虫依赖于标记来确定上下文和各个关键字的权重，重于 SEO
是阅读代码的人对网站更容易将网站分块，便于阅读维护理解。

