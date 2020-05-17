# 1.git

## 1.1 git是什么

- 分布式
- 版本控制
  - 文件控制
  - 本地 文件保留
  - 集中式版本控制
    - 多人开发 多人协作svn
  - 分布式版本控制
    - 每个电脑都有一份多个版本的备份
    - 
- 软件，安装电脑工具

## 1.2 为什么要做版本控制

- 回滚
- 方便

## 1.3 使用

- git管理文件夹

  - 进入要管理的文件夹（进入）
  - 初始化（提名）
  - 管理
  - 生成版本

  

- 1.git init 初始化

![image-20200515201726772](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200515201726772.png)

- ###### 2.git status查看文件状态

  - 三种状态 
    - 红色：新增的文件/修改了原老文件 git add 文件名
    - 绿色：git已经进行管理 git commit -m '描述信息'
    - 生成版本

  ![image-20200515205741900](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200515205741900.png)

- 3.git add index.html  全部管理 git add .

  ![image-20200515205906637](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200515205906637.png)

- 4.个人信息配置：用户名、邮箱

  - git config --global user.email "you@example.com"
  - git config --global user.name "Your Name"

- 5.git commit -m '第一个版本'   修改文件后git会检测 git status

  ![image-20200515212112437](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200515212112437.png)

- 6.查看版本记录 git log

## 1.4 git的三个状态

- 工作区 红色 用来开发
  - 文件状态：git会自动检测，可以通过git add提交到暂存区
    - 已管理的文件
    - 已修改的文件
- 暂存区 绿色 缓冲
  + 通过git commit 提交到版本库
- 版本库 用来保存

## 1.5 回滚

- 查出版本号的commit值 

  ```
  git log
  ```

- 回滚到上次的版本 

  ```
  git reset --hard ae9aa3236bf20a470e8d8209d6df5f4e92c4933f
  ```

  ![image-20200515214904045](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200515214904045.png)

- 再回滚到原来的版本查询版本号 

  ```
  git reflog  git reset --hard a5dfa53
  ```

  

![image-20200515215356837](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200515215356837.png)

# 2.总结

```
git init
git add
git commit -m
git log
git reset --hard 版本号
git reflog
git checkout -- index.html红变白，变回修改之前的状态
git reset head -- index.html
```

![image-20200516161624021](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200516161624021.png)

# 3. 线上代码bug紧急修复

![](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200515221537654.png)

## 3.1 创建分支进行修复，再进行合并

- master 主干

  - bug 分支 隔离的独立环境

  - dev 分支 隔离的独立环境

    ![image-20200515223859376](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200515223859376.png)

## 3.2 查看处于哪个分支

```
git branch
```

## 3.3 创建新的分支

```
git branch 分支名称dev bug
```

## 3.3 切换到分支

git checkout 分支名称 

## 3.4 分支合并

```
git checkout master
git merge 要合并的分支
注意：切换分支再合并
```

## 3.5 分支合并产生冲突

~~~python
手动删除
~~~

## 3.6 删除分支

```
git branch -d 分支名称
```

# 4.工作流

masterk开发稳定版本

创建分支dev专门用于开发新功能，测试版试用稳定后再做合并

![image-20200515232831415](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200515232831415.png)

# 5.代码托管

## 5.1 注册guthub

- （个人、小企业）、gitlab（企业）等账号

## 5.2 创建仓库

## 5.3 本地代码推送到仓库

```
echo "# dbhost" >> README.md
git init
git add README.md
git commit -m "first commit"
# 给远程仓库起别名
git remote add origin https://github.com/kemeng1417/dbhost.git
# 向远程仓库推送代码
git push -u origin master（分支名称）
```

```
# 第一次克隆远程仓库代码
git clone 远程仓库地址 （内部已实现起别名）
# 切换分支
git checkout 分支
```

![](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200515225307370.png)

在公司进行开发

```
1. 切换到dev分支进行开发
	git checkout dev
2. 把master分支合并到dev（仅执行一次）
    git merge master
3.修改代码
4.提交代码
    git add .
    git commit -m 'xx'
    git push origin dev
```

回到家中继续开发

```
1.切换到dev分支进行开发
	git checkout dev
2.拉代码
	git pull origin dev
3.继续开发
4.提交代码
    git add .
    git commit -m 'xx'
    git push origin dev
```

开发完毕上线

```
1.将dev合并到master，进行上线
	git checkout master
	git merge dev
	git push origin master
2.把dev分支也推送到远程
	git checkout dev
	git merge master
	git push origin dev
```

```
git pull origin dev

git fetch origin dev
git merge origin/dev
```

# 6.rebase(变基)的三个应用场景

![image-20200516161720425](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200516161720425.png)

# 7.快速解决冲突

1. 安装beyond compare

2. 在git中配置

   ```
   git config --local merge.tool bc3
   git config --local mergetool.path "c:/Program Files/Beyond Compare 4/BComp.exe"
   git config --local mergetool.keepBackup false
   ```

