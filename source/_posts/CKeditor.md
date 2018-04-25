---
title: CKeditor在VS2017下关于.net.MVC的配置问题
date: 2018-04-24 10:48:42
tags:
  - ASP.NET
  - MVC
  - CKeditor
  - CKFinder
---

# 前言

关于富文本编辑器，在很多开发者看来，富文本编辑器的编写是一件很神秘或者复杂的事情。神秘倒没有，复杂的话，确实如此。但是它的基本原理并不复杂，入门也不难。今天我们的主题是讲述CKeditor在VS2017下关于.NET.MVC的配置问题。

首先让我们看下CKeditor在浏览器中的效果图：

![效果图](https://github.com/MINDOU520/Images/raw/master/qianyan.jpg)

我们可以看到，这是一个具有强大功能，多种工具的编辑器。

# 添加方法

如果我们要在VS种添加CKeditor，有两种方法：

第一：直接在[CKeditor官网](https://docs.ckeditor.com/)进行下载,根据你自己的需要选择版本下载，本期所讲的是4.0版本的。
我们下载下来后，解压之后把文件夹添加到你项目中要使用的UI界面的根目录下。但是在这个文件中，有许多是我们用不上的，我们可以选择删除：

* _sample(示例文件夹)
* _source（未压缩源程序）
* 以及lang文件夹下除 zh-cn.js、en.js 以外的文件（也可以根据需要保留其他语言文件）

第二：直接在VS中进行配置。在VS中的NuGet包管理器中下载。
![在VS中进行配置](https://github.com/MINDOU520/Images/raw/master/NuGet.png)
配置完成后，CKeditor的文件夹会出现在Scripts文件夹中。

# 使用方法

1、在视图页面引入ckeditor核心文件ckeditor.js

```
<script type="text/javas cript" src="ckeditor/ckeditor.js"></script>
<script src="~/Scripts/ckeditor/config.js"></script>
```

2、在使用编辑器的地方插入HTML控件textarea

```
<textarea id="TextArea1" cols="20" rows="2" class="ckeditor"></textarea>
```

如果是ASP.NET环境，也可用服务器端控件TextBox

```
<asp:TextBox ID="tbContent" runat="server" TextMode="MultiLine" class="ckeditor"></asp:TextBox>
```

注意在控件中加上 class="ckeditor" 。

3、将相应的控件替换成编辑器代码


```
<script type="text/javascript">
CKEDITOR.replace('TextArea1');
//如果是在ASP.NET环境下用的服务器端控件<TextBox>
CKEDITOR.replace('tbContent');
//如果<TextBox>控件在母版页中，要这样写
CKEDITOR.replace('<%=tbContent.ClientID.Replace("_","$") %>');
</script>
```
但是需要注意的是由于textarea中有特殊字符，出于安全原因，默认情况mvc框架不允许提交的，应在相应方法上加上[ValidateInput(false)]属性。否则会出现如下错误：

![错误](https://github.com/MINDOU520/Images/raw/master/bug.png)

到这里我们就差不多配置完成了，这里有[全部配置参数](https://github.com/MINDOU520/Images/raw/master/CKeditor)，可以自行添加。

但是，在数据保存后，数据的显示会出现具有HTML标签的问题，如图：

![数据带有HTML标签](https://github.com/MINDOU520/Images/raw/master/html.png)

我们只需要在你要显示数据的地方用@Html.Raw(item.tbContent)显示就行了。

但是我们会发现，现在配置好的CKeditor并没有上传功能的，如图的图片上传：

![图片上传](https://github.com/MINDOU520/Images/raw/master/noup.png)

因为CKeditor是没有上传功能的，这是我们就需要引用CKFinder.

# CKFinder
关于CkFinder，我们也是在[CKFinder官网](https://docs.ckeditor.com/)进行下载。
解压之后江文件夹放在根目录下，与CKeditor在同一文件夹，同时把文件夹中的_sample、_source文件夹删除，理由同上。

<font color=red size="5">注意</font> 

为了防止找不到类，我们需要引用bin文件夹中的CKFinder.dll文件，如图：

![引用dll](https://github.com/MINDOU520/Images/raw/master/dll.png)

并且找到CKFinder的config.ascx，修改其中的参数，如图：

![修改参数](https://github.com/MINDOU520/Images/raw/master/CheckAuthentication.png)

之后在CKeditor文件下的Config.js的配置中加入

```
config.filebrowserImageUploadUrl = "/Home/Upload";
```

接下来就要把ckfinder集成到ckeditor中了，代码如下：

```
var editor = CKEDITOR.replace('editor1');
CKFinder.SetupCKEditor(editor, '/ckfinder/');
```

当然，在页面相应位置引用script代码是必不可少的。

```
<script type="text/javascript" src="/ckfinder/ckfinder.js"></script>
```

到此为止，就已经配置完成了，我们可以看到在图片上传或者Flash上传中已经有上传功能了，如图：

![上传功能](https://github.com/MINDOU520/Images/raw/master/up.png)

# 结语
关于ASP.NET MVC模式在VS2017中的CKeditor+CKFinder配置就已经介绍完了，希望能对您有用！













