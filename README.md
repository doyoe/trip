# trip

移动端经验速递

## 概要

本文档针对移动前端开发，包括 `Hybrid` 里面的web页面，非 `Native` 应用。

## 适用

所有经验适用于：`iOS6.0+`, `Android4.0+`

## 目录

* [兼容性](#compatibility)
	* [CSS伪类:active](#active)
* [经验](#experience)
	* [禁止保存或拷贝图像](#touch-callout)
	* [取消touch高亮](#tap-highlight-color)
	* [禁止选中内容](#user-select)
	* [快速回弹滚动](#overflow-scrolling)
	* [手机号码识别](#tel)
	* [邮箱地址识别](#email)
	* [关闭iOS键盘首字母自动大写](#autocapitalize)
	* [关闭iOS输入自动修正](#autocorrect)
	* [禁止文本缩放](#text-size-adjust)
	* [清除输入框内阴影](#input-shadow)
* 待续...

<a name="compatibility"></a>
## 兼容性

<a name="active"></a>
### CSS伪类:active

如果你想使用元素的伪类来实现激活状态，那么你需要知道以下问题：

1. iOS（非PC）上任何元素的伪类 `:active` 都无效；
2. Android上，`Android Browser` 和 `Chrome` 都支持伪类 `:active` ，其它第三方浏览器基本上都不支持；


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
<a mailto:dooyoe@gmail.com">dooyoe@gmail.com</a>
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

<a name="input-shadow"></a>
### 清除输入框内阴影

在iOS上，输入框默认有内部阴影，但无法使用 `box-shadow` 来清除，如果不需要阴影，可以这样关闭：

```
input,
textarea {
	border: 0; /* 方法1 */
	-webkit-appearance: none; /* 方法2 */
}
```


	
	
	
	
	

