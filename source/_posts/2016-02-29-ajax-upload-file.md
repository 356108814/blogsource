title: Ajax上传文件
categories: 前端
tags:
  - ajax
date: 2016-02-29 20:34:42
---
#### FormData
> XMLHttpRequest Level 2添加了一个新的接口FormData.利用FormData对象,我们可以通过JavaScript用一些键值对来模拟一系列表单控件,我们还可以使用XMLHttpRequest的send()方法来异步的提交这个"表单".比起普通的ajax,使用FormData的最大优点就是我们可以异步上传一个二进制文件.
构造函数
new FormData (optional HTMLFormElement form)
参数：form
(可选) 一个HTML表单元素,可以包含任何形式的表单控件,包括文件输入框.

#### 示例
    <form id="save-form">
        <input type="file" id="uploadFile">
    </form>
##### js上传
	var formData = new FormData();
	//获取文件对象
	var uploadFile = document.getElementById("uploadFile").files[0];
	formData.append("uploadFile", uploadFile);
	xhr = new XMLHttpRequest();
	xhr.open("POST", "/upload");
	xhr.send(formData);

##### jquery上传
	var file = $("#uploadFile").files[0]; //选择上传的文件
	var formData = new FormData();
	formData.append("excelFile", file);
	$.ajax({  
	    url : '/upload',  
	    type : 'POST',  
	    data : formData,  
	    /**   
	     * 必须false才会避开jQuery对 formdata 的默认处理   
	     * XMLHttpRequest会对 formdata 进行正确的处理   
	     */  
	    processData : false,  
	    /**   
	     *必须false才会自动加上正确的Content-Type   
	     */  
	    contentType : false,  
	    success : function(data) {
	        console.log(data);
	        $('#mids').val(data);
	        applySearch();
	    }
	});
