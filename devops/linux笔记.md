操作系统概述：

进程与线程：https://mp.weixin.qq.com/s?__biz=MzkwMDE1MzkwNQ==&mid=2247496008&idx=1&sn=b4c6bfc75abfa8a45843bc3c2221b6ad&source=41#wechat_redirect

linux多用户：https://www.cnblogs.com/lemaden/p/10188848.html

环境变量配置：https://www.cnblogs.com/youyoui/p/10680329.html



#文件系统

## 目录

- https://blog.51cto.com/u_13452945/2147428
- https://www.linuxprobe.com/linux-directory.html
- `ls -l`查看文件属性和文件间关系

```
# debian linux 10.8
/boot	存放引导加载器使用的文件
/sys 存放系统文件
/bin 存放系统的常用命令
/etc 存放系统管理所需的配置文件和子目录
/opt 存放第三方软件(默认安装路径)
/usr unix software resource 一般来自linux发行版
	/bin 存放所有用户命令
	/sbin 存放超级用户的一些管理程序
	/include 存放linux下开发和编译应用需要的头文件
	/lib 存放常用的动态链接共享库和软件包的配置文件
	/share 
	/src 存放linux开放的源代码
	/local 存放普通用户的应用程序和文件
		/bin 存放本地增加的命令
		/lib 存放本地增加的库
		/etc 
		/include 
		/share
		/src 
/dev 作为访问外部设备的接口，而非存放设备的驱动程序
/media 存放自动挂载的多媒体设备，如U盘
/mnt 存放临时挂载的文件系统，被超级用户使用
/srv 存放服务启动后待访问的数据目录
/run 存放系统运行时的临时文件
/tmp 存放程序执行时产生的临时文件
/var 存放经常被修改的文件
	/local 存放/usr/local中程序的可变数据
	/lib 存放系统运行时的可变数据
	/log 存放程序的日志
	/tmp 存放更大或需存在较长时间的临时文件
/proc 系统内存的映射，虚拟目录
/lost+found 存放非正常关机时无家可归的文件
/root 超级用户的主目录
/home 存放普通用户的主目录
	/user1
	/user2
/sbin 存放超级用户的命令
```

# 命令

## 文件目录命令

```shell
# 显示当前所在目录 print working directory
pwd
# 检查文件或目录是否存在
ls
# 切换目录 change directory
cd
# 创建文件
touch
# 创建目录 make directory
mkdir
# 查看文件信息
cat
# 查看文件开头
head
# 查看文件结尾
tail
# 统计文件内容信息
wc
# 复制 copy
cp
# 移动文件 move
mv
# 删除文件或目录 remove
rm
# 删除目录 remove directory
rmdir
# 
find
# 
rename
# 
rmdir
# 打包(不支持压缩)
tar
# 压缩
gzip
bzip2
```

## 用户和组命令

```shell
# 新建用户
useradd
# 删除用户
userdel
# 

# 
```

