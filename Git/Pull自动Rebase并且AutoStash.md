## Pull自动Rebase并且AutoStash

刚开始使用git命令行时，没注意到它有配置文件可以配置命令执行时需要的参数

为解决自动`stash`的问题使用如下指令：

```
git pull --autostash
```

这样本地存在未提交的修改时，可以拉取线上仓库的代码，若支持`rebase`，使用如下指令：

```
git pull --rebase --autostash
```

但是每次都这样输入指令太繁琐，解决办法就是如下提示：

#### 全局修改

```
$vim ~/.gitconfig

// 添加如下内容
[pull]
	rebase = true
[rebase]
	autoStash = true

// 保存修改
```

#### 仓库修改

```
cd projects/*****/repo
git config pull.rebase true
git config rebase.autoStash true
```