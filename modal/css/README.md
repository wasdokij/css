# 打破CSS小小瓶颈

早前看到了张鑫旭老师的一篇文章《说说CSS学习中的瓶颈》http://www.zhangxinxu.com/wordpress/2012/07/bottleneck-css-study/ 由此开始了，一份属于自己的CSS规范和开始深入理解css。

## 正文从这里开始

维护最大的难题在于看不懂... <br>
打开别人的代码总会冒出，写的什么鬼？ <br>
没有模块划分、嵌套深如地狱、样式重复不会利用样式优先级等，这些维护时的痛点很多开发者应该都深有体会。。。 <br>
其实本文主要想讲的是，如何管理好页面、组件，把每个组件给颗粒化。***但！*** 更重要的一些规范很多开发者有没有，因子我觉得，有了自己的规范，有了约束、有了规律，以上的问题就可以很好的解决了，所以一下开始学习路线.... `PS`: 记得点下文中放出的每个`链接`，并且看完哦


## 主要解决的痛点：
> * [命名规范书写规范](#name)
> * [css模块划分](#module)
> * [CSS权重及防止嵌套过深（事关性能和维护问题）](#hierarchy)
> * [层叠上下文和层叠顺序（很多不理解层叠顺序的朋友，写页面时会出现奇奇怪怪的问题， 比如定位、浮动等导致的问题）](#stratum)

---
<a name="name"></a>
### 解决命名难题，整合一套css规范
英文不好的，比如我！ 可以做一张表整理一下命名表 <br>
`PS`：这里有一张别人整理的表 https://www.cnblogs.com/zsy0712/p/6225947.html
##### 常见class关键词：
 ```css
  布局类：header, footer, container, main, content, aside, page, section
  包裹类：wrap, inner
  区块类：region, block, box
  结构类：hd, bd, ft, top, bottom, left, right, middle, col, row, grid, span
  列表类：list, item, field
  主次类：primary, secondary, sub, minor
  大小类：s, m, l, xl, large, small
  状态类：active, current, checked, hover, fail, success, warn, error, on, off
  导航类：nav, prev, next, breadcrumb, forward, back, indicator, paging, first, last
  交互类：tips, alert, modal, pop, panel, tabs, accordion, slide, scroll, overlay,
  星级类：rate, star
  分割类：group, seperate, divider
  等分类：full, half, third, quarter
  表格类：table, tr, td, cell, row
  图片类：img, thumbnail, original, album, gallery
  语言类：cn, en
  论坛类：forum, bbs, topic, post
  方向类：up, down, left, right
  其他语义类：btn, close, ok, cancel, switch; link, title, info, intro, more, icon; form, label, search, contact, phone, date, email, user; view, loading...
 ```

页面分成模块然后以一个单词来名，模块下的组件(模块+组件)的命名方式，也就是，姓氏命名法（祖姓 + 名），如下图  如：
```css
.header { }
.header > .header-button { }
```

###### PS 模块命名约定
约定好全局公共模块命名，如
```css
.tb{ }// 淘宝
.tb-xxx{ }// 淘宝-xxx
```
在子孙模块数量可预测的情况下，严格继承祖先模块的命名前缀,要是子模块不多的时候就要写全。
```css
<div class="modulename">
	<div class="modulename-cover"></div>
	<div class="modulename-info"></div>
</div>
```
当子孙模块超过`4级`或以上的时候，可以考虑在祖先模块内具有识辨性的独立缩写作为新的子孙模块
```css
<div class="modulename">
	<div class="modulename-cover"></div>
	<div class="modulename-info">
    	<div class="modulename-info_user">
    		<div class="modulename-info-user-img">
    			<img src="" alt="">
    			<!-- 这个时候 miui 为 modulename-info-user-img 首字母缩写-->
    			<div class="miui-tit"></div>
    			<div class="miui-txt"></div>
    			...
    		</div>
    	</div>
    	<div class="modulename-info-list"></div>
	</div>
</div>
```

##### 命名好了，那就差一个css规范了<br>
哪怕自己一个人开发也要养成良好的习惯，每次写项目时在.css文件头部放上一行带有规范地址的注释，也利于自己或他人以后维护。

可以一下看我自己的这个规范：https://github.com/wasdokij/css/blob/master/README.md

<a name="module"></a>
### css模块划分
世界很美好！很多前人已经总结很好了，  百度、google了一下，所以就在这里放好机票，自行过去吧....


`提示`：记得看完，并且理解！！！ 开发中，当自己发现css不整洁的时候要停下来！再看一遍、再看一遍、再看一遍。 保持css代码整洁可读，否则html部分的结构也会很槽糕！

 ***google*** （英文原版）: http://rscss.io/components.html

 ***百度***（翻译过的）: https://segmentfault.com/a/1190000008137332

`小提示`： 开发小技巧-->
 * 拿到设计稿先规划好公共模块-->模块里再细化组件-->规划每个组件的命名！
 * 如果英文不好的朋友，可以像我一样，把常用的模块和组件命名列成一张表(比较长的单词可以缩写，和小伙伴一起约定好)。

---
<a name="hierarchy"></a>
### CSS权重等级及浏览器渲染机制

浏览器在渲染css时是有自己的规则的（权重越高就优先渲染）<br>

**权重的基本规则：**
* 相同的权重：以后面出现的选择器为最后规则
* 不同的权重，权重值高则生效

***4个等级的定义***：
1. 第一等：内联样式，如: style=""，权值为1000。
2. 第二等：ID选择器，如：#content，权值为100。
3. 第三等：类、伪类和属性选择器，如： .content，权值为10。
4. 第四等：元素、伪元素选择器，如： h2、:before 与 :after，权值为1。

`小提示`：!important 为最高权重，慎用！

***浏览器渲染机制***：

浏览器读取css选择器有一个很重要的原则，那就是从右到左读取。所以尽量避免使用后代选择器，要去除不必要的祖先元素，以及防止嵌套过深，可以考虑使用类选择器来替换后代选择器
```css
.content div {}  // 浏览器先去找到所有的div这个标签，然后再去找div上面的.content这个样式，往上找父节点，找不到就继续往上...，这样会需要更长的渲染时间。
.content .article {} // 这样就会直接了当的去寻找了
```
***利用好关系选择符提升css权重***：
```css
1.利用选择符提高css权重例子：
.search-form  .field { /* ... */ }
.search-form > .field { /* ... */ } // 尽量用子选择符 > ,少用包含选择符 (空格)，以更好地避免冲突
.btn.action { /* ... */ } // 多种属性或状态, 如：选中按钮
```

`知识点补充`（出自w3cplus大漠老师的文章）：

[《CSS3基本选择器》](https://www.w3cplus.com/css3/basic-selectors)

[《CSS3属性选择器》](https://www.w3cplus.com/css3/attribute-selectors)

[《CSS3伪类选择器》](https://www.w3cplus.com/css3/pseudo-class-selector)

[《CSS选择器优化》](https://www.w3cplus.com/css/css-selector-performance)

---
<a name="stratum"></a>
### 层叠上下文和层叠顺序
这部分可以看张鑫旭老师的文章，理解透这部分对页面布局及常见问题处理会提升一个档次（这部分最好对定位、浮动等有所理解后，看这篇才会理解得更透彻）
http://www.zhangxinxu.com/wordpress/2016/01/understand-css-stacking-context-order-z-index/