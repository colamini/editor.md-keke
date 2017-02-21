# 使用editor.md作为后台富文本编辑器以及前台Markdown文档展示

标签（空格分隔）： editor.md 富文本编辑器

---
[toc]

##写在前面
当后台需要插入富文本编辑器的时候，editor.md是一个不错的选择，它是一款开源的md编辑器，编写简单，风格简洁美观，非常适合编写技术文档。
##整个流程
- 首先需要在后台管理页插入富文本编辑器
- 然后拿到编辑好的html代码并把它存入数据库，为了在前台展示时加载页面。同时还应拿到对应md源代码（也存入数据库），为了让文章在必要的时候可以重新编辑修改。
- 前端拿到html代码后，引入editor.md相应的css和js文件，对页面进行正确的加载。 

##1.下载安装
- 在github上把项目clone下来
https://github.com/pandao/editor.md
- 或者在官网上直接下载安装
https://pandao.github.io/editor.md/

##2.基本研究思路
**不感兴趣的可以直接跳到实战部分**

a.打开项目文件件下面的examples文件夹中的full.html或者use-zepto文件
</br></br>

![此处输入图片的描述][1]
![此处输入图片的描述][2]

可以看到需要引入的css和js文件如下：
```html
        <link rel="stylesheet" href="css/style.css" />
        <link rel="stylesheet" href="../css/editormd.css" />   
   
        <script src="js/jquery.min.js"></script>
        <script src="../editormd.js"></script>   
```

editor.md的基本配置项以及可以使用的函数：
```html
        
        <script type="text/javascript">
            var testEditor;
            
            $(function() {
                
                $.get('test.md', function(md){
                    testEditor = editormd("test-editormd", {
                        width: "90%",
                        height: 740,
                        path : '../lib/',
                        theme : "dark",
                        previewTheme : "dark",
                        editorTheme : "pastel-on-dark",
                        markdown : md,
                        codeFold : true,
                        //syncScrolling : false,
                        saveHTMLToTextarea : true,    // 保存 HTML 到 Textarea
                        searchReplace : true,
                        //watch : false,                // 关闭实时预览
                        htmlDecode : "style,script,iframe|on*",            // 开启 HTML 标签解析，为了安全性，默认不开启    
                        //toolbar  : false,             //关闭工具栏
                        //previewCodeHighlight : false, // 关闭预览 HTML 的代码块高亮，默认开启
                        emoji : true,
                        taskList : true,
                        tocm            : true,         // Using [TOCM]
                        tex : true,                   // 开启科学公式TeX语言支持，默认关闭
                        flowChart : true,             // 开启流程图支持，默认关闭
                        sequenceDiagram : true,       // 开启时序/序列图支持，默认关闭,
                        //dialogLockScreen : false,   // 设置弹出层对话框不锁屏，全局通用，默认为true
                        //dialogShowMask : false,     // 设置弹出层对话框显示透明遮罩层，全局通用，默认为true
                        //dialogDraggable : false,    // 设置弹出层对话框不可拖动，全局通用，默认为true
                        //dialogMaskOpacity : 0.4,    // 设置透明遮罩层的透明度，全局通用，默认值为0.1
                        //dialogMaskBgColor : "#000", // 设置透明遮罩层的背景颜色，全局通用，默认为#fff
                        imageUpload : true,
                        imageFormats : ["jpg", "jpeg", "gif", "png", "bmp", "webp"],
                        imageUploadURL : "./php/upload.php",
                        onload : function() {
                            console.log('onload', this);
                            //this.fullscreen();
                            //this.unwatch();
                            //this.watch().fullscreen();

                            //this.setMarkdown("#PHP");
                            //this.width("100%");
                            //this.height(480);
                            //this.resize("100%", 640);
                        }
                    });
                });
                
                $("#goto-line-btn").bind("click", function(){
                    testEditor.gotoLine(90);
                });
                
                $("#show-btn").bind('click', function(){
                    testEditor.show();
                });
                
                $("#hide-btn").bind('click', function(){
                    testEditor.hide();
                });
                
                $("#get-md-btn").bind('click', function(){
                    alert(testEditor.getMarkdown());
                });
                
                $("#get-html-btn").bind('click', function() {
                    alert(testEditor.getHTML());
                });                
                
                $("#watch-btn").bind('click', function() {
                    testEditor.watch();
                });                 
                
                $("#unwatch-btn").bind('click', function() {
                    testEditor.unwatch();
                });              
                
                $("#preview-btn").bind('click', function() {
                    testEditor.previewing();
                });
                
                $("#fullscreen-btn").bind('click', function() {
                    testEditor.fullscreen();
                });
                
                $("#show-toolbar-btn").bind('click', function() {
                    testEditor.showToolbar();
                });
                
                $("#close-toolbar-btn").bind('click', function() {
                    testEditor.hideToolbar();
                });
                
                $("#toc-menu-btn").click(function(){
                    testEditor.config({
                        tocDropdown   : true,
                        tocTitle      : "目录 Table of Contents",
                    });
                });
                
                $("#toc-default-btn").click(function() {
                    testEditor.config("tocDropdown", false);
                });
            });
        </script>
```
对于配置项只说明一点，就是主题theme的设置:

打开同一目录下的theme.html（用浏览器打开），这时候你可以看到不同主题的呈现效果，选定你想要的主题，对配置项进行相应的修改。

例如我修改的配置项如下：

```javascript
 theme : "default",
 previewTheme : "default",
 editorTheme : "base16-light",
```

对于函数，只有两个函数与我们的需求相关，那就是getHtml()还有getMarkDown()函数，这两个函数是否符合我们的需求的，让我们接下来再看：

