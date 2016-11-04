---
title: centos tar压缩与解压缩命令大全
author: funchs
tags: [工具]
categories: [original]
date: 2016-11-04 12:00:00
---

## centos tar压缩与解压缩命令大全
管理centos服务器的时候常会对文件进行一些常规操作，除了ftp之外了解在ssh下必要的文件操作命令那也是必不可少的，以下摘录一些常用的文件操作命令:

### 文件操作:
```shell
ls #查看目录中的文件
ls -F #查看目录中的文件
ls -l #显示文件和目录的详细资料
ls -a #显示隐藏文件
ls *[0-9]* #显示包含数字的文件名和目录名
rm go.tar #&nbsp;删除go.tar文件
find mt.cgi #查找文件名为mt.cgi的文件
df ?h #查看磁盘剩余空间
find / -name xxx -print #查找xxx文件
```

### 压缩与解压缩
```shell
tar xvf wordpress.tar #解压tar格式的文件
tar -tvf myfile.tar #查看tar文件中包含的文件&nbsp;
tar cf toole.tar tool #把tool目录打包为toole.tar文件
tar cfz xwyme.tar.gz tool #把tool目录打包且压缩为xwyme.tar.gz文件，
#因为.tar文件几乎是没有压缩过的，MT的.tar.gz文件解压成.tar文件后差不多是10MB&nbsp;
tar jcvf /var/bak/www.tar.bz2 /var/www/ #创建.tar.bz2文件，压缩率高
tar xjf www.tar.bz2 #解压tar.bz2格式
gzip -d ge.tar.gz #解压.tar.gz文件为.tar文件
unzip phpbb.zip #解压zip文件，windows下要压缩出一个.tar.gz格式的文件还是有点麻烦的
bunzip2 file1.bz2 #解压一个叫做 ‘file1.bz2′的文件
bzip2 file1 #压缩一个叫做 ‘file1′ 的文件
gunzip file1.gz #解压一个叫做 ‘file1.gz’的文件
gzip file1 #压缩一个叫做 ‘file1′的文件
gzip -9 file1 #最大程度压缩
rar a file1.rar test_file #创建一个叫做 ‘file1.rar’ 的包
#同时压缩 ‘file1′, ‘file2′ 以及目录 ‘dir1′
rar a file1.rar file1 file2 dir1 
rar x file1.rar #解压rar包
unrar x file1.rar #解压rar包
tar -cvf archive.tar file1 #创建一个非压缩的 tarball
#创建一个包含了 ‘file1′, ‘file2′ 以及 ‘dir1′的档案文件
tar -cvf archive.tar file1 file2 dir1 
tar -tf archive.tar #显示一个包中的内容
tar -xvf archive.tar #释放一个包
tar -xvf archive.tar -C /tmp #将压缩包释放到 /tmp目录下
tar -cvfj archive.tar.bz2 dir1 #创建一个bzip2格式的压缩包
tar -xvfj archive.tar.bz2 #解压一个bzip2格式的压缩包
tar -cvfz archive.tar.gz dir1 #创建一个gzip格式的压缩包
tar -xvfz archive.tar.gz #解压一个gzip格式的压缩包
zip file1.zip file1 #创建一个zip格式的压缩包
#将几个文件和目录同时压缩成一个zip格式的压缩包
zip -r file1.zip file1 file2 dir1 
unzip file1.zip #解压一个zip格式压缩包
```