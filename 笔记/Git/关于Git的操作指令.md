# 关于Git的操作指令

| 命令                                     | 作用                                        |
| ---------------------------------------- | ------------------------------------------- |
| **git config --global user.name** 用户名 | 设置用户签名                                |
| git config --global user.email 邮箱      | 设置用户签名                                |
|                                          |                                             |
| **git init**                             | 初始化本地库                                |
|                                          |                                             |
| **git status**                           | 查看本地库状态                              |
|                                          |                                             |
| **git add** <File>                       | 工作区-->暂存区                             |
| **git -commit -m** '日志‘                | 暂存区-->本地库                             |
| **git commit -a -m** '日志'              | 工作区-->本地库                             |
|                                          |                                             |
| **git log**                              | 查看当前分支提交历史                        |
| **git reflog**                           | 查看命令历史                                |
| **git log --graph**                      | 版本点线图                                  |
|                                          |                                             |
| **git reset --hard** 哈希值              | 恢复至指定版本                              |
| **git reset --hard** HEAD^^^             | 恢复至上n个版本（n个^）(HEAD可替换为分支名) |
| **git reset --hard** HEAD~n              | 恢复至上n个版本(HEAD可替换为分支名)         |
|                                          |                                             |
| **git reset --soft**                     | 仅仅在本地库移动 HEAD 指针                  |
| **git reset --mixed**                    | 在本地库移动 HEAD 指针，重置暂存区          |
| **git reset --hard**                     | 在本地库移动 HEAD 指针，重置暂存区和工作区  |
|                                          |                                             |
| **git diff**                             | 比较工作区和暂存区或本地库文件差异          |
| **git diff --cached**                    | 比较暂存区和本地库差异                      |
| **git diff** 哈希值/HEAD^^/HEAD~n        | 比较工作区和本地库中指定版本差异            |
|                                          |                                             |
| **git restore --staged** <file>          | 暂存区-->工作区                             |
| **git restore** <file>                   | 本地库-->工作区                             |
|                                          |                                             |
| **git branch** 分支名                    | 添加分支                                    |
| **git checkout -b** 分支名               | 新建一个分支，并切换到该分支                |
| **git branch -d** 分支名                 | 删除分支                                    |
| **git branch**                           | 查看所有分支                                |
| **git branch -v**                        | 查看分支以及每个分支最后一次提交            |
| **git checkout** 分支名                  | 切换分支                                    |
|                                          |                                             |
| **git merge** 分支名                     | 把指定的分支合并到当前分支                  |
|                                          |                                             |
| **git remote add** **别名 远程地址**     | 添加远程库                                  |
| **git remote -v**                        | 查看远程库别名、地址                        |
| **git remote rm** <name>                 | 删除远程库                                  |
|                                          |                                             |
| **git pull** 别名 分支名                 | 远程库-->本地库（合并）                     |
| **git push** 别名 分支名                 | 本地库-->远程库                             |
|                                          |                                             |
| **git clone** 远程地址                   | 克隆远程库                                  |
|                                          |                                             |
|                                          |                                             |
|                                          |                                             |
|                                          |                                             |
|                                          |                                             |
|                                          |                                             |
|                                          |                                             |

