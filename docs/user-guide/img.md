## 图片处理

通过填写几个参数即可对图片进行缩略操作，生成各种缩略图。


### 接口规范

```
img-url?szhdl=imageview/<mode>/w/<Width>/h/<Height>
```

注意，接口规范 **不含** 任何空格与换行符

### mode

其中 ```mode``` 分成如下几种情况

模式|说明
---|---
/**0**/w/<LongEdge>/h/<ShortEdge>|限定缩略图的长边最多为<LongEdge>，短边最多为<ShortEdge>，进行等比缩放，不裁剪。如果只指定 w 参数则表示限定长边（短边自适应），只指定 h 参数则表示限定短边（长边自适应）。
/**1**/w/<Width>/h/<Height>|限定缩略图的宽最少为<Width>，高最少为<Height>，进行等比缩放，居中裁剪。转后的缩略图通常恰好是 <Width>x<Height> 的大小（有一个边缩放的时候会因为超出矩形框而被裁剪掉多余部分）。如果只指定 w 参数或只指定 h 参数，代表限定为长宽相等的正方图。
/**2**/w/<Width>/h/<Height>|限定缩略图的宽最多为<Width>，高最多为<Height>，进行等比缩放，不裁剪。如果只指定 w 参数则表示限定宽（长自适应），只指定 h 参数则表示限定长（宽自适应）。它和模式0类似，区别只是限定宽和高，不是限定长边和短边。从应用场景来说，模式0适合移动设备上做缩略图，模式2适合PC上做缩略图。
/**3**/w/<Width>/h/<Height>|限定缩略图的宽最少为<Width>，高最少为<Height>，进行等比缩放，不裁剪。如果只指定 w 参数或只指定 h 参数，代表长宽限定为同样的值。你可以理解为模式1是模式3的结果再做居中裁剪得到的。
/**4**/w/<LongEdge>/h/<ShortEdge>|限定缩略图的长边最少为<LongEdge>，短边最少为<ShortEdge>，进行等比缩放，不裁剪。如果只指定 w 参数或只指定 h 参数，表示长边短边限定为同样的值。这个模式很适合在手持设备做图片的全屏查看（把这里的长边短边分别设为手机屏幕的分辨率即可），生成的图片尺寸刚好充满整个屏幕（某一个边可能会超出屏幕）。
/**5**/w/<LongEdge>/h/<ShortEdge>|限定缩略图的长边最少为<LongEdge>，短边最少为<ShortEdge>，进行等比缩放，居中裁剪。如果只指定 w 参数或只指定 h 参数，表示长边短边限定为同样的值。同上模式4，但超出限定的矩形部分会被裁剪。


### 示例

![](http://photos.cdnzz.com/imageview/panda.jpg)

    原图
```
http://photos.cdnzz.com/imageview/panda.jpg
```

![](http://photos.cdnzz.com/imageview/panda.jpg?szhdl=imageview/0/w/200/h/200)

    等比缩小生成200x200缩略图
```
http://photos.cdnzz.com/imageview/panda.jpg?szhdl=imageview/0/w/200/h/200
```

![](http://photos.cdnzz.com/imageview/panda.jpg?szhdl=imageview/1/w/200/h/200)

    裁剪正中部分，等比缩小生成200x200缩略图
```
http://photos.cdnzz.com/imageview/panda.jpg?szhdl=imageview/1/w/200/h/200
```

### 注意
- 可以仅指定w参数或h参数；
- 新图的宽/高/长边/短边，不会比原图大，即本接口总是缩小图片；
- 所有模式都可以只指定w参数或只指定h参数，并获得合理结果。在w、h为限定最大值时，未指定某参数等价于将该参数设置为无穷大（自适应）；在w、h为限定最小值时，未指定参数等于给定的参数，也就限定的矩形是正方形。
