---
title: Vue-quill-editor修改上传图片的方式
date: 2017-10-16 11:15:08
categories: "vue" 
tags: 
     - javascript
     - vue
---
Vue-quill-editor富文本编辑器默认在插入图片的时候将图片转为base64格式插入到富文本内容中，这样会导致富文本内容过长，post提交超出限制或时间过长。

##修改为选择图片后上传到服务器地址，返回服务器路径
1.在node_modules目录中找到vue-quill-editor目录下的src/editor.vue，
1.1、在props新增一个变量来储存服务上传地址

![image.png](http://upload-images.jianshu.io/upload_images/6651371-104cd54b4c538f73.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1.2、在initialize方法中新增一段自定义的image事件：
```
var toolbar = self.quill.getModule('toolbar');
toolbar.addHandler('image', function() {
	var fileInput = this.container.querySelector('input.ql-image[type=file]');
	if(fileInput == null) {
		fileInput = document.createElement('input');
		fileInput.setAttribute('type', 'file');
		fileInput.setAttribute('accept', 'image/png, image/gif, image/jpeg, image/bmp, image/x-icon');
		fileInput.classList.add('ql-image');
		fileInput.addEventListener('change', function() {
			if(fileInput.files != null && fileInput.files[0] != null) {
				if(self.uploadUrl) {
					var file = fileInput.files[0]
					var fd = new FormData()
					fd.append('editor', 'true')
					fd.append(file.name, file)
					var request = new XMLHttpRequest();
					request.open("POST", self.uploadUrl, true);
					request.onreadystatechange = function() {
						if(request.readyState === 4 && request.status === 200) {
							var res = JSON.parse(request.responseText)
							var range = self.quill.getSelection(true);
							self.quill.insertEmbed(range.index, 'image', res.fullUrl);//这里的res.fullUrl为后端返回的字段，可根据项目自行修改
							self.quill.setSelection(range.index + 1, 0)
							fileInput.value = "";
						} else if(request.readyState === 4) {
							console.error(request)
						}
					}
					request.send(fd);
				} else {
					var reader = new FileReader();
					reader.onload = function(e) {
						var range = self.quill.getSelection(true);
						self.quill.insertEmbed(range.index, 'image', e.target.result);
						self.quill.setSelection(range.index + 1, 0)
						fileInput.value = "";
					};
					reader.readAsDataURL(fileInput.files[0]);
				}
			}
		});
		this.container.appendChild(fileInput);
	}
	fileInput.click();
});
```
2.使用时填写服务器上传地址

![image.png](http://upload-images.jianshu.io/upload_images/6651371-d425afc364f8ba4e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如不填，则使用base64格式插入图片

3.重新打包项目即可