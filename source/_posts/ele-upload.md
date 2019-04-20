---
title: element上传图片el-upload
date: 2019-04-09 22:12:12
tags: ['JavaScript']
categories: JavaScript
---

### element上传图片el-upload
<!-- more -->
### el-upload组件
```
<el-upload   
    action=""                                             
    list-type="picture-card"
    :on-change="uploadChange"
    :file-list="fileList"
    :limit="5"                          
    :auto-upload="false">                           
    <i class="el-icon-plus"></i>
</el-upload>
```
list-type:文件上传类型，如照片墙、单个上传照片
on-change:自定义上传图片
file-list:照片墙的数据
limit: 最大允许上传个数
auto-upload:el-upload 会自动上传两次，false不自动上传

### 获取base64
```
uploadChange(file,fileList) {
    var reader = new FileReader();
    reader.readAsDataURL(file.raw);
    reader.onload = (e) =>{ 
        //reader.result获取base64
        let imgStr = reader.result
    }  
}
```
