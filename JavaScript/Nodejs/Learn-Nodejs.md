## Learn Nodejs API

### 0x01 Child Process

生成新的线程执行操作，包含异步和同步接口，示例如下：

```
const spawn = require('child_process').spawn;
// const spawn = require('child_process').spawnSync;
const ls = spawn('ls', ['-lh', '/usr']);    // use absolute path

ls.stdout.on('data', (data) => {
  console.log(`stdout: ${data}`);
});

ls.stderr.on('data', (data) => {
  console.log(`stderr: ${data}`);
});

ls.on('close', (code) => {
  console.log(`child process exited with code ${code}`);
});
```

- child_process.excute() or child_process.excuteSync() 执行shell命令
- child_process.excuteFile() or child_process.excuteFileSync()执行shell脚本文件

以上每个所有的方法都返回一个'Child Process'实例；

### 0x02 Cluster

该模块主要是充分利用多核CPU的优点，最大发挥机器的性能，均衡服务的负载，将所有的请求分散到不同的Worker线程；

更具体的使用待补充；
