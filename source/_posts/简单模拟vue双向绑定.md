---
title: 简单模拟vue双向绑定
date: 2017-09-17 14:57:08
categories: "vue" 
tags: 
     - javascript
     - vue
---
#思路  
vue.js 是采用数据劫持结合发布者-订阅者模式的方式，通过Object.defineProperty()来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。

那么我们就使用Object.defineProperty()写个小demo，模拟vue的双向绑定效果，来借此学习一下Object.defineProperty()这个方法

#Object.defineProperty(obj, prop, descriptor)
#传入参数
第一个参数:目标对象
第二个参数:需要定义的属性或方法的名字。
第三个参数:目标属性所拥有的特性。（descriptor）
前两个参数不多说了，一看代码就懂，主要看第三个参数descriptor，看看有哪些取值

#descriptor
他又以下取值，我们简单认识一下，后面例子，挨个介绍，
value:属性的值(不用多说了)
writable:如果为false，属性的值就不能被重写,只能为只读了
configurable:总开关，一旦为false，就不能再设置他的（value，writable，configurable）
enumerable:是否能在for...in循环中遍历出来或在Object.keys中列举出来。
get:外部取obj的子属性prop的值时触发的回调
set:外部给obj的子属性prop赋值时触发的回调
注意：在 descriptor 中不能 同时设置访问器 (get 和 set) 和 wriable 或 value，否则会错，就是说想用(get 和 set)，就不能用（wriable 或 value中的任何一个）

#demo
我们利用set和get来简单模拟一下双向绑定

1.先定义个构造函数
```
function Vue (opt){
	var el=opt.el;
	var data=opt.data;
	var methods=opt.methods;
	var model={};
	var keys=Object.keys(data);
	for (let i=0;i<keys.length;i++) {
		Object.defineProperty(model, keys[i], {
			set: function(newValue) {
				data[keys[i]]=newValue
				change(keys[i])
			},
			get: function() {
				return data[keys[i]]
			},
			enumerable:true,
			configurable:true,
		})
	}
	var inputs=document.querySelector(el).querySelectorAll('input[v-model]')
	for (let i=0;i<inputs.length;i++) {
		inputs[i].onkeyup=function(){
			var v=this.getAttribute('v-model')
			model[v]=this.value
			change(v)
		}
	}
	
	var buttons=document.querySelector(el).querySelectorAll('*[v-click]')
	for (let i=0;i<buttons.length;i++) {
		buttons[i].onclick=function(){
			var func=this.getAttribute('v-click')
			methods[func]()
		}
	}
	
	function change(k){
		var els=document.querySelector(el).querySelectorAll('*[v-text="'+k+'"]')
		for (let j=0;j<els.length;j++) {
			els[j].innerHTML=data[k]
		}
		var inputs=document.querySelector(el).querySelectorAll('input[v-model="'+k+'"]')
		for (let j=0;j<inputs.length;j++) {
			inputs[j].value=data[k]
		}
	}

	return model
}
```
2.写一段简单的html
```
<div id="app">
	<input type="" name="" value="" v-model="obj"/>
	<p v-text="obj"></p>
	<input type="" name="" value="" v-model="aaa"/>
	<p v-text="aaa"></p>
	<button v-click="printObj">打印</button>
</div>
```
3.实例化vue方法
```
var vm=new Vue({
	el:'#app',
	data:{
		obj:'',
		aaa:'',
	},
	methods:{
		printObj:function(){
			alert(vm.obj)
		}
	}
})
```
如上所示，分别定义了obj和aaa两个字段和一个printObj方法，来看看效果：

![vue.gif](http://upload-images.jianshu.io/upload_images/6651371-8e61f666246af09b.gif?imageMogr2/auto-orient/strip)

好了，大功告成！当然vue远远比这个复杂的多，这里只是练习一下Object.defineProperty()这个方法。