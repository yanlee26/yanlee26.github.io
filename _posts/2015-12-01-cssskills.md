---
date: 2015-12-01
layout: post
title: CSS skills 
thread: 9
categories: CSS
tags: [BIT]
excerpt: CSS skills
---

# CSS 使用技巧

- 文字
	1. 水平居中

	`text-aligh:center`
	2. 垂直居中
	`div{height:35px;line-height:35px}//1/n容器高度`
- 容器
	1. 水平居中

	`div{width:760px;margin:0 auto}`
	2. 垂直居中
	`
	.outer{
		position:relative;
		height:480px;
	}
	.inner{
	position:absolute;
	top:50%;
	height:240px;
	margin-top:-120px;
	//然后，将小容器定位为absolute，再将它的左上角沿y轴下移50%，最后将它margin-top上移本身高度的50%即可。
	}
	`
- 图片宽度自适应
	`img{max-width:100%}`
- 3D按钮
	`
	button {
　　　　background: #888;
　　　　border: 1px solid;
　　　　border-color: #999 #777 #777 #999;
　　}
	`
- font属性

`
body { 
　　　　font-family: Arial, Helvetica, sans-serif; 
　　　　font-size: 13px; 
　　　　font-weight: normal; 
　　　　font-variant: small-caps; 
　　　　font-style: italic; 
　　　　line-height: 150%; 
　　}
body { 
　　　　font: italic small-caps normal 13px/150% Arial, Helvetica, sans-serif; 
　　}
`
- link状态设置顺序
	`
	a:link 
　　a:visited 
　　a:hover 
　　a:active
	`
- IE条件注释
`<!--[if IE]> 
　　　　<link rel="stylesheet" type="text/css" href="ie-stylesheet.css" /> 
　　< ![endif]-->`

