## git的相关命令
git init

git add filenames

git commit -m "describe"

git status

git diff filename

git log

git log --pretty=oneline  //减少日志的显示

git reset --hard HEAD^/HEAD^^/HEAD~100  指定回退到多少个版本

git reflog  查看历史版本号

git checkout --filename  撤销修改，丢弃工作区的修改（理解工作区与暂存区）

git clone url   克隆远程仓库的东西

git checkout -b dev  创建dev分支并且切换到dev分支，等价于git branch dev和git checkout dev

git checkout -d dev  删除分支

git branch 查看所有分支

git branch -a 查看远程的所有分支

git checkout master 切换到master分支

git merge dev 合并dev分支

git merge --no-ff -m "describe" dev 合并dev分支信息任然保存

git log --graph  查看分支合并图

git stash  存数工作现场，然后去修改bug再次返回

git stash list  查看现场

git stash pop  返回现场，等价于git stash apply + git stash drop

git branch -D name  强行删除一个从未被合并的分支

git tag v1.0  创建标签

git rm file_path 删除暂存区或分支上的文件，同时工作区也不需要这个文件了

git rm --cached file_path 删除暂存区或分支上的文件，但是本地需要使用，只是不希望这个文件被版本控制，结合.gitignore文件使用

git fetch origin + git diff 本地分支(local branch) origin/远程分支(remote branch)  对比当前工作区与远程仓库的区别

git对比两个分支的差异：master,dev
查看dev有，而master中没有的：git log dev ^master
查看master中有，而dev中没有的内容： git log master ^dev
> 多人协作开发：每个人建立不同的分支
* git push origin branchname  推送自己的修改
* 如果推送失败，git pull 视图合并(如果显示no tracking information)
则远程分支与本地分支没有建立联系，需要重新创建
git branch --set-upstream branch-name origin/branchname
* 如果有冲突，解决冲突，并且在本地提交
* 没有冲突或冲突已经解决，git puash origin branchname

参考连接： [https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000]()
## git常用命令
1. 将远程仓库的文件clone到本地：
```
git clone url(https)
```
2. 本地文件连接到远程仓库
```
cd folder
git init
git add .
git commit -m "备注信息" -a
//远程建立仓库，复制url
git remote add origin url
git push -u origin master
```
3.撤销操作
```
git add 后撤销
撤销所有的add文件： git reset HEAD .
撤销单个add文件: git reset HEAD -filename

git commit 后撤销
只回退commit的信息，保留修改代码: git reset --soft head
彻底回退到上次commit的版本，不保留修改代码git reset --hard head^
head : 当前版本
head^: 上一个版本
--hard 会抛弃当前工作区的修改
--soft 会回退到之前的版本，但是保留当前工作区的修改，可以重新提交

撤销所有本地改动代码
git checkout.

本地代码回退到与远程仓库保持一致
git reset --hard

git push撤销
回滚此次push到服务器的代码
git log查看commit的信息
git revert 以前commit的id
git push此时本地回滚的代码到服务器就可以了

git merge 撤销
git checkout
git reset --hard
```
4. git忽略而不提交文件的3种情况
- 从未提交过的文件可以使用.gitignore
```
//在.gitignore文件中添加忽略的文件夹或者文件，例如:log/文件夹
log/*
```
- 已经推送（push）过的文件，想从git远程仓库中删除，并在以后的提交中忽略，但是本地还是保留这个文件
```
git rm --cached Xml/config.xml
```
- 已经推送(push)过的文件，想在以后提交的时候忽略此文件，即使本地已经修改过，不删除远程仓库中对应的文件
```
git update-index --assume-unchanged Xml/config.xml
//如果要忽略一个目录
git update-index --assume-unchanged $(git ls-files | tr '\n' ' ')
```

### vim编辑器
vim -R file 查看文件只读

