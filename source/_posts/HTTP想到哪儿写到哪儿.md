---
title: HTTP想到哪儿写到哪儿
date: 2019-03-29 15:34:54
tags:-HTTP
---
1.Content-Type:表示的媒体类型信息:
* text/html ： HTML格式
* text/plain ：纯文本格式      
* text/xml ：  XML格式
* image/gif ：gif图片格式    
* image/jpeg ：jpg图片格式 
* image/png：png图片格式
* application/xhtml+xml ：XHTML格式
* application/xml     ： XML数据格式
* application/atom+xml  ：Atom XML聚合格式    
* application/json    ： JSON数据格式
* application/pdf       ：pdf格式  
* application/msword  ： Word文档格式
* application/octet-stream ： 二进制流数据（如常见的文件下载）
* application/x-www-form-urlencoded ：key/value格式发送到服务器
* multipart/form-data ： 需要在表单中进行文件上传时，就需要使用该格式

application/x-www-form-urlencoded和multipart/form-data的区别:

	前者在处理较大数据时效率比较低,适合用于较小的表单传输

# 2.HTTP请求报头:主要由请求行,请求头,请求体组成	
# 请求行:包含一些主要信息,请求方法 请求地址,和HTTP版本和协议
