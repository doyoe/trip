# trip

移动端经验速递

## 概要

本文档针对移动前端开发，包括 `Hybrid` 里面的web页面，非 `Native` 应用。

## 适用

所有经验适用于：`iOS6.0+`, `Android4.0+`

## 目录

* [兼容性](#user-content-compatibility)
	* [CSS伪类:active](#user-content-active)
	* [清除iOS输入框内阴影](#user-content-input-shadow)
	* [Samsung S4圆角Bug](#user-content-s4-radius)
	* [边框圆角致背景溢出](#user-content-background-overflow)
	* [一个失败的圆（圆角）](#user-content-border-radius-percentage)
	* [不要使用伪元素动画](#user-content-pseudo-element-animation)
	* [:checked与兄弟选择符一起使用的bug](#user-content-checked-sibling-bug)
	* [为什么flex布局不生效](#user-content-flex)
* [经验](#user-content-experience)
	* [禁止保存或拷贝图像](#user-content-touch-callout)
	* [取消touch高亮](#user-content-tap-highlight-color)
	* [禁止选中内容](#user-content-user-select)
	* [快速回弹滚动](#user-content-overflow-scrolling)
	* [设置添加到主屏幕的App标题](#user-content-shortcut-title)
	* [设置添加到主屏幕的App图标](#user-content-shortcut-icon)
	* [添加到主屏幕时隐藏地址栏和状态栏（即全屏）](#user-content-hide-bar)
	* [添加到主屏幕时设置系统顶栏颜色](#user-content-status-bar-style)
	* [手机号码识别](#user-content-tel)
	* [邮箱地址识别](#user-content-email)
	* [关闭iOS键盘首字母自动大写](#user-content-autocapitalize)
	* [关闭iOS输入自动修正](#user-content-autocorrect)
	* [禁止文本缩放](#user-content-text-size-adjust)
* 待续...

<a name="compatibility"></a>
## 兼容性

<a name="active"></a>
### CSS伪类:active

如果你想使用元素的伪类来实现 `按下激活` 状态，那么你需要知道以下问题：

* iOS上的几乎任何浏览器，定义元素的伪类 `:active` 都是无效；
* Android上，`Android Browser` 和 `Chrome` 都支持伪类 `:active` ，其它第三方浏览器有部分不支持；
* 定义了 `:active` 并且当前浏览器环境支持，当手指在滚动或者无意间的划过时，`:active` 状态都会被激活；

> 为了规避上述所有的问题，如果需要 `按下激活` 状态，推荐使用 `js` 新增一个 `className`

<a name="input-shadow"></a>
### 清除输入框内阴影

iOS上的几乎任何浏览器输入框（input, textarea）默认有内部阴影，但无法使用 `box-shadow` 来清除，如果不需要阴影，可以这样关闭：

```
input,
textarea {
	/* 方法1: 去掉边框 */
	border: 0;

	/* 方法2: 边框色透明 */
	border-color: transparent;

	/* 方法3: 重置输入框默认外观 */
	-webkit-appearance: none;
	appearance: none;
}
```

<a name="s4-radius"></a>
### Samsung S4圆角Bug

`Samsung S4` 手机在 `Android Browser4.4.2` 上（其他版本未测），如果你使用了 `border-radius`，并且使用了 `-webkit-transform` 属性，当使用了 `translatez` 或者 `translate3d` 值，圆角会出现问题：

```
<style>
.test {
	border: 2px solid red;
	width: 50px;
	height: 50px;
	border-radius: 50%;
	background-color: gray;
	box-shadow: 0 2px 5px rgba(0, 0, 0, .3);
	-webkit-transform: translate(0, 0) translatez(0);
	transform: translate(0, 0) translatez(0);
}
</style>
<div class="test"></div>
```

如上代码，`-webkit-transform: translate(0, 0) translatez(0)` 将会导致圆角无法包裹住 `background-color`。

当然，`-webkit-transform: translate3d(0, 0, 0)` 也是一样的，所以如果你的某个场景是这样的，那么可以直接使用 `-webkit-transform: translate(0, 0)` 来避免这个问题。


<a name="background-overflow"></a>
### 边框圆角致背景溢出

在红米和OPPO等手机某些版本的 `Android Webview` 中，如果一个元素定义了 `border` + `border-radius`，这时如果该元素有背景，那么背景将会溢出圆角之外。

之所以会出现这个问题：其主要原因是因为CSS对背景裁剪（background-clip）有不同的处理方式，通常它可以是 `border-box | padding-box | content-box` 这3种方式。

浏览器的默认裁减方式是 `border-box`，即溢出 `border` 之外的背景都将被裁减。

对于上述无法裁减边框之外背景的手机，将值定义为 `padding-box | content-box` 都能fix这问题，不过更推荐使用 `padding-box`。因为使用 `content-box`，如果定义了 `padding` 不为 `0`，背景将无法铺满元素。


<a name="border-radius-percentage"></a>
### 一个失败的圆（圆角）

在移动平台上开发时，用CSS画一个圆很简单，只需要一句代码：
```
.circle {
	border-radius: 50%;
}
```
不过，在 `Android Browser2.*` 上，这个定义将会失效，而显示为默认的矩形。

因为 `Android Browser2.*` 不支持以 `百分比` 作为 `border-radius` 的值，所以如果你需要兼容 `Android Browser2.*`，那么你可以这样：
```
.circle {
	width: 10rem;
	height: 10rem;
	border-radius: 5rem;
}
```
如果你觉得这样定义不够灵活，想懒一点，那么其实可以给 `border-radius` 预设一个比较大的值，比如 `100rem`，用以避免当元素的尺寸变了，圆角半径也得跟着变，除非元素的尺寸超出了你预设的阀值。


<a name="pseudo-element-animation"></a>
### 不要使用伪元素动画

有的时候你可能会为了减少页面上的DOM数，而使用伪元素。但如果你想给伪元素增加 `animation` 或者 `transition` 动画，这时候会碰上支持性问题。

如果你的项目需要支持以下系统版本，那么建议直接使用真实元素：

* `iOS Safari6.1及以下`
* `Android Browser4.1.*及以下`，包括一些深度定制的系统，比如：
    * 魅族 - Flyme OS 4.1.1.1C及以下（比这高的版本尚未测试过）- **咱国产能别这么坑么（安卓版本为4.4.4的魅族MX4 pro）**

<a name="checked-sibling-bug"></a>
### :checked与兄弟选择符一起使用的bug

在 `Android Browser4.2.*及以下`（可能版本稍有出入）（包括坑爹的Flyme），如果你有这样一段代码：

```
input:checked ~ .test {
  background-color: #f00;
}
```

那么将无任何效果，如果你想使得上述代码生效，有2种方式：

第一种，使用 `input` 和 `+` 进行激活：

```
html + input {}
input:checked ~ .test {
  background-color: #f00;
}
```

只要存在 `input`和 `+` 选择符配合使用的选择器（空规则集也行）即可使得上述代码激活生效。

第二种，直接换成 `+`：

```
input:checked + .test {
  background-color: #f00;
}
```

<a name="flex"></a>
### 为什么flex布局不生效

* 使用[块级元素](http://blog.doyoe.com/2015/03/09/css/%E8%A7%86%E8%A7%89%E6%A0%BC%E5%BC%8F%E5%8C%96%E6%A8%A1%E5%9E%8B%E4%B8%AD%E7%9A%84%E5%90%84%E7%A7%8D%E6%A1%86/#block-level-element)作为 `flex items（flex子项）`；

> `Android Browser4.3及以下`，`iOS Safari6.1及以下` 的 `flex子项` 需要使用块级元素，在这些版本之上还可以使用行内块元素

> 在这些版本中，如果你发现flex子项之间出现了间隙，或者在未定义换行的情况下子项自身抑或子项之间换行了，或者出现了其它不正常的情况，那么仔细看一下flex子项可能是使用了行内级元素；

* 当横向布局时，给 `flex子项` 子项定义 `width` 为非 `auto` 的值

> `Android Browser4.3及以下`，`iOS Safari6.1及以下` 的 `flex子项` 如果没有显式的定义 `width` 为非 `auto` 的值，那么子项分配父元素剩余空间时将会不符合标准预期；

* 当纵向布局时，给 `flex子项` 子项定义 `height` 为非 `auto` 的值

> `Android Browser4.3及以下`，`iOS Safari6.1及以下` 的 `flex子项` 如果没有显式的定义 `height` 为非 `auto` 的值，那么子项分配父元素剩余空间时将会不符合标准预期；

<a name="experience"></a>
## 经验

<a name="touch-callout"></a>
### 禁止保存或拷贝图像

通常当你在手机或者pad上长按图像 `img` ，会弹出选项 `存储图像` 或者 `拷贝图像`，如果你不想让用户这么操作，那么你可以通过以下方法来禁止：

```
img {
	-webkit-touch-callout: none;
}
```

> 需要注意的是，该方法只在 `iOS` 上有效。

<a name="tap-highlight-color"></a>
### 取消touch高亮

在移动设备上，所有设置了伪类 `:active` 的元素，默认都会在激活状态时，显示高亮框，如果不想要这个高亮，那么你可以通过以下方法来禁止：

```
.xxx {
	-webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}
```

<a name="user-select"></a>
### 禁止选中内容

如果你不想用户可以选中页面中的内容，那么你可以禁掉：

```
html {
	-webkit-user-select: none;
}
```

<a name="overflow-scrolling"></a>
### 快速回弹滚动

1. 早期的时候，移动端的浏览器都不支持非body元素的滚动条，所以一般都借助 iScroll;
2. Android 3.0/iOS解决了非body元素的滚动问题，但滚动条不可见，同时iOS上只能通过2个手指进行滚动；
3. Android 4.0解决了滚动条不可见及增加了快速回弹滚动效果，不过随后这个特性又被移除；
4. iOS从5.0开始解决了滚动条不可见及增加了快速回弹滚动效果

在iOS上如果你想让一个元素拥有像 Native 的滚动效果，你可以这样做：

```
.xxx {
	overflow: auto; /* auto | scroll */
	-webkit-overflow-scrolling: touch;
}
```

<a name="shortcut-title"></a>
### 设置添加到主屏幕的App标题

`iOS Safari` 允许用户将一个网页添加到主屏幕然后像 `App` 一样来操作它。我们知道每个 `App` 下方都会有一个名字，`iOS Safari` 提供了一个私有的 `meta` 来定义这个名字，代码如下：

```
<meta name="apple-mobile-web-app-title" content="Web App名称" />
```

`Android Chrome31.0`，`Android Browser5.0` 也开始支持添加到主屏幕了，但并没有提供相应的定义标题的方式，所以如果你想统一 `iOS` 和 `Android` 平台定义 Web app 名称的方式，可以使用 `title` 标签来定义，代码如下：

```
<title>Web App名称</title>
```

但如果你想要网页标题和App名字不一样的话，那就只有iOS才行。

<a name="shortcut-icon"></a>
### 设置添加到主屏幕的App图标

当我们将一个网页添加到主屏幕时，除了会需要设置标题之外，肯定还需要能够自定义这个App的图标，代码如下：

```
<link rel="apple-touch-icon" href="app.png" />
```

不过该方案，在拟物设计的 `iOS6及以前` 会自动为图标添加一层高光效果，`iOS7` 已使用了扁平化设计，所以如果使用该方案，在不同版本下得到的效果会不一致。

当然，你也可以使用原图作为App的图标，用以保持各平台表现一致，代码如下：

```
<link rel="apple-touch-icon-precomposed" href="app.png" />
```

如果你想给不同的设备定不同的图标，可以通过 `sizes` 属性来定义，形如：

```
<link rel="apple-touch-icon" sizes="76x76" href="ipad.png@1x" />
<link rel="apple-touch-icon" sizes="120x120" href="iphone-retina@2x.png" />
<link rel="apple-touch-icon" sizes="152x152" href="ipad-retina@2x.png" />
<link rel="apple-touch-icon" sizes="180x180" href="iphone-retina@3x.png" />
```

规则如下：

* 如果没有跟相应设备推荐尺寸一致的图标，会优先选择比推荐尺寸大并且最接近推荐尺寸的图标。
* 如果没有比推荐尺寸大的图标，会优先选择最接近推荐尺寸的图标。
* 如果有多个图标符合推荐尺寸，会优先选择包含关键字precomposed的图标。

实际情况下，大部分智能手机都接近或者已经达到视网膜屏质量，所以如果想省事的话，可以分别为 `iPhone` 和 `iPad` 定义一种高质量的 `icon` 即可。

该方案在 `iOS` 和 `Android` 上都通用。

<a name="hide-bar"></a>
### 添加到主屏幕时隐藏地址栏和状态栏（即全屏）

当我们将一个网页添加到主屏幕时，会更希望它能有像 `App` 一样的表现，没有地址栏和状态栏全屏显示，代码如下：

```
<meta name="apple-mobile-web-app-capable" content="yes" />
```

该方案在 `iOS` 和 `Android` 上都通用。

<a name="status-bar-style"></a>
### 添加到主屏幕时设置系统顶栏颜色

当我们将一个网页添加到主屏幕时，还可以对 `系统显示手机信号、时间、电池的顶部状态栏` 颜色进行设置，前提是开启了：

```
<meta name="apple-mobile-web-app-capable" content="yes" />
```

有了这个前提，你可以通过下面的方式来进行定义：

```
<meta name="apple-mobile-web-app-status-bar-style" content="black" />
```

content只有3个固定值可选：default | black | black-translucent

* 如果设置为 `default`，状态栏将为正常的，即白色，网页从状态栏以下开始显示；
* 如果设置为 `black`，状态栏将为黑色，网页从状态栏以下开始显示；
* 如果设置为 `black-translucent`，状态栏将为灰色半透明，网页将充满整个屏幕，状态栏会盖在网页之上；

该设置只在 `iOS` 上有效。

<a name="tel"></a>
### 手机号码识别

在 `iOS Safari` （其他浏览器和Android均不会）上会对那些看起来像是电话号码的数字处理为电话链接，比如：

* 7位数字，形如：1234567
* 带括号及加号的数字，形如：(+86)123456789
* 双连接线的数字，形如：00-00-00111
* 11位数字，形如：13800138000

可能还有其他类型的数字也会被识别，但在具体的业务场景中，有些时候这是不必须的，所以你可以关闭电话自动识别，然后在需要拨号的地方，开启电话呼出和短信功能。

1. 关闭电话识别：

```
<meta name="format-detection" content="telephone=no" />
```

2. 开启电话功能：

```
<a href="tel:123456">123456</a>
```

3. 开启短信功能：

```
<a href="sms:123456">123456</a>
```

<a name="email"></a>
### 邮箱地址识别

在 `Android` （iOS不会）上，浏览器会自动识别看起来像邮箱地址的字符串，不论有你没有加上邮箱链接，当你在这个字符串上长按，会弹出发邮件的提示。

1. 关闭邮箱地址识别：

```
<meta name="format-detection" content="email=no" />
```

2. 开启邮件发送：

```
<a href="mailto:dooyoe@gmail.com">dooyoe@gmail.com</a>
```

> 如果想同时关闭电话和邮箱识别，可以把它们写到一条 meta 内，代码如下：

```
<meta name="format-detection" content="telephone=no,email=no" />
```

<a name="autocapitalize"></a>
### 关闭iOS键盘首字母自动大写

在iOS中，默认情况下键盘是开启首字母大写的功能的，如果业务不想出现首字母大写，可以这样：

```
<input type="text" autocapitalize="off" />
```

<a name="autocorrect"></a>
### 关闭iOS输入自动修正

在iOS中，默认输入法会开启自动修正输入内容的功能，如果不需要的话，可以这样：

```
<input type="text" autocorrect="off" />
```

<a name="text-size-adjust"></a>
### 禁止文本缩放

当移动设备横竖屏切换时，文本的大小会重新计算，进行相应的缩放，当我们不需要这种情况时，可以选择禁止：

```
html {
	-webkit-text-size-adjust: 100%;
}
```

> 需要注意的是，PC端的该属性已经被移除，该属性在移动端要生效，必须设置 `meta viewport'


待续啊待续。。。






