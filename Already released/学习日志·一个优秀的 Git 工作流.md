# 学习日志·一个优秀的 Git 工作流

* 参考 https://www.bilibili.com/video/BV19e4y1q7JJ

三个仓库 Remote  Local Disk 将本地想象成两个部分

`git clone` 三仓库统一

`git checkout -b my-feature` 创建 feature branch 而不是往 main push code

“硬盘 Disk 不在意是源文件来自哪个 branch”

`git diff` 查看暂存区与disk区文件的差异

`git add <changed_file>`  

`git commit` 将暂存区内容添加到local区的当前分支中

`git push origin my-feature` 提交

`git checkout main init` 不是修改状态

`git fetch origin` 拉取远端最新分支

`git rebase main` 这一步会让 Git 把在 my-feature 上的提交“搬到”origin/main 最新提交之后

> 假设当前分支与 main分支存在共同部分common，该指令用 main分支包括common在内的整体替换当前分支的common部分（原先xxx分支内容为common->diversityA，当前分支内容为common->diversityB，执行完该指令后当前分支内容为common->diversityA->diversityB）。

然后在 Github create pull request ->Squash and merge

删除远端 branch 

删除本地 先切换`git checkout main` 再 `git branch -D my-feature`

`git pull origin master`

另：实际项目情况

main：生产环境，也就是你们在网上可以下载到的版本，是经过了很多轮测试得到的稳定版本。 

release：开发内部发版，也就是测试环境。 

dev：所有的feature都要从dev上checkout。 

feature：每个需求新创建的分支。 



1.从dev分支上checkout -b new-feature，进行开发 

2.开发完后，经过自测没问题了，准备发版 

3.merge到release分支上进行发版，并打tag 

4.有bug就直接在release上进行修改，改完再次发版，打tag，直到测试人员验证完毕 

5.这时可以将release分支合并到dev上，也可以删除掉feature分支了，并等待通知是否将此功能上线（在正式服务器上运行），如果上线，那就merge到main（master）分支。

