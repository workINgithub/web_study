# line-height

### “大值特性”

首先一定心中有一个前提，
行内元素前会有一个“支柱”元素（幽灵空白节点）。

所以在./value.html的例子中，会出现这样的情况。内联元素`span`的`line-height`是20px，然后块级元素`box`的line-height高度是96px。然后`box`的高度是96px，这就是因为块级元素的`line-height`属性作用到了幽灵空白节点上。然后行框以最高的内联盒子决定的。


