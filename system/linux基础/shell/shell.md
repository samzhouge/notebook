## 过滤
- 过滤空格和注释
```
  1. 正向搜索命令行：
  grep "^\s*[^# \t].*$"
  ”\s* #“ 代表0个或多个空格或Tab
  "[^# \t] #" 代表非#、非空格、非Tab的字符
  ".*" 代表任意字符重复任意遍
  \s和\t的区别在于，\s matches any whitespace character, including tabs. \t only matches a tab character.
  
  2. 排除法搜索命令行：
  egrep -v "^\s*#|^\s*$"
  "-v" 表示排除特定的字段
  "^\s*#" 首字符是#，或者有几个空格后紧跟# （注释行）
  "^\s*$" 无字符的行或者只有空格的行
```
例子：
  - grep "^\s*[^# \t].*$" /etc/ssh/sshd_config
  - egrep -v '^\s*#|^\s*$' /etc/ssh/sshd_config

