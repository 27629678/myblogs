## WK(UI)WebView支持WebP图片

```
<html>
  <meta http-equiv="Access-Control-Allow-Origin" content="*">
  <script type="text/javascript" src="http://webpjs.appspot.com/js/webpjs-0.0.2.min.js"></script>
  <body>
    <img src="../image/image.webp" width="400" height="300" alt="This was my first image in/for alpha channel">
  </body>
</html>
```

`webjs`所做的工作大体如下：

1. 遍历`Dom`，将`image`标签`src`的扩展名为`webp`图片的`URL`保存；
2. 下载图片，转码成`PNG`；
3. 替换原标签`src`的值，局部渲染使图片显示出来；

所以，在**不支持**`WebP`图片的浏览器上可以看到图片未下载替换完成之后是一个空白的占位框，替换完成后再正常显示图片内容；

> NOTE: 无法解决扩展名为非`webp`的`WebP`类型的图片显示问题