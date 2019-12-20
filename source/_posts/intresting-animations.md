---
title: 有趣的动画
date: 2019-12-20 18:09:33
tags: 动画
---

许多看起来效果很惊艳的动画，其实实现原理很简单。 下面是几种有意思的动画实现：


# 彩虹波形图

1. 步骤1：先绘制单条波形
2. 步骤2：设置animation的keyframes，从0% 到 100% 设为20帧， 对background颜色，height和margin-top的值做周期性设置。
3. 步骤3：再增加19个div
4. 步骤3：每个div的动画开始时间依次递减，这样就看到有规律波动的波形图了

<iframe height="339" style="width: 100%;" scrolling="no" title="Responsive CSS bars" src="https://codepen.io/April666/embed/yLLbzyO?height=339&theme-id=dark&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/April666/pen/yLLbzyO'>Responsive CSS bars</a> by tuzilingdang
  (<a href='https://codepen.io/April666'>@April666</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


#  旋转的小圆

这里的实现很有趣的地方在于： 看起来是6个圆在转，其实html元素只有一个div。

1. 步骤1：先画一个圆（把div的样式变成圆形，不用给背景色哦）
2. 步骤2：box-shadow设置多层，每层给一个颜色，设置一个合适的扩散半径。这时候圆形是叠加在一起的。
3. 步骤3：按角度和半径计算每个圆形圆心的位置，设置box-shadow每个圆的X偏移和Y偏移量。六个静止的圆就画好了。
4. 步骤3：在keyframe中增加 transform:rotate(-360deg); animation-timing-function设置linear，animation-iteration-count设置成infinite，用animation让圆形动起来。

<iframe height="469" style="width: 100%;" scrolling="no" title="rotate circular" src="https://codepen.io/April666/embed/abbWjRe?height=469&theme-id=dark&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/April666/pen/abbWjRe'>rotate circular</a> by tuzilingdang
  (<a href='https://codepen.io/April666'>@April666</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

#  嵌套黑白圆圈

这个动画效果较前两个更为复杂，但实现也只用了一个div元素。具体实现原理可参考前两个动画，只是使用了伪元素和boxshadow做出了更多原色的效果。

1. 步骤1：使用过after和before以及boxshow画出几种不同的大小和位置的圆圈。
2. 步骤2：在伪元素和元素本身加上动画。
3. 步骤3：不得不说，box-shawdow真是个好东西，以前只用它给卡片加个阴影，没想到做起动画来这么给力。

<iframe height="498" style="width: 100%;" scrolling="no" title="animation" src="https://codepen.io/April666/embed/gOOWGXV?height=498&theme-id=dark&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/April666/pen/gOOWGXV'>animation</a> by tuzilingdang
  (<a href='https://codepen.io/April666'>@April666</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

# 扭动的圆

有点像一只虫子，实现也比较简单，思路见上面两个动画。

<iframe height="265" style="width: 100%;" scrolling="no" title="eYmvrwL" src="https://codepen.io/April666/embed/eYmvrwL?height=265&theme-id=dark&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/April666/pen/eYmvrwL'>eYmvrwL</a> by tuzilingdang
  (<a href='https://codepen.io/April666'>@April666</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

#  替代gif的动画效果

实现原理是把很多张帧图片合成一张，跟最原始电影的原理一样。这里可以电视图片的元素当成一个固定位置的电影屏幕，不停循环替换帧，就可以有动态效果了。 替换帧的方式使用velocity库，在js中控制背景图的位置就可以实现。

其实就是这样一张图片：
![](https://s3-us-west-2.amazonaws.com/s.cdpn.io/5973/muybridge_horse.jpg)

合成图片的工具可以使用[gka](https://github.com/gkajs/gka)，请设计同学帮忙生成多张帧序列图就可以啦。

<iframe height="265" style="width: 100%;" scrolling="no" title="animation2" src="https://codepen.io/April666/embed/qBBmPKJ?height=265&theme-id=dark&default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/April666/pen/qBBmPKJ'>animation2</a> by tuzilingdang
  (<a href='https://codepen.io/April666'>@April666</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>



以上是几种有趣的动画实现，后面的文章会深入讲一下几种不同的动画实现方式。