3. 应用beyond compare 解决冲突

   ```
   git mergetool 
   ```

# 8.总结

- 添加远程连接

  ```
  git remore add origin 地址
  ```

- 推送代码

  ```
  git push origin dev
  ```

+ 下载代码

  ```
  git clone 地址
  ```

+ 拉取代码

  ```
  git pull origin dev
  等价于
  git fetch origin dev
  git merge origin/dev
  ```

+ 保持代码提交整洁（变基）

  ```
  git rebase 分支
  ```

+ 记录图形展示

  ```
  git log --graph --pretty=format:"%h %s"
  ```

# 9.多人开发流程

## 9.1 gitflow工作流

![image-20200516142531825](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200516142531825.png)



## 9.2 创建项目&邀请成员

协同开发时，需要所有成员都可以对同一个项目进行操作，需要邀请成员并赋予权限，否则无法开发，github支持两种创建项目的方式

1. 合作者，将用户添加到仓库合作者中之后，该用户就可以向当前仓库提交代码![image-20200516162022427](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200516162022427.png)

2. 组织，将成员邀请进入组织，组织下可以创建多个仓库，组织成员可以向组织下仓库提交代码

![image-20200516162124469](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200516162124469.png)

![image-20200516162131996](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200516162131996.png)

![image-20200516162138055](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200516162138055.png)

![image-20200516162144961](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200516162144961.png)

**Tag标签管理**：为了清晰的管理版本，在公司不会直接使用commit版本来做，会基于tag实现：v1.0、v1.2、v2.0版本

```
git tag -a v1.0 -m '版本介绍'
git tag -d v1.0
git push origin --tags
git pull origin --tags

git checkout v1.0 切换tag
git clone -b v0.1 地址 指定tag下载代码
```

## 9.3 code review

1. 配置，代码review之后才能合并到dev分支。

![image-20200516162629806](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200516162629806.png)

![image-20200516162634398](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200516162634398.png)

2. 提交review

![image-20200516162657594](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200516162657594.png)

![image-20200516162702570](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200516162702570.png)

3. 组长做review

![image-20200516162722545](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200516162722545.png)

![image-20200516162727318](C:\Users\km\AppData\Roaming\Typora\typora-user-images\image-20200516162727318.png)

## 9.4 提测上线（预发布）

由专们团队或团队leader执行以下步骤

1. 基于dev分支创建release分支

   ```
   git checkout dev
   git checkout -b release
   ```

2. 测试等

3. 合并到master

   ```
   使用pull request
   或
   本地将release合并到master分支
   ```

4. 在master分支打tag

   ```
   git tag -a v2 -m '第二版'
   git push origin --tags
   ```

5. 运维人员就可以去下载代码做上线

   ```
   git clone -b v2 地址
   ```

## 9.4 给开源软件贡献代码

1. fork源代码 将别人源代码拷贝到我自己的远程仓库
2. 在自己仓库进行修改代码
3. 给源代码的作者提交 修复bug的申请（pull request）

# 10.其他

## 10.1配置

- 项目配置文件： 项目/.git.config

  ```
  git config --local user.name 'km'
  git config --local user.email 'km@qq.com'
  ```

- 全局配置文件：~/.gitconfig

  ```
  git config --global user.name 'km'
  git config --global user.email 'km@qq.com'
  ```

- 系统配置文件：/etc/.gitconfig

  ```
  git config --system user.name 'km'
  git config --system user.email 'km@qq.com'
  ```

应用场景：

```
git config --local user.name 'km'
git config --local user.email 'km@qq.com'

git config --local merge.tool bc3
git config --local mergetool.path "c:/Program Files/Beyond Compare 4/BComp.exe"
git config --local mergetool.keepBackup false

git remote add origin 地址 默认添加在本地配置文件中（--local）
```

## 10.2 免密码登录

- url中体现

  ```
  原来的地址：https://github.com/kemeng1417/dbhost.git
  修改的地址：https://用户名：密码@github.com/kemeng1417/dbhost.git
  
  git remote add origin https://用户名：密码@github.com/kemeng1417/dbhost.git
  git push origin master
  ```

- SSH实现

  ~~~
  1. 生成公钥和私钥(默认放在~/.ssh目录下，id_rsa.pub公钥 、 id_rsa私钥
  	ssh -keygen
  2. 拷贝公钥的内容，并设置到github中
  3. 在git本地中配置ssh地址
  	git remote add origin git@github.com:kemeng1417/dbhost.git
  	
  4. 使用不需要密码
  	git push origin master
  ~~~

- git自动管理凭证

## 10.3 git忽略文件

在git不再管理当前目录下的某些文件

```
*.h
!a.h
files/
*.py[c|a|d]
```

更多参考：https://github.com/github/gitignore

## 10.4 github任务管理相关

- issues 文档以及任务管理
- wiki 项目文档