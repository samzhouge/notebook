
# git配置

## 全局配置
* 查看配置 git config --global -l
* 换行 windows系统下拉取代码换行符是linux的格式LF
  * git config --global core.autocrlf false
* git名字
  * git config --global user.name zhouge
* 邮箱 
  * git config --global user.email abc@email.com

## 回滚远程分支
```
# 获取将要回滚到的版本号
git log [--pretty=oneline --abbrev-commit]
# 回滚提交（本地回滚）
git reset --hard commentId
# 强制提交到远程分支
git push -f [origin xxx]
```