- CSS 优先级
`
　　行内样式 > id样式 > class样式 > 标签名样式

`
- font-size基准
`
　　body {font-size:62.5%;}
//浏览器的缺省字体大小是16px，你可以先将基准字体大小设为10px：
`
- Text-transform和Font Variant
`
p {text-transform: uppercase} 
　　p {text-transform: lowercase} 
　　p {text-transform: capitalize}
`
- reset
`

`
- 图片列表
`
ul {list-style: none}
　　ul li { 
　　　　background-image: url("path-to-your-image"); 
　　　　background-repeat: none; 
　　　　background-position: 0 0.5em; 
　　}
`
- 三角形
`
.triangle { 
　　　　border-color: transparent transparent green transparent;
　　　　border-style: solid; 
　　　　border-width: 0px 300px 300px 300px; 
　　　　height: 0px; 
　　　　width: 0px; 
　　}
`
- 禁止自动换行
`p { white-space:nowrap; }`
- 用图片替换文字---有时我们需要在标题栏中使用图片，但是又必须保证搜索引擎能够读到标题
`h1 { 
　　　　text-indent:-9999px; 
　　　　background:url("h1-image.jpg") no-repeat; 
　　　　width:200px; 
　　　　height:50px; 
　　}`
- 焦点突出
`　　input:focus { border: 2px solid green; }
`
- CSS 提示框
`
　　<a class="tooltip" href="#">链接文字 <span>提示文字</span></a>
a.tooltip {position: relative} 
　　a.tooltip span {display:none; padding:5px; width:200px;} 
　　a:hover {background:#fff;} /*background-color is a must for IE6*/ 
　　a.tooltip:hover span{display:inline; position:absolute;}
`
- 固定位置
`
body{ margin:0;padding:100px 0 0 0;}
　　div#header{
　　　　position:absolute;
　　　　top:0;
　　　　left:0;
　　　　width:100%;
　　　　height:<length>;
　　}
　　@media screen{
　　　　body>div#header{position: fixed;}
　　}
　　* html body{overflow:hidden;}
　　* html div#content{height:100%;overflow:auto;}
`
- 图片预加载
[预加载](https://perishablepress.com/3-ways-preload-images-css-javascript-ajax/)
- CSS选择器
[CSS选择器](http://www.ruanyifeng.com/blog/2009/03/css_selectors.html)
- 背景图定位
[定位](http://www.ruanyifeng.com/blog/2008/05/css_background_image_positioning.html)

### 3D旋转视频展示区
`
<style>
body {
    margin-top: 5em;
    text-align: center;
    color: #414142;
    background: rgb(246,241,232);
    /*制作多背景*/
    background-image: -ms-radial-gradient(farthest-side ellipse at center,  rgba(246,241,232,.85) 39%,rgba(212,204,186,.5) 100%), url("http://fs0.139js.com/file/s_jpg_857b081bjw1du3kveu19sj.jpg");
    background-image: -webkit-radial-gradient(farthest-side ellipse at center,  rgba(246,241,232,.85) 39%,rgba(212,204,186,.5) 100%), url("http://fs0.139js.com/file/s_jpg_857b081bjw1du3kveu19sj.jpg");
    background-image: radial-gradient( farthest-side ellipse at center,  rgba(246,241,232,.85) 39%,rgba(212,204,186,.5) 100%), url("http://fs0.139js.com/file/s_jpg_857b081bjw1du3kveu19sj.jpg");
    /*控制背景图像尺寸*/
    background-size: cover;
}

h1, em, #information {
    display: block;
    font-size: 25px;
    font-weight: normal;
    margin: 2em auto;
}

a {
    color: #414142;
    font-style: normal;
    text-decoration: none;
    font-size: 20px;
}
        
a:hover {
    text-decoration: underline;
}
        
#container {
    margin: 0 auto;
    width: 1024px;
}
    
.wrapper {
    display: inline-block;
    width: 310px;
    height: 100px;
    vertical-align: top;
    margin: 1em 1.5em 2em 0;
    cursor: pointer;
    position: relative;
    font-family: Tahoma, Arial;
    -webkit-perspective: 4000px;
    -moz-perspective: 4000px;
    -ms-perspective: 4000px;
    -o-perspective: 4000px;
    perspective: 4000px;
}

.item {
    height: 100px;
    -webkit-transform-style: preserve-3d;
    -moz-transform-style: preserve-3d;
    -ms-transform-style: preserve-3d;
    -o-transform-style: preserve-3d;
    transform-style: preserve-3d;
    /*给每个列表项添加过渡动画效果*/
    -webkit-transition: -webkit-transform .6s;
    -moz-transition: -moz-transform .6s;
    -ms-transition: -ms-transform .6s;
    -o-transition: -o-transform .6s;
    transition: transform .6s;
}

.item:hover {
    /*悬浮状态改变每个列表项的transform效果*/
    -webkit-transform: translateZ(-50px) rotateX(95deg);
    -moz-transform: translateZ(-50px) rotateX(95deg);
    -ms-transform: translateZ(-50px) rotateX(95deg);
    -o-transform: translateZ(-50px) rotateX(95deg);
    transform: translateZ(-50px) rotateX(95deg);
}
.itemimg {
    display: block;
    position: absolute;
    top: 0;
    /*设置列表项图片的圆角和阴影效果*/
    border-radius: 3px;
    box-shadow: 0px 3px 8px rgba(0,0,0,0.3);
   -webkit-transform: translateZ(50px);
   -moz-transform: translateZ(50px);
   -ms-transform: translateZ(50px);
   -o-transform: translateZ(50px);
    transform: translateZ(50px);
   -webkit-transition: all .6s;
   -moz-transition: all .6s;
   -ms-transition: all .6s;
   -o-transition: all .6s;
    transition: all .6s;
    width: 310px; 
    height: 100px;
 }

.item .information {
    display: block;
    position: absolute;
    top: 0;
    height: 80px;
    width: 290px;
    text-align: left;
    border-radius: 15px;
    padding: 10px;
    font-size: 12px;
    text-shadow: 1px 1px1pxrgba(255,255,255,0.5);
    box-shadow: none;
    background: rgb(236,241,244);
    /*给底层显示文本的层级设置渐变效果*/
    background: -webkit-linear-gradient(to bottom,  rgba(236,241,244,1) 0%,rgba(190,202,217,1) 100%);
    background: -ms-linear-gradient(to bottom,  rgba(236,241,244,1) 0%,rgba(190,202,217,1) 100%);
    background: linear-gradient(to bottom,  rgba(236,241,244,1) 0%,rgba(190,202,217,1) 100%);
    -webkit-transform: rotateX(-90deg) translateZ(50px);
    -moz-transform: rotateX(-90deg) translateZ(50px);
    -ms-transform: rotateX(-90deg) translateZ(50px);
    -o-transform: rotateX(-90deg) translateZ(50px);
    transform: rotateX(-90deg) translateZ(50px);
    -webkit-transition: all .6s;
    -moz-transition: all .6s;
    -ms-transition: all .6s;
    -o-transition: all .6s;
    transition: all .6s;
 }

.information strong {
    display: block;
    margin: .2em 0 .5em 0;
    font-size: 20px;
    font-family: "Oleo Script";
  }
.item:hoverimg {
    /*列表项悬浮状态时，去掉图片的阴影效果*/
    box-shadow: none;
    border-radius: 15px;
}

.item:hover .information {
    box-shadow: 0px 3px 8px rgba(0,0,0,0.3);
    border-radius: 3px;
 }
</style>
<div id="container">
        <h1>CSS3 3D变形制作视频展示区</h1>
        <div class="wrapper">
            <div class="item">
                <img src="http://pic2.qiyipic.com/image/20140415/4e/32/5f/v_105669963_m_601_180_101.jpg" />
                <span class="information">
                    <strong>澳门风云</strong>闻名中外，曾担任美国赌场保安总顾问的魔术手石一坚，终回流澳门退休，更宴请各方朋友到来庆祝生日宴.
                </span>
            </div>
        </div>
    
        <div class="wrapper">
            <div class="item">
                <img src="http://pic4.qiyipic.com/image/20140417/b5/01/81/a_100003950_m_601_m2_180_101.jpg" />
                <span class="information">
                <strong>改过迁善</strong>该剧讲述了金明民饰演的律师在失忆后回顾自己以往的所作所为心生忏悔，为弥补自己犯下的错误而与自己曾经工作过的律师事务所对簿公堂的故事。
                </span>
            </div>
        </div>
        
        <div class="wrapper">
            <div class="item">
                <img src="http://pic1.qiyipic.com/common/lego/20140521/4515581d06cc4d5b8ab320da2cf3778d.jpg" />
                <span class="information">
                <strong>父子刑警</strong>本剧改编自雫井修介小说《Bitter Blood》。剧中，新晋刑警•佐原夏辉（佐藤健饰）被分配到银座警察
                </span>
            </div>
        </div>
        
        <div class="wrapper">
            <div class="item">
                <img src="http://pic5.qiyipic.com/image/20140319/7a/8d/4f/a_100003478_m_601_m1_180_101.jpg" />
                <span class="information">
                <strong>果宝特攻3</strong>果宝特攻3,顾名思义是果宝特攻的第二部续集,已在国家广播电影电视总局备案.暂定剧情为:菠萝吹雪偶然间穿越到了古代的水果世界
                </span>
            </div>
        </div>
        <div class="wrapper">
            <div class="item">
                <img src="http://pic0.qiyipic.com/image/20140517/ce/e8/42/v_106167752_m_601_180_101.jpg" />
                <span class="information">
                <strong>红眼</strong>1988年7月16日，从汉城始发的列车发生了一起严重的交通事故，造成100多人死亡。
                </span>
            </div>
        </div>
        <div class="wrapper">
            <div class="item">
                <img src="http://pic6.qiyipic.com/image/20140303/da/e9/aa/v_105073913_m_601_180_101.jpg" />
                <span class="information">
                <strong>熊出没之夺宝熊兵</strong>一场漆黑雨夜的意外事故，一段笑料十足的误打误撞，将两个外表相似却内容各异的箱子调换。
                </span>
            </div>
        </div>
    </div>
`
- 分页
`
ul.pagination {
    display: inline-block;
    padding: 0;
    margin: 0;
}

ul.pagination li {display: inline;}

ul.pagination li a {
    color: black;
    float: left;
    padding: 8px 16px;
    text-decoration: none;
}

ul.pagination li a.active {
    background-color: #4CAF50;
    color: white;
}

ul.pagination li a:hover:not(.active) {background-color: #ddd;}
//html
ul.pagination>li>a*8
//面包屑导航
ul.breadcrumb {
    padding: 8px 16px;
    list-style: none;
    background-color: #eee;
}
ul.breadcrumb li {display: inline;}
ul.breadcrumb li+li:before {
    padding: 8px;
    color: black;
    content: "/\00a0";
}
ul.breadcrumb li a {color: green;}
//html
ul>li>a*5
`
