常用的命令
esc键 退出编辑,进入命令模式
命令模式下常用的命令
h,j,k,l分别表示左、下、右、上
行号gg/行号G 快速定位到行  G是到文档的末尾，gg是第一行，Ngg是第N行
i 推出命令模式，进入编辑模式
I 插入到行开始的地方
a 进入编辑模式在光标的后面进行编辑
A 插入到行的末尾
dd 删除一行
dw 删除一个单词
Nyy 复制一行或者N行
yw 复制一个单词
y 复制
d 删除
p 粘贴
v 选中一个或多个字符
V 选中一行 
/ 全局搜索
? 往后搜索
o 另起一行进行编辑
u 撤销上一步的操作
n 光标达到搜索的前一个
N 光标达到搜索的后一个
e 光标定位在一个单词的末尾
b 光标定位在一单词开始
^ 光标定位到一行的开始
$ 光标定位到一行的末尾
> 相当于一个tab的功能

:args 显示正在编辑的文件名称

:w 保存

:wq or :x 保存并退出

:q 关闭（已经保存）

:q!  强制退出



### linux常用的命令
mkdir filename 创建文件夹
vi filename  创建文件，已经存在则直接打开
touch filename 创建文件，已有直接打开
cat filename 打印出文件内容
rm -rf filename 删除文件以及文件夹
rar x rarname 解压文件

### git远程连接服务器进行操作
- 连接命令ssh:
ssh 用户名@地址  ssh root@12.121.121.21
- 文件操作scp:
scp用来将服务器端的文件拷贝到本地，或者将本地文件拷贝到远程服务器

### git客户端配置多个用户（多个git账号）
1. 取消git config的全局配置
```$xslt
git config --global unset user.name "name"
```
2. 生成公钥和秘钥，放在不同的文件中
```$xslt
ssh-keygen -t rsa -f 'filename1' -C 'email1'
ssh-keygen -t rsa -f 'filename2' -C 'email2'
```
3. 设置代理
```$xslt
ssh-agent bash
ssh-add ~/.ssh/filename1
ssh-add ~/.ssh/filename2
```
4. 将公钥复制到远程settings中
5. 在.ssh文件夹下配置文件
```
touch config
vi config
具体的编辑内容如下：
#default gitlab user(pengyu1@corp.netease.com)
Host git@g.hz.netease.com
HostName g.hz.netease.com
User pengyu1
IdentityFile ~/.ssh/id_rsa
Port 22222

#default github user(2389726648@qq.com)
Host git@github.com
HostName ssh.github.com
User p0301y
IdentityFile ~/.ssh/id_rsa_new
Port 443
```
注意事项：在使用的时候需要在不同的项目中设置
```$xslt
git config user.name ''
git config user.email ''
```

### git中的子模块管理的使用
1. 使用场景
    - 多个主项目(A\B\C)都依赖于子项目sub(或者说是一个通用的库lib)：当子项目代码更新的时候，主项目只要通过
    git上的主项目会自动更新，但是clone到本地的主项目只需要updata命令就可以更新依赖；主项目和子项目的维护
    相互独立，互不干扰
2. submodule
    - git Submodule允许子项目单独作为一个git项目存在在父项目中，子项目可以有自己独立的commit,push,pull等操作
3. 项目中使用Submodule
     - git命令添加submodule：git submodule add git@github.com:jjz/pod-library.git pod-library
      将git@github.com:jjz/pod-library.git添加到pod-library文件夹下
     - 修改submodule: cd pod-library git status/add/commit
     - 更新submodule: git submodule foreach git pull （在父项目下直接运行） cd and git pull (在submodule目录下更新)
     - clone submodule: 
        1. 采用递归的方式clone整个项目(--recursive): git clone git@github.com:jjz/pod-project.git --recursive
        2. 先clone父项目，然后初始化和更新子项目:   
        初始化    
        git clone git@github.com:jjz/pod-project.git     
        cd pod-project   
        git submodule init   
        更新   
        git submodule update

