


#git config user.name “iranw”           //配置变量(本git目录-针对本目录)
#git config user.email “wang_wenguan@yeah.net” //配置变量 
#git config --global user.name “iranw”  //配置变量(用户目录-针对本用户)
#git config --system user.name “iranw”  //配置变量(系统用户目录-针对所有用户)
#git config --list                          //查看所有变量
#git config user.mail                       //获取特定系统变量
#git help                               //获取帮助信息
#git help config                            //获取特定帮助信息(方式1)
#git config --help                      //获取特定帮助信息(方式2)


#git init                                   //初始化git目录(文件已存在)
#git clone https://github.com/*.git     //克隆仓库(默认目录为项目名)
#git clone https://github.com/*/*.git dirname       //克隆仓库到特定目录(dirname)
#git工作目录所有文件氛围两种状态 : 以跟踪、未跟踪(untracked)。已跟踪文件指本来就被纳入版本控制管理的文件。未跟踪文件是既没有上次更新时的快照，也不在当前暂存区里
#已跟踪文件状态又分为：未更新(unmodified)、以修改(modified)、已放入暂存区(staged)
#untracked      未跟踪
#unmodified 未修改
#modified       已修改
#staged         已放入暂存区



#git status                             //查看当前文件状态
#git add a.txt                          //开始跟踪某个文件
#git add --all                          //跟踪所有文件
#git add .                              //跟踪所有文件

#在git项目管理目录添加.gitignore文件可以忽略特定文件免于跟踪

#git diff                               //查看已暂存&未暂存的更新差别(所有文件)
#git diff a.txt                         //查看特定文件更新
#git diff --staged                      //查看已暂存与上次提交文件差异
#git diff --cached                      //查看已暂存与上次提交文件差异
#git diff --cached a.txt                    //查看已暂存与上次提交特定文件差异
#git diff --staged a.txt                    //查看已暂存与上次提交特定文件差异
#git commit                             //提交更新
#git commit -m “提交说明”               //提交更新
#git commit -a -m “提交说明”            //提交所有跟踪文件，省略add步骤
#git rm a.txt                               //解除文件跟踪(移除)(同时删除物理文件)
#git rm -f a.txt                            //解除文件跟踪(删除物理文件+暂存区)
#git rm -cached a.txt                       //从git中移除文件(不删除物理文件)
#git mv a.txt b.txt                     //文件从命名

#git log                                    //查看提交历史
#git log -p -2                          //查看历史(p:详情，2:显示条数)
#git log --stat                         //显示日志并显示行数统计
#git log --pretty=oneline               //每次提交一行显示
#git log --pretty=format:”%h-%an,%ar:%s”    //定时格式化显示
#git log --pretty=format:”%h %s” --graph    //图形化衍合和分支信息
#gitk                                   //调用图形化分支对比工具

#git commit --amend                 //合并本次提交与上次提交
#git reset HEAD a.txt                       //取消暂存文件
#git checkout -- a.txt                  //取消对文件的修改(已修改文件&未暂存)

#git remote                             //查看远程仓库
#git remote -v                          //查看远程仓库(详细信息)
#git remote --verbose                   //同上
#git remote add name https://github.com/*.git   //添加远程添加
#git fetch remote-name                  //远程仓库抓取数据(fetch只是将远程数据拉倒本地仓库，并不合并到当前分支)
#git push origin master                 //推送数据到远程仓库
#git remote show origin                 //查看远程分支情况
#git remote rename a b                  //修改远程仓库本地名称

#git tag                                    //列出所有标签
#git tag -a v1.0 -m “标签说明”          //添加标签
#git show v1.0                          //查看标签信息
#git push origin v1.0                       //将标签传到服务器

Git保存的不是文件差异或者变化量，而是一系列文件快照
HEAD是当前分支的别名

#git branch a                           //创建分支
#git checkout a                         //切换分支
#git checkout master                    //切换分支
#git checkout -b a                      //创建并切换分支
#git merge hotfix                       //合并分支到当前分支
#git branch -d hotfix                       //删除分支
#git branch -D hotfix                   //强制删除分支
#git branch -v                          //查看分支最后一次提交信息
#git branch --merged                    //查看并入当前分支的分支
#git branch --no-merged             //查看未并入当前分支的分支

#git branch -vv 查看跟踪分支

#git checkout -b localBranchName origin/remoteBranchName
#git checkout -b [branch] [remotename]/[branch]

#git branch -u origin/branchName  改变当前分支跟踪的远程分支
#git branch --set-upstream-to origin/branchName 改变当前分支跟踪的远程分支


#git checkout --track origin/remoteBranchName