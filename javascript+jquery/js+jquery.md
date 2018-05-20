# js + jquery
1. 拼接html class 属性点击事件无法起作用，因为$dom已经在页面初始化时加载，无法获取新拼接的class，但是id是存在的。（**错误**）当时造成这种错误的原因是因为js的加载顺序。
	- 在拼接完js之后才能获取相应的id或者class。
	- 拼接时元素已经加载到dom中了。
	- 经常拼接会影响效率。
2. 判断class是否存在：`$(selector).hasClass(class)`
3. 判断某id是否存在：`$(selector).length>0`
4. js判断对象是否相等，用`===`,使用`==`会造成类型转换
5. 不使用选择器`selector`，则默认的this不是指当前元素。
6. ie8、9使用ajax进行异步提交：
	- 引入[jquery.form.js](http://jquery.malsup.com/form/)
	- 使用form对象调用ajaxSubmit
```
$(document).ready(function() {
  var options = {
    beforeSend: function() {
     
    },
    uploadProgress: function(event, position, total, percentComplete) {
      
    },
    success: function() {
      
    },
    complete: function(response) {
      
    },
    error: function() {
      
    }
  };
  $("#myForm").ajaxForm(options);
});
```

7. `$(selector).attr(attribute)`获取被选元素的属性值
8. a标签的`href='#'`会被认为是一个标签，并且从页面中寻找标签并跳转，若没有发现该标签则调到页首。`###`是一个`#`和`##`的组合，页面中找不到`##`的标签就不会发生跳转，也不会发生跳到页首的情况。写成`javascript:void(0)`也可以避免。
9. jquery添加css
```
$(selector).css(name,value)
```
10. jquery添加class
```
$(selector).addClass("className")
```
若有相同元素，新追加的class优先级高
11. jquery获取select指定name的值
```
// 获取value
var value = $('select[name=""]').val();
// 获取text
var text =  $('select[name=""] option:selected').text();
```
12. jquery获取多个相同name的select值
```
var value=$('select[name=""]').map(function(){return this.value}).get().join();
```

13. jquery遍历name相同的input
```
$('input[name=""]').each(function(){
	alert($(this).val());
});
```