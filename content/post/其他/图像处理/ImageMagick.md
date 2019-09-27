---
#author: "yixy"
title: "ImageMagick"
date: 2019-09-26T12:50:12+08:00
lastmod: 2019-09-26T12:50:12+08:00
tags: [
    "Tools","图像处理"
]
categories: [
    "其他"
]
# you can close something for this content if you open it in config.toml.
#comment: false
#mathjax: false
#contentCopyright: false
---

## ImageMagick简介 ##

> ImageMagick是一套功能强大、稳定而且开源的工具集和开发包，可以用来读、写和处理超过89种基本格式的图片文件，包括流行的TIFF、JPEG、GIF、 PNG、PDF以及PhotoCD等格式。利用ImageMagick，你可以根据web应用程序的需要动态生成图片, 还可以对一个(或一组)图片进行改变大小、旋转、锐化、减色或增加特效等操作，并将操作的结果以相同格式或其它格式保存，对图片的操作，即可以通过命令行进行，也可以用C/C++、Perl、Java、PHP、Python或Ruby编程来完成。同时ImageMagick提供了一个高质量的2D工具包，部分支持SVG。ImageMagic的主要精力集中在性能，减少bug以及提供稳定的API和ABI上。 

> https://www.w3cschool.cn/imagemagick_use/

```
brew install ImageMagick
```

* convert：转换图像格式和大小，模糊，裁剪，拼接，驱除污点，抖动，临近，图片上画图片，加入新图片，生成缩略图等。 
* identify：描述一个或较多图像文件的格式和特性。 
* mogrify：按规定尺寸制作一个图像，模糊，裁剪，抖动等。Mogrify改写最初的图像文件然后写到一个不同的图像文件。 
* composite：根据一个图片或多个图片组合生成图片。 
* montage：创建一些分开的要素图像。在含有要素图像任意的装饰图片，如边框、结构、图片名称等。 
* compare：在算术上和视觉上评估不同的图片及其它的改造图片。 
* display：如果你拥有一个X server的系统，它可以按次序的显示图片 
* animate ：利用X server显示动画图片 
* import ：在X server或任何可见的窗口上输出图片文件。 你可以捕获单一窗口，整个的荧屏或任何荧屏的矩形部分。 
* conjure ：解释执行 MSL (Magick Scripting Language) 写的脚本。

## 常用命令 ##

获取宽度和高度

```
identify test.png | cut -d ' ' -f 3 | cut -d 'x' -f 1
identify test.png | cut -d ' ' -f 3 | cut -d 'x' -f 2
```

压缩

```
convert -compress LZW youzhilane.jpg y.jpg
```

将图像的像素改为1024*768，注意1024与768之间是小写字母x 

```
convert -resize 1024x768  xxx.jpg   xxx1.jpg
```

将图像的缩减为原来的50%*50% 

``` 
convert -sample 50%x50%  xxx.jpg  xxx1.jpg
```

将图像顺时针旋转270度 

```
convert -rotate 270 sky.jpg sky-final.jpg
```

使用-draw选项还可以在图像里面添加文字:在图像的10,80 位置采用60磅的全黑Helvetica字体写上 Hello, World! 

``` 
convert -fill black -pointsize 60 -font helvetica -draw 'text 10,80 "Hello, World!" '  hello.jpg  helloworld.jpg 
```

批量格式转换

```
for file in *.jpg; do convert $file ${file%%.*}.png; done
```

批量生成缩略图

```
for file in *.png; do convert -sample 50%x50% $file ${file%%.*}.png;done
```

截图，w为要截取图片的宽，h为要截取图片的高，dx，dy是开始截取的偏移位置，以左上角为原点

```
convert result_60.jpg -crop wxh+dx+dy result_60.jpg
```

拼图，横向拼接

```
convert 1.jpg 2.jpg 3.jpg +append result.jpg
```

拼图，纵向拼接

```
convert 1.jpg 2.jpg 3.jpg -append result.jpg
```

拼图，横向＋纵向

```
convert /( 1.jpg 2.jpg 3.jpg +append /) /
/( 4.jpg 5.jpg 6.jpg +append /) /
/( 7.jpg 8.jpg 9.jpg +append /) /
-append result.jpg
```

## 在线图像压缩网站 ##

折腾了半天，发现貌似还是在线工具压缩效果好一些。可以用picdiet，它采用了客户端浏览器js本地压缩。

https://www.picdiet.com/zh-cn

## 相关链接 ##

https://www.cnblogs.com/yymn/p/4479805.html

https://www.cnblogs.com/mfryf/p/7492500.html