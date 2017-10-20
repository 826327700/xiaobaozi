---
title: Vue-element项目中支持flie协议打开编译后的文件
date: 2017-10-14 10:27:08
categories: "vue" 
tags: 
     - javascript
     - vue
---
##需求说明
当项目打包完成后，产生的static文件夹和index.html，假设我们想直接双击index.html通过flie协议运行在浏览器时，我们会发现页面引用的资源全部都会错误，这是由于资源路径引起的。我们来修改一下打包配置

##修改位置
1.config/index.js

![image.png](http://upload-images.jianshu.io/upload_images/6651371-cb982c6077fdae00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
由
```
assetsPublicPath: '/',
```
改成
```
assetsPublicPath: './',
```

2.build/utils.js

![image.png](http://upload-images.jianshu.io/upload_images/6651371-1dd15cc0c10ca45f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
由
```
return ExtractTextPlugin.extract({
        use: loaders,
        fallback: 'vue-style-loader'
 })
```
改成
```
return ExtractTextPlugin.extract({
        publicPath:'../../',
        use: loaders,
        fallback: 'vue-style-loader'
 })
```
保存，重新打包即可。