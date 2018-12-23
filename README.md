---
title: 原生js实现网页图片点击展示效果
date: 2018-12-23 11:09:16
updated: 2018-12-23 11:09:16
---

之前博客用过一些第三方的图片展示插件，比如`fancybox`，但是用久了感觉还是有点问题，反正自己用着不舒服，而且这些第三方插件大都使用jQuery，就想自己写个原生就是实现的图片展示。

网上看了一些代码，实现起来还是很简单的，大概思路就在每个图片的点击事件中添加图层与图片副本。

实现效果如下

![兰州小红鸡](https://image.idealli.com/blog/18122301.jpg)

在录个动图，感谢可爱女朋友的出境

![兰州小红鸡](https://image.idealli.com/blog/18122302-min.gif)

也可以在我的相册里看

<a target="_blank" href="https://me.idealli.com/photos/index.html" class="LinkCard">
小鸡的相册
</a>

具体的实现代码就一点点了，100多行原生js
就不细讲了，直接贴出来吧
**如何使用**

在页面引入js
```javascript
<script type="text/javascript" src="https://image.idealli.com/photoshow.js"></script>
```
下面是代码

```javascript
let container = document.documentElement||document.body;
let img,div,src,btnleft,btnright;
var imgid=0;
let x,y,w,h,tx,ty,tw,th,ww,wh;
let closeMove=function(){
    if(div==undefined){
        return false;
    }
    div.style.opacity=0;
    img.style.height=h+"px";
    img.style.width=w+"px";
    img.style.left=x+"px";
    img.style.top=(y - container.scrollTop)+"px";
    // 延迟移除dom
    setTimeout(function(){
        div.remove();
        img.remove();
        btnright.remove();
        btnleft.remove();
    },100);

};

let closeFade=function(){
    if(div==undefined){
        return false;
    }
    div.style.opacity=0;
    img.style.opacity=0;
    // 延迟移除dom
    setTimeout(function(){
        div.remove();
        img.remove();
        btnright.remove();
        btnleft.remove();
    },100);
};

let style=function() {
btnleft.style.cssText=`
    position:fixed;
    border-radius: 50%;;
    left:${x - 20}px;
    top:${y - container.scrollTop + h/2}px;
    width:50px;
    height:50px;
    border: 0px;
    background-color: rgba(200,200,200,0.8);
    font-size: 20px;
    z-index: 999999999;
    transition:all .3s cubic-bezier(0.165, 0.84, 0.44, 1);
`;
btnright.style.cssText=`
    position:fixed;
    border-radius: 50%;
    left:${x + w + 20}px;
    top:${y - container.scrollTop + h/2}px;
    width:50px;
    border: 0px;
    height:50px;
    font-size: 20px;
    background-color: rgba(200,200,200,0.8);
    z-index: 999999999;
    transition:all .3s cubic-bezier(0.165, 0.84, 0.44, 1);
`;
btnleft.innerText="<";
btnright.innerText=">";

img.style.cssText=`
    position:fixed;
    border-radius: 12px;
    left:${x}px;
    top:${y - container.scrollTop}px;
    width:${w}px;
    height:${h}px;
    z-index: 999999999;
    transition:all .3s cubic-bezier(0.165, 0.84, 0.44, 1);
    opacity:0;
`;
}

// 监听滚动关闭层
document.addEventListener("scroll",function(){
    closeFade();
});
document.querySelectorAll("img").forEach(v=>{

	if (v.parentNode.localName!='a') {
		v.id=imgid;
		imgid++;
		    v.addEventListener("click",function(e){ // 注册事件
	        // 记录小图的位置个大小
	        x=e.target.offsetLeft;
	        y=e.target.offsetTop;
	        w=e.target.offsetWidth;
	        h=e.target.offsetHeight;
	        src=e.target.src;
	        id=e.target.id;
	        // 创建遮罩层
	        div=document.createElement("div");
	        div.style.cssText=`
	            position:fixed;
	            left:0;
	            top:0;
	            bottom:0;
	            right:0;
	            background-color: rgba(25,25,25,0.8);
	            z-index:99999999;
	            transition:all .3s cubic-bezier(0.165, 0.84, 0.44, 1);
	        `;
	        document.body.appendChild(div);
	        setTimeout(function(){
	            div.style.opacity=1;
	        },0);
	        // (此处可以加loading)

	        // 创建副本
	        img=new Image();
	        btnright=document.createElement("button");
	        btnleft=document.createElement("button");
	        img.src=src;
	        style();


	        btnleft.onclick=function(){
	        	if(id===0){
	        		alert("已经是第一张了！");
	        		return;
	        	}
	        	var left=document.getElementById(id-1);
	        	img.src=left.src;
	        	x=left.offsetLeft;
	        	y=left.offsetTop;
	      		w=left.offsetWidth;
	        	h=left.offsetHeight;
	        	style();
	        	id--;
	        }
	        btnright.onclick=function(){
	        	id++;
	        	if(id>=imgid){
	        		alert("已经是最后一张了！");
	        		return;
	        	}
	        	var right=document.getElementById(id);
	        	img.src=right.src;
	        	x=right.offsetLeft;
	        	y=right.offsetTop;
	      		w=right.offsetWidth;
	        	h=right.offsetHeight;
	        	style();
	        	
	        }
	        img.onload=function(){
	            document.body.appendChild(img);
	            document.body.appendChild(btnright);
	            document.body.appendChild(btnleft);

	            // 浏览器宽高
	            wh=window.innerHeight;
	            ww=window.innerWidth;

	            // 目标宽高和坐标
	            if(w/h<ww/wh){
	            	th=wh-80;
		            tw=w/h*th >> 0;
		            tx=(ww - tw) / 2;
		            ty=40;	            	
	            }
	            else{
	            	tw=ww*0.8;
	            	th=h/w*tw >> 0;
	            	tx=ww*0.1;
	            	ty=(wh-th)/2;
	            }

	            // 延迟写入否则不会有动画
	            setTimeout(function(){
	                img.style.opacity=1;
	                img.style.height=th+"px";
	                img.style.width=tw+"px";
	                img.style.left=tx+"px";
	                img.style.top=ty+"px";
	                btnleft.style.left=(tx-90)+"px";
	                btnleft.style.top=(ty+th/2)+"px";
	                btnright.style.left=(tx+tw+40)+"px";
	                btnright.style.top=(ty+th/2)+"px";
	                // 点击隐藏
	                div.onclick=img.onclick=closeMove;
	            },10);
	        };
	    });//end event
	}
});//end forEach
```

