---
title: Pack URIs in WPF
date: 2018-04-29 17:23:13
tags: Programing
---

[Pack URI](https://msdn.microsoft.com/en-us/library/aa970069\(v=vs.100\).aspx) 是WPF中用于定位资源文件
的特殊语法，和http:// , ftp://  类似，*pack* 是URI的scheme.

Pack URI分为绝对URI(Absolute)和相对URI(Relative)两种，在XAML中最常见的是相对URI，比如下面在XAML中声明一个Image：

``` html
<Image source="images/winter.jpg"/>
```

其中，*images/winter.jpg*，也可以使用更繁琐的绝对URI来这样声明Image对象：

``` html
<Image source="pack://application:,,,/images/winter.jpg"/>
```

这两种方式是等效的，只不过在这里使用绝对URI来设置Image的source不会带来任何好处。

pack URI的语法看起来很奇怪，是因为它在一个URI里面嵌入了另一个URI，*application:,,,*中的三个逗号实际上是三个转义的反斜杠，
也就是说嵌入的这个URI是以*application:///*开头的。

上面描述的场景是在同一个程序集中检索资源文件，如果你的程序有多个模块，需要引用另一个程序集中的资源，这时较短的相对URI在XAML中就无能为力了，
就需要绝对URI来大显身手:

``` html
<Image Source="pack://application:,,,/ImageLibrary;component/images/winter.jpg"/>
```

这里将Image的Source属性设置为被嵌入到名为ImageLibrary的程序集中的图像。
也可以下面等价的的C\#代码替换：

``` csharp
        var img = new Image();
        var uri = new Uri("pack://application:,,,/ImageLibrary;component/images/winter.jpg");
        img.Source = new BitmapImage(uri);
```

在C\#代码中也可以使用下面相对URI的形式：

``` csharp
        var img = new Image();
        var uri = new Uri("ImageLibrary;component/images/winter.jpg",UriKind.Relative);
        img.Source = new BitmapImage(uri);
```

如果使用强命名的程序集，在pack URI中还可以使用包含版本和公钥标记的名称还限定一个程序集，程序集名称，版本号和公钥之间用分号隔开：

``` csharp
        var img = new Image();
        var uri = new Uri("ImageLibrary;v1.25;56fcf93f19314c9e;component/images/winter.jpg", UriKind.Relative);
        img.Source = new BitmapImage(uri);
```

