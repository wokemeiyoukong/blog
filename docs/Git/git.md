#### `Git`

+ 分布式版本管理系统

  + 工作区 - 暂存区 - `git` 仓库

+ 查看用户

  ```js
  git config --global user.name
  ```

+ 初始化

  ```js
  git init 
  ```

+ 查看状态

  ```js
  git status
  ```

+ 添加文件至缓存区

  ```js
  git add . // 添加当前目录所有文件
  git add 文件名1 // 单个文件
  git add 文件名1 文件名2 // 多个文件
  ```

+ 存本地仓库

  ```js
  git commit -m 'description' // 推送到本地仓库并添加描述
  ```

+ 版本回退

  ```js
  git log // 获取对应改动的版本号id
  git log --pretty=oneline
  
  git reset --hard '版本号id'
  
  // 回到指定的版本号上
  git reflog // 所有的操作版本id
  ```

+ 线上仓库

  ```js
  git clone 'https/ssh' // 克隆线上仓库到本地
  git add .
  git commit -m 'desc'
  git push // 推送到线上仓库
  git pull // 从线上仓库拉去最新代码
  ```

+ 不同的 `clone` 方式

  ```js
  https / ssh // 配置公钥 操作方法一致
  ```

+ 多分支操作

  ```js
  git branch // 查看所有分支
  git branch '分支名' // 创建新分支
  git checkout '分支名' // 切换分支
  git merge '分支名' // 合并分支
  git branch -d '分支名' // 删除分支
  ```

+ 可视化工具

  ```js
  Github for Desktop
  Source Tree
  TortoiseGit
  ```

+ 创建忽略文件

  ```js
  touch .gitignore
  ```

+ 删除远程仓库

  ```js
  git romote -rm '仓库名'
  ```


+ 创建新分支

  ```js
  git checkout -b '新分支名'
  git push origin dev:dev '远端分支/新分支名'
  git branch --set-upstream-to=origin/dev
  git pull '拉取远端资源'
  ```

  

```
rm -r 删除文件夹
mv source target 移动资源文件到目标文件夹
pwd 显示当前所在目录路径
touch 创建文件
```

