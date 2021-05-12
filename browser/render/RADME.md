# 渲染过程

前言：因为这块内容暂时没有能力去深入源码，或者是硬件，所以阅读了很多文章来补充这方面的知识。希望在后面能有能力去探究下底层。

### 一些定义

1. 帧：在视频领域，电影、电视、数字视频等可视为随时间连续变换的许多张画面，其中帧是指每一张画面。
2. rAF: requestAnmaiteFrame函数
3. Jank既指“卡顿”表现，通常是由于在主线程上执行长任务，阻止渲染或在后台进程上消耗过多处理器能力所致。
4. 信息转换为屏幕上的像素，我们称为光栅化。
5. 分层：为了分清哪些元素位于哪些图层（这里要和absolute元素脱离文档流要区分开来，他们还是属于同一层的。），主线程遍历布局树创建图层树（此部分在 DevTools 性能面板中称为“Update Layer Tree”）。如果页面的某些部分应该是单独图层（如滑入式侧面菜单）但没拆分出来，你可以使用 CSS 中的 will-change 属性来提示浏览器。
6. 渲染管线（render pipeline）：指浏览器渲染的一个流程。（可以参看./render-process.png)
7. 合成是一种将页面的各个部分分层，分别光栅化，并在称为合成线程的单独线程中合成为页面的**技术**。
8. 绘制四边形：包含诸如图块在内存中的位置，以及合成时绘制图块在页面中的位置等信息。
9. 合成帧：一个绘制四边形的集合，代表一个页面的一帧。
10. Raster Scheduled （栅格化规划）and Rasterize（栅格化）
11. janks: 卡顿

### 合成（composite）

经前面几个步骤，浏览器知道文档的结构、每个元素的样式、页面的几何形状和绘制顺序，它是如何绘制页面的？

> 处理这种情况的一种简单的方法是，先在光栅化视窗内的画面，如果用户滚动页面，则移动光栅框，并光栅化填充缺少的部分。这就是 Chrome 首次发布时处理光栅化的方式。但是，现代浏览器会运行一个更复杂的过程，我们称为合成。

合成的定义已经在“一些定义”章节中给出了。简单来说就是先将页面分成独立的layer，分别光栅化后，在将其合成的技术。


**那这种比先前的技术哪里有优势？**
就比如说, 如果输入事件是滚动。那么合成线程不需要涉及主线程的情况下完成就行了。

**合成的流程**
1. 合成线程将(若干个)图层分块后发送到光栅线程
2. 光栅线程光栅化每个小块后将他们存储在显存中。
3. 一旦块被光栅化，合成线程会收集这些块的信息（称为绘制四边形）创建合成帧。
4. 合成帧通过 IPC（进程间通讯）提交给浏览器进程，可以从 UI 线程或其他插件的渲染进程添加另一个合成帧。这些合成器帧被发送到 GPU 然后在屏幕上显示。

### 参考：

[[译] 现代浏览器内部揭秘（第三部分）](https://juejin.cn/post/6844903692894732295)

例子：

// 疑似是app.update()中的m.offsetTop导致在一次渲染管线中，js线程占用太多时间了。
[jank](https://googlechrome.github.io/devtools-samples/jank/)
