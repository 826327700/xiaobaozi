---
title: vue-cli项目中使用autoprefixer
date: 2017-09-20 15:32:08
categories: "vue" 
tags: 
     - javascript
     - vue
---
##修改前
build/utils.js文件 50行

![QQ截图20170920144715.png](http://upload-images.jianshu.io/upload_images/6651371-e3710585a31cf971.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

改成：
```
postcss: [require('autoprefixer')({ browsers: ['last 10 Chrome versions', 'last 5 Firefox versions', 'Safari >= 6', 'ie > 8'] })],
```
##修改后

![QQ图片20170920144844.png](http://upload-images.jianshu.io/upload_images/6651371-14fd6c17b03a0ba9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)