b.用浏览器打开use-zepto.html文件（由于full.html在非php环境中打开会出错，原因在于 `$.get('test.md', function(md){});`

在左边的编辑器里面编辑：(注意：要有TOC还有代码高亮区！）
![此处输入图片的描述][3]

然后点击`Get Html`按钮，仔细观察alert内容（建议把alert改为console.log，方便对比）

打开开发者工具，定位到preview区
![此处输入图片的描述][4]

然后发现，你想要的html代码跟getHtml()得到的代码不一样！！！

**那么关键点就在于获取容器editormd-preview-container里面的html代码了。**

##3.开始实战

- 清空example文件夹下的所有html文件,并建立两个html文件，admin.html是后台管理页，user.html用于前台展示页

![此处输入图片的描述][5]

- 两个文件分别引入相关的css和js文件
```html
 <link rel="stylesheet" href="css/style.css" />
 <link rel="stylesheet" href="../css/editormd.css" />
 <script src="js/zepto.min.js"></script>
 <script src="../editormd.js"></script>
```

- admin.html
html结构
```html
<div id="layout">

        <div class="editormd" id="test-editormd">   
            <textarea></textarea>             

        </div>
        
        <br/><input id="submit" type="button" name="submit" value="Submit" class="btn" style="margin-left: 5%;" />
        <button id="preview-btn">Preview HTML (Press Shift + ESC cancel)</button>
    </div>     
```

 js部分
新建一个editormd对象：
```javascript
testEditor = editormd();
```
  editormd的配置项如下：
```javascript
 testEditor = editormd("test-editormd", {
                width: "90%",
                height: 740,
                path : '../lib/',
                theme : "default",
                previewTheme : "default",
                editorTheme : "base16-light",
                codeFold : true,
                        //syncScrolling : false,
                        saveHTMLToTextarea : true,    // 保存 HTML 到 Textarea
                        searchReplace : true,
                        //watch : false,                // 关闭实时预览
                        htmlDecode : "style,script,iframe|on*",            // 开启 HTML 标签解析，为了安全性，默认不开启    
                        //toolbar  : false,             //关闭工具栏
                        //previewCodeHighlight : false, // 关闭预览 HTML 的代码块高亮，默认开启
                        emoji : true,
                        taskList : true,
                        tocm            : true,         // Using [TOCM]
                        tex : true,                   // 开启科学公式TeX语言支持，默认关闭
                        flowChart : true,             // 开启流程图支持，默认关闭
                        sequenceDiagram : true,       // 开启时序/序列图支持，默认关闭,
                        //dialogLockScreen : false,   // 设置弹出层对话框不锁屏，全局通用，默认为true
                        //dialogShowMask : false,     // 设置弹出层对话框显示透明遮罩层，全局通用，默认为true
                        //dialogDraggable : false,    // 设置弹出层对话框不可拖动，全局通用，默认为true
                        //dialogMaskOpacity : 0.4,    // 设置透明遮罩层的透明度，全局通用，默认值为0.1
                        //dialogMaskBgColor : "#000", // 设置透明遮罩层的背景颜色，全局通用，默认为#fff
                        imageUpload : true,
                        imageFormats : ["jpg", "jpeg", "gif", "png", "bmp", "webp"],
                        imageUploadURL : "./php/upload.php",
                        onload : function() {
                            console.log('onload', this);
                            
                            //this.fullscreen();
                            //this.unwatch();
                            //this.watch().fullscreen();

                            //this.setMarkdown("#PHP");
                            //this.width("100%");
                            //this.height(480);
                            //this.resize("100%", 640);
                        }
                    });
```

 submit函数用于获取html代码和markdown源代码：
```javascript
  document.getElementById("submit").onclick = function(){
                let htmlCode = document.getElementsByClassName("editormd-preview-container")[0].innerHTML;
                let mdCode = testEditor.getMarkdown();
                console.log(htmlCode);
            }

```
 模拟用户从数据库拿到md源代码，可以修改文章:
 
```javascript
  document.getElementsByTagName('textarea')[0].innerHTML = "##HELLO keke";
```
 
- user.html
html结构
```html
<div class="markdown-body editormd-preview-container"></div>
```
模拟从后台获取数据

编辑后台管理（admin.html)md编辑器，按`submit`按钮，把控制台打印出来的代码贴到（user.html)markdowm-body标签里面：
```html
<div class="markdown-body editormd-preview-container">

     <h2 id="h2-kek"><a name="kek" class="reference-link"></a><span class="header-link octicon octicon-link"></span>kek</h2><p>Wad</p>
     <div class="markdown-toc editormd-markdown-toc"><ul class="markdown-toc-list"><li><a class="toc-level-2" href="#kek" level="2">kek</a><ul></ul></li></ul></div><pre class="prettyprint linenums prettyprinted"><ol class="linenums"><li class="L0"><code class="lang-javascript"><span class="kwd">var</span><span class="pln"> i </span><span class="pun">=</span><span class="pln"> </span><span class="lit">0</span><span class="pun">;</span></code></li></ol></pre>
     

</div> 
```

浏览器打开，成功！

![此处输入图片的描述][6]


源代码地址：https://github.com/kexinWeb/editor.md-keke.git


  [1]: http://www.kexinweb.com/images/node-blog/4.png
  [2]: http://www.kexinweb.com/images/node-blog/5.png
  [3]: http://www.kexinweb.com/images/node-blog/7.png
  [4]: http://www.kexinweb.com/images/node-blog/9.png
  [5]: http://www.kexinweb.com/images/node-blog/10.png
  [6]: http://www.kexinweb.com/images/node-blog/11.png
