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