## Provision文件安装后提示无法匹配证书私钥

### 0x01 表现

具体表现如下图所示：

![pic1](./Resources/Provision_Not_Match01.png)

### 0x02 折腾

- 清理并重新安装配置文件（未解决问题）
- 重新制作配置文件（怀疑配置文件被线上修改，但是未解决问题）
- 删除本地KeyChain中的证书，并从线上下载重新安装（未解决）
- 换了一台电脑，并未出现类似的问题，表明该问题仅在这一台电脑上因为某种原因所致（应该是缓存）

### 0x03 解决文案之一

1. 开启自动管理Provision文件功能，将复选框勾选即可，如下图所示

![pic2](./Resources/Provision_Not_Match02.png)

2. 关闭自动管理Provision文件功能，取消上图中的勾选框即可；
3. 重新手动设置Provision文件，问题消除；

![pic3](./Resources/Provision_Not_Match03.png)

> **NOTE：**很偶然地就把这个问题解决了，不知是不是巧合，待后续遇到之后再验证；

### 0x04 解决文案之二

待补充