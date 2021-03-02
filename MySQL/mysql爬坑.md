# linux
1. mac下使用brew安装了mysql，运行`sudo mysql.server start`时，报错`The server quit without updating PID file`.
   - 查看是否是默认端口占用导致.
   - 查看是否是权限问题导致. 运行权限修改命令`sudo chmod -R a+rwx /usr/local/var/mysql`
   - 查看是否是my.conf配置问题.
     -  `mysql --help | grep 'Default options' -A 1`查看my.conf所在路径. brew安装的mysql默认无my.cnf，直接使用默认配置启动.
     - mysql按以下顺序加载：`/etc/my.cnf -> /etc/mysql/my.cnf -> /usr/local/etc/my.cnf -> ~/.my.cnf`

2. 重新安装mysql
   - https://blog.csdn.net/weixin_44037416/article/details/94553405

# windows
