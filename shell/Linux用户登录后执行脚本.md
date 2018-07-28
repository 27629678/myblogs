## Linux用户登录（或登出）后运行脚本

### 1. 若任意用户登录都运行脚本

把脚本（shell）放置在以下目录内：

```
$:/etc/profile.d/
```

### 2. 指定用户登录后运行脚本

把脚本放置在指定用户目录内：

```
$:/home/user_name/.bash_profile
```

> NOTE: 最好把脚本放置在bash_profile文件内，若放置在bash_login文件内，在bash_profile文件存在的情况下不会执行bash_login文件内的脚本；