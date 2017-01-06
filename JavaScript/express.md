## Express踩过的坑

### 0x01 中间件

#### 1.1 Json解析

服务端Json的解析要用到`body-parser`这个中间；但是，不得不说中间某个版本该中间件发生了变化

```
This does not handle multipart bodies, 
due to their complex and typically large nature.
For multipart bodies, you may be interested in the following modules:

- busboy and connect-busboy
- multiparty and connect-multiparty
- formidable
- multer
```

在设计一个**Post**请求时，仅使用了`json（）`去解析body，在Header中没有设置`multipart/form-data`时，解析是正常的，一但设置，服务端的body内没有任何数据；

> **解决方法**：使用上文中提到的几个中间件，例如`connect-multiparty`

**Usage:**

```
var multipart = require('connect-multiparty');
var multipartMiddleware = multipart();
app.post('/upload', multipartMiddleware, function(req, resp) {
  console.log(req.body, req.files);
  // don't forget to delete all req.files when done
});
```

[connect-multiparty中间件](https://github.com/expressjs/connect-multiparty)

[body-parser中间件](https://github.com/expressjs/body-parser#body-parser)

[Anatomy of an HTTP Transaction](https://nodejs.org/en/docs/guides/anatomy-of-an-http-transaction/)

### 1.2 multipart/form-data 解析

在`Express 4.x`版本中，对于`Content-Type="multipart/form-data"`的数据解析，`body-parser`默认不做处理；

> **NOTE：**貌似在`Express3.x`的时候，`body-parser`把解析`Content-Type="multipart/form-data"`类型数据的库内置了的，后来又拿出来了，真折腾；

下面以上传文件为例分别使用`multer`和`multipart-connect`来实现文件的上传：

H5页面代码如下：

```
<body>
  <p>上传文件</p>

  <form action="/upload" method="post" enctype="multipart/form-data">
    <p>文件名</p>
    <input type="text" name="alias">
    <br><br>
    <input type="file" name="hotfix">
    <br><br>
    <input type="submit">
  </form>
</body>
```

> **NOTE:** `form`标签的`content-type`类型使用`submit`时默认为`www-url-encoded`, 上传文件时记得要使用`multipart/form-data`类型；

#### 1.2.1 使用multer实现文件上传

```
let multer = require('multer');

let storage = multer.diskStorage({
  destination: function (req, file, cb) {
    cb(null, './hotfixs');
  },
  filename: function (req, file, cb) {
    var fileFormat = (file.originalname).split(".");
    cb(null, file.fieldname + '-' + Date.now() + "." + fileFormat[fileFormat.length - 1]);
  }
});

let upload = multer({
  storage: storage
});

// @route('/upload', post)
app.post('/upload', upload.single('hotfix'), function(req, res) {
  // 代码运行到此处时文件已经写入「./hotfixs」目录下面
  res.status(200).send({success: true});
});

```

#### 1.2.2 使用multipart-connect实现文件上传

```
let multipart = require('connect-multiparty');
app.post('/upload2', multipart(), function(req, res) {
  // 代码运行此处时，文件已经写入一个临时目录内
  res.status(200).send({success: true});
});
```

#### 1.2.3 总结

`multipart-connect`使用完毕后要记得清理垃圾；