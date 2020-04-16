layout: '[layout]'
title: js图片上传预览功能
date: 2016-04-24 19:07:07
tags: [js]
categories: [js]
---

# 调用方法

```html
<div id="localImag">
    <img id="preview" src="images/jzxq.png" style="display: block; width: 350px;float:left;">
</div>
<input type="file" name="file" style="display: none;" id="doc" onchange="javascript:setImagePreview();$('.no-choose').empty();">
<a href="javascript:void(0)" onclick="$('#doc').click();" class="jzxqll">选择图片</a>
<span class="no-choose">未选择文件</span>
```

# 封装好的js方法

```js
//下面用于图片上传预览功能
function setImagePreview(avalue) {
    var docObj=document.getElementById("doc");

    var imgObjPreview=document.getElementById("preview");
    if(docObj.files &&docObj.files[0])
    {
        //火狐下，直接设img属性
        imgObjPreview.style.display = 'block';
        imgObjPreview.style.width = '350px';
        imgObjPreview.style.height = '';
        //imgObjPreview.src = docObj.files[0].getAsDataURL();

        //火狐7以上版本不能用上面的getAsDataURL()方式获取，需要一下方式
        imgObjPreview.src = window.URL.createObjectURL(docObj.files[0]);
    }
    else
    {
        //IE下，使用滤镜
        docObj.select();
        var imgSrc = document.selection.createRange().text;
        var localImagId = document.getElementById("localImag");
        //必须设置初始大小
        localImagId.style.width = "350px";
        localImagId.style.height = "";
        //图片异常的捕捉，防止用户修改后缀来伪造图片
        try{
            localImagId.style.filter="progid:DXImageTransform.Microsoft.AlphaImageLoader(sizingMethod=scale)";
            localImagId.filters.item("DXImageTransform.Microsoft.AlphaImageLoader").src = imgSrc;
        }
        catch(e)
        {
            alert("您上传的图片格式不正确，请重新选择!");
            return false;
        }
        imgObjPreview.style.display = 'none';
        document.selection.empty();
    }
    return true;
}
```