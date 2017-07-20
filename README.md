readme.md 语法
=========

##无序列表：
使用星号、加号、和减号来做为列表的项目标记:
星号：

* item 1
* item 2
* itme 3

加号：
+ item1
+ item2
+ item3

减号：
- item1
- item2
- item3

##有序列表：
一般使用数字接着一个英文句点作为项目标记：<br>
1. item1<br>
2. item2<br>
3. item3<br>


##链接
* 行内形式:<br>
直接在后面用括号直接接上链接：<br>
This is an [example link](http://example.com/).
* 参考形式:<br>
为链接定一个名称，之后你可以在文件的其他地方定义该链接的内容：
I get 10 times more traffic from [Google][1] than from
[Yahoo][2] or [MSN][3].

[1]: http://google.com/ "Google"
[2]: http://search.yahoo.com/ "Yahoo Search"
[3]: http://search.msn.com/ "MSN Search"
##添加图片：

行内形式（title 是选择性的）：
语法：
\!\[\]\(图片的地址\)
<br>
![alt text](https://github.com/sunalong/sources/blob/master/img/cat.png "Cat")

<br>
参考形式：
语法：
\!\[]\[varName\]<br>
\[varName\]:图片地址
<br>
![alt text][id]
[id]: https://github.com/sunalong/sources/blob/master/img/github.png "GitHub"

方法三：
语法：\<img src="图片地址" width="240"\>
<br>
<img src="http://f.cl.ly/items/0W2i0E151s0B3S3x3E3W/ogp_.jpg" width="240">
<br>其中width="240"可不用加的，效果如下：<br>
<img src="http://f.cl.ly/items/0W2i0E151s0B3S3x3E3W/ogp_.jpg">
