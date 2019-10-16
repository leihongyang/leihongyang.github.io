# Markdown标记语法

# 标题
```
 # H1
 ## H2
 ### H3
 #### H4
 ##### H5
 ###### H6

 最常使用的是 H1 和 H2 标题，还有更明显的另一种写法：

 Alt-H1
 ======

 Alt-H2
 ------
 ```
此处-是负号，不是_下横号，_下横号，显示的是双横线
# H1
## H2
### H3
#### H4
##### H5
###### H6

最常使用的是 H1 和 H2 标题，还有更明显的另一种写法：

Alt-H1
======

Alt-H2
------

# 强调语法
```
 强调，例如意大利斜体，可以使用 *asterisks* 或 _underscores_。  
 加重语气强调，例如粗体，可以用 **asterisks** 或 __underscores__ 。
 可以混用这两种 **asterisks and _underscores_**。
 给文字加上删除线，是这样 ~~Scratch this.~~
```

# 清单
下面的示例为了清楚展示缩排时需要的空格，使用了点号，实际撰写时只需要相同数量的空格。
```
 1. 第一个有序列表项
 2. 另一个项
 ..* 无序的子列表
 1. 数字本身是否排序并不重要，统统使用相同的数字也可以
 ..1. 有序的子列表
 4. 另一项

 ...可以在一则项目中使用缩进的段落格式。注意上面的**空行**，还有本段前的**空格**(至少一个，我们使用了三个，展示的更清楚)

 ...在一个段落中**强制换行**，在语句后方加入两个**空格**。..
 ...这个被强制设置的独立行，依旧在同一个段落中。..
 ...（有些人觉得使用空格强制换行太麻烦，例如 GFM 就不需要）。

 * 可以使用星号建立无序目录
 - 或是短横线（负号）
 + 使用半形加号也可以
```
# 连接设置
有两种方式可以建立文中的链接。
```
 [这是一个行内链接](https://www.gitbook.com)  

 [这是一个带有标题的行内链接](https://www.gitbook.com "GitBook's Homepage")  

 [这是一个参考链接][Arbitrary
 case-insensitive reference text]  

 [这是一个对应到 Git 仓库文件的相对参考链接](../blob/master/LICENSE)  

 [参考标的物也可以使用数字][1]  

 直接使用文字对应也可以 [这段文字链接到参考项目]

 参考项目可以写在文档的最后，有点像书内的注解（注脚）。

 [arbitrary case-insensitive reference text]: https://www.mozilla.org
 [1]: http://slashdot.org
 [这段文件链接到参考项目]: http://www.reddit.com
```
# 图片
```
 这是我们的 logo （将鼠标移动到图片上会显示图片标题）：  
 行内格式：  
 ![alt text](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo 标题文字示例一")

 参考链接结构：
 ![alt text][logo]

 [logo]: https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo 标题文字示例二"
```
# 程序代码与语法高亮标示
```
 行内 `code` 必须使用 `back-ticks` 将文字包起来(一般在键盘左上方的地一个键)。
```

整段独立展示的代码必须使用成对的三个 back-ticks ``` 包裹起来，或是使用四个空格缩排。建议使用第一种方法，可以让代码具有可读性。
# 引言
```
 > 引言(Blockquotes)常常出现在电子邮件中，表示摘录来信的原句。
 > 这一行是引言的一部分。

 Quote break.

 > 这是一段非常长的引言区块，只要在句首使用了正确的符号和空格，就可以持续不间断的撰写，整段文字都是会被包含在引言区块中。当然你依旧可以在引言区块中 *使用* **Markdown** 的行内格式标记语法。
```
# 水平分割线
```
 三个或三个以上的符号，必须在独立的一行，前后不能有其他文字。

 ---
 短横线（Hyphens）

 ***
 半形星号（Asterisks）

 ___
 下底/横线（Underscores）
```
# 空行分隔段落
知道Markdown如何进行分段是很重要的，基本上空行代表前后的文字都会是段落（在HTML中以```<p>```与```</p>```包裹起来）。如果使用桌面编辑软件，有些可以微调空行的显示，让编辑区看起来不那么松散；甚至有些软件直接取消分行的设置，按下 return 就代表分段了。
最好的方法就是实践，打开编辑器，启用预览窗口，试着输入几次，看看有什么结果，很快就熟悉了。
尝试输入下面的内容看看：
```
 Here's a line for us to start with.

 This line is separated from the one above by two newlines, so it will be a *separate paragraph*.

 This line is also a separate paragraph, but ...
 This line is only separated by a single newline, so it's a separate line in the *same paragraph*.
```
(注意：Markdown 套用了 GFM 的分行模式，因此强制断行并不需要在行后加入两个空格。)
# 影片
虽然无法直接嵌影片，但可以采用图片链接的模式：
```
 <a href="http://www.youtube.com/watch?feature=player_embedded&v=YOUTUBE_VIDEO_ID_HERE
 " target="_blank"><img src="http://img.youtube.com/vi/YOUTUBE_VIDEO_ID_HERE/0.jpg"
 alt="IMAGE ALT TEXT HERE" width="240" height="180" border="10" /></a>
```
也可以直接在 Markdown 这样写，但会失去尺寸和边界的设定：
```
 [![IMAGE ALT TEXT HERE](http://img.youtube.com/vi/YOUTUBE_VIDEO_ID_HERE/0.jpg)](http://www.youtube.com/watch?v=YOUTUBE_VIDED_ID_HERE)
```
