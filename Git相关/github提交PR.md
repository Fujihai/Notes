# 一次 PR 的提交过程

此文主要记录向一个开源仓库提交 PR 的基本步骤。

**最重要的一点：先查看该开源库的 PR 提交规范。**

接下来是基础步骤：

* fork 开源仓库
* git clone 自己 fork 下来的仓库

```javascript
git clone git@github.com:liufushihai/xxxxx.git
```

* 创建新分支并切换到新分支上

```javascript
git checkout -b patch-1
```

* 修改文件内容并对比修改的内容

```javascript
git diff
git status
```

* git add - commit

```javascript
git add
git commit -m "update: Chinese README.md"
```

* 推送创建好的新分支到自己 fork 下来的远程仓库

```javascript
git push origin patch-1
```

* 在 github 上创建 pull request，源分支是自己创建好的新分支，目标分支是要合并的分支。 填好描述提交即可。