[TOC]

# 常用命令使用

## vim

```bash
# 保存
:wq

# 强制关闭
:q!
```

rpm
```bash
rpm -qa | grep java
```




## echo

```bash
echo "hello world"
```



## cat

```bash
# 查看文件的所有内容
cat filename
```



## ifconfig

```bash
# 查看ip
ifconfig
```



## 文件操作

```bash
# 创建目录

mkdir /data/a

# 创建多级目录
mkdir -p /data/a/b

# 创建文件
touch a

# 修改文件
mv a a.1

# 复制文件
cp a b

# 删除文件
rm -rf a
```

## head 

```bash
head [-n number] 文件
选项与参数：
-n ：后面接数字，代表显示几行的意思
```







## tail

```bash
# 监视filename文件的尾部内容
tail -f filename

# 显示文件的最后20行
tail -n 20 filename
```



# 常用软件安装



## redis



## nginx

### 1. 安装依赖包

```bash
# 一键安装上面四个依赖
yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel
```

### 2.下载并解压安装包

```bash
# 创建一个文件夹
cd /usr/local
mkdir nginx
cd nginx
# 下载tar包
wget http://nginx.org/download/nginx-1.13.7.tar.gz
tar -xvf nginx-1.13.7.tar.gz
```

### 3.安装nginx

```bash
# 进入nginx目录
cd /usr/local/nginx

# 执行命令
./configure

# 执行make命令
make

# 执行make install命令
make install
```

### 4.配置nginx.conf

> #打开配置文件
> vim /usr/local/nginx/conf/nginx.conf

将端口号改成8089，因为可能apeache占用80端口，apeache端口尽量不要修改，我们选择修改nginx端口。

localhost修改为你服务器ip地址。



### 5.启动nginx

```bash
# 启动nginx
/usr/local/nginx/sbin/nginx

# 查看nginx进程是否启动：
ps -ef | grep nginx
```

### 6.图片服务器

```bash
进入nginx.conf 主配置文件中 添加一个location并指定实际路径
location /images/ {
    root  /home/ftpadmin/health/;
    autoindex on;
}  

```

 ![]()

### 6. nginx启停操作

```bash
# 进入安装目录中
cd /usr/local/nginx/sbin

# 启动
./nginx

# 关闭
./nginx -s stop 

#  重启
./nginx -s reload

```



![](https://img-blog.csdnimg.cn/20181030165018819.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdXhpYW83MjM4NDY=,size_16,color_FFFFFF,t_70)
