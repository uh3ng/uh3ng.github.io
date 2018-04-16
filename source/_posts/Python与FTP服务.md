---
title: Python与FTP服务
date: 2018-04-12 10:38:28
tags: Python
---
## FTP服务端
使用python的pyftpdlib库可以创建一个简单的FTP服务，首先需要安装这个库。安装很简单，打开命令行用pip直接安装就可以：

```shell
pip install pyftpdlib  
#或者  
#python -m pip install pyftpdlib  
```
FTP分为被动模式和主动模式，这里不细说了，下面的例子采用的是被动模式的代码

```python
from pyftpdlib.authorizers import DummyAuthorizer  
from pyftpdlib.handlers import FTPHandler  
from pyftpdlib.servers import FTPServer  
  
#创建FTP用户验证  
authorizer = DummyAuthorizer()  
#添加一个用户，依次为用户名，登录口令，目录，权限  
authorizer.add_user('username', 'your_pass', '用户目录', perm='elradfmw')  
#创建句柄  
handler = FTPHandler  
handler.authorizer = authorizer  
#FTP被动模式下的端口号范围，主动模式不用设置  
handler.passive_ports = range(2000, 2333)  
#绑定监听的ip和端口号  
server = FTPServer(('192.168.XX.XXX', 21), handler)  
#开启服务  
server.serve_forever()  
```
## FTP客户端
客户端的实现也不算复杂，本文只是简单的实现了文件的上传和下载功能
```Python
import ftplib  
import sys  
  
#获取服务器的ip地址（如192.168.1.107），使用sys.argv可以从命令行参数里面获取  
if len(sys.argv) < 2:  
    tmp = input("please input server address:")  
    sys.argv.append(tmp)  
server_address = sys.argv[1]  
#创建FTP实例，并显示欢迎界面  
ftp = ftplib.FTP(server_address)  
print(ftp.getwelcome())  
#登录，输入服务器里添加过的用户名和口令  
ftp.login('user', 'pass')  
  
#文件上传  
def upload(fname):  
    fd = open(fname, 'rb')  
    new_name = input("input new name:")  
    #以二进制的形式上传  
    ftp.storbinary("STOR %s" % new_name, fd)  
    fd.close()  
    print("upload finished")  
      
#文件下载  
def download(fname):  
    #构建文件的存储路径，这里用的是D盘,可以自行设置  
    new_path = "D:\\FTPdownload\\" + fname  
    fd = open(new_path, 'wb')  
    #以二进制形式下载，注意第二个参数是fd.write，上传时是fd  
    ftp.retrbinary("RETR %s" % fname, fd.write)  
    fd.close()  
    print("download finished")  
  
def main():  
    #选择操作，上传、下载、退出  
    op = input("what do you want?(u/d/q)")  
    if op == "u":  
        #输入文件完整路径，必要时可以用绝对路径  
        fname = input("input the file of path:")  
        upload(fname)  
    elif op == "d":  
        fname = input("input the file name:")  
        download(fname)  
    else:  
        print("quit now!")  import ftplib  
import sys  
  
#获取服务器的ip地址（如192.168.1.107），使用sys.argv可以从命令行参数里面获取  
if len(sys.argv) < 2:  
    tmp = input("please input server address:")  
    sys.argv.append(tmp)  
server_address = sys.argv[1]  
#创建FTP实例，并显示欢迎界面  
ftp = ftplib.FTP(server_address)  
print(ftp.getwelcome())  
#登录，输入服务器里添加过的用户名和口令  
ftp.login('user', 'pass')  
  
#文件上传  
def upload(fname):  
    fd = open(fname, 'rb')  
    new_name = input("input new name:")  
    #以二进制的形式上传  
    ftp.storbinary("STOR %s" % new_name, fd)  
    fd.close()  
    print("upload finished")  
      
#文件下载  
def download(fname):  
    #构建文件的存储路径，这里用的是D盘,可以自行设置  
    new_path = "D:\\FTPdownload\\" + fname  
    fd = open(new_path, 'wb')  
    #以二进制形式下载，注意第二个参数是fd.write，上传时是fd  
    ftp.retrbinary("RETR %s" % fname, fd.write)  
    fd.close()  
    print("download finished")  
  
def main():  
    #选择操作，上传、下载、退出  
    op = input("what do you want?(u/d/q)")  
    if op == "u":  
        #输入文件完整路径，必要时可以用绝对路径  
        fname = input("input the file of path:")  
        upload(fname)  
    elif op == "d":  
        fname = input("input the file name:")  
        download(fname)  
    else:  
        print("quit now!")  
    ftp.quit()  
  
if __name__ == '__main__':  
    main()  
    ftp.quit()  
  
if __name__ == '__main__':  
    main()  
```

## 一些ftplib库的相关操作
```Python
ftp.cwd(pathname)           # 设置FTP当前操作的路径  
ftp.dir()                   # 显示目录下所有目录的信息  
ftp.nlst()                  # 获取目录下的文件  
ftp.mkd(pathname)           # 新建远程目录  
ftp.rmd(dirname)            # 删除远程目录  
ftp.pwd()                   # 返回当前所在位置  
ftp.delete(filename)        # 删除远程文件  
ftp.rename(old_name, new_name)    #将fromname改为toname  
ftp.storbinary('STOR filename.txt',file_handel,[bufsize])  # 上传目标文件,最后一个参数可以不填  
ftp。retrbinary('RETR filename.txt',file_handel,[bufsize])  # 下载FTP文件  
```