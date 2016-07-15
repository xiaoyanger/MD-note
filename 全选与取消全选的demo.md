layout: '[layout]'
title: 全选与取消全选的demo
date:  2016-05-15 19:19:55
tags: [js]
categories: [js]
---
---全选与取消全选的demo---
 
# demo 1
```html
<! DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" >
        <head >
            <title >中国站长天空-网页特效 -表单特效-checkbox和按钮实现的多选框全选功能 </title>
            <meta http -equiv= "content-type" content= "text/html;charset=gb2312" >
                <!--把下面代码加到<head >与</ head>之间 -->
                <script type= "text/javascript">
                    <!--
                    //说明：Javascript控制CheckBox的全选与取消全选
                    function checkAll(name)
                    {
                        var el = document.getElementsByTagName('input');
                        //取document中所有的input，比如文本输入框、按钮等元件，全都取出来存入数组，可以用el.length取数量，el[i]取内容
                        var len = el.length;
                        for(var i=0 ; i<len; i ++)
                        {
                            if((el[i].type== "checkbox") && (el[i].name==name))
                            {
                                el[i].checked = true;
                            }
                        }
                    }
                    function clearAll(name)
                    {
                        var el = document.getElementsByTagName('input');
                        var len = el.length;
                        for(var i=0 ; i<len; i ++)
                        {
                            if((el[i].type== "checkbox") && (el[i].name==name))
                            {
                                el[i].checked = false;
                            }
                        }
                    }
                    //-->
                </script >
            </head >
            <body >
                <!--把下面代码加到<body >与</ body>之间 -->
                <form>
                    < input type="checkbox" name="test" value="" onclick="if(this.checked==true) { checkAll('test'); } else { clearAll('test'); }" >字母全选开关
                    <input type ="checkbox" name ="test" value ="a"> a
                    <input type ="checkbox" name ="test" value ="b"> b
                    <input type ="checkbox" name ="test" value ="c"> c
                    <input type ="checkbox" name ="test" value ="d"> d
                    <input type ="checkbox" name ="test" value ="e"> e
                    <input type ="checkbox" name ="test" value ="f"> f
                    <input type ="checkbox" name ="test" value ="g"> g
                    <br >
                    < input type="checkbox" name="num" value="" onclick="if(this.checked==true) { checkAll('num'); } else { clearAll('num'); }" >数字全选开关
                    <input type ="checkbox" name ="num" value ="1"> 1
                    <input type ="checkbox" name ="num" value ="2"> 2
                    <input type ="checkbox" name ="num" value="3" >3
                    <br >
                    < input type="button" value="选择所有的字母" onclick="checkAll('test')" >
                    < input type="button" value="清空选中的字母" onclick="clearAll('test')" >
                    < br>
                    < input type="button" value="选择所有的数字" onclick="checkAll('num')" >
                    < input type="button" value="清空选中的数字" onclick="clearAll('num')" >
                </form>
            </body >
</html>
 ```
 
# demo2
 
 ```html
<!DOCTYPE html>
<html lang="en">
<head>
     <meta charset="UTF-8">
     <title>Document</title>
     <script>
     window.onload=function(){
          var CheckAll=document.getElementById('All');
          var UnCheck=document.getElementById('uncheck');
          var OtherCheck=document.getElementById('othercheck');
          var div=document.getElementById('div');
          var    CheckBox=div.getElementsByTagName('input');
          CheckAll.onclick=function(){
                  for(i=0;i<CheckBox.length;i++){
                          CheckBox[i].checked=true;
                      };
              };
          UnCheck.onclick=function(){
                  for(i=0;i<CheckBox.length;i++){
                          CheckBox[i].checked=false;
                      };
              };
          othercheck.onclick=function(){
                  for(i=0;i<CheckBox.length;i++){
                          if(CheckBox[i].checked==true){
                                  CheckBox[i].checked=false;
                              }
                          else{
                              CheckBox[i].checked=true
                              }
 
                      };
              };
     };
     </script>
</head>
<body>
     全选：<input type="button" id="All" value="全选" /><br />
不选<input type="button" id="uncheck" value="不选" /><br />
反选<input type="button" id="othercheck" value="反选" /><br />
<div id="div">
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
     <input type="checkbox" /><br/>
</div>
</body>
</html>
```