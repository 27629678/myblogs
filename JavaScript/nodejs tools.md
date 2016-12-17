## Express辅助工具

### 1. supervisor

每次修改代码后会自动重启

```
// install
npm install -g supervisor

// excute
supervisor app.js
```

### 2. forever

'forever'是一个简单的命令式nodejs的守护进程，能够启动，停止，重启App应用。forever完全基于命令行操作，在'forever'进程之下，创建node的子进程，通过monitor监控node子进程的运行情况，一旦文件更新，或者进程挂掉，'forever'会自动重启node服务器，确保应用正常运行。
