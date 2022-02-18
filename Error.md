# 踩坑日记
[toc]{type: "ul", level: [2,3,4]}

## 2021-12-17
今天刚刚搭建好这个踩坑日记，简单记录一下之前踩过的坑，
希望从今天开始能够一直记录，一方面激励自己干活，一方面便于查看之前踩过的坑，不要走弯路
### 之前的踩坑记忆
Centos真的坑，虽然是稳定版本，但是就个人使用而言系统不如Ubuntu受众广泛，解决问题的方式也多，下次一定换Ubuntu
#### X11转发相关问题
- vscode密匙生成之后，.autority的权限会产生一些问题，记得更改权限（.ssh 700 authorized_keys 644）
- 由于校园网ip问题？或者其他原因，每次vscode登录之后转发的display需要重新设置。w查看ip后，export DISPLAY=ip:0.0重新设置即可。
- GLX问题，open3d，PCL的visualization均不可用，不要再尝试了，就算能用那么大的文件打开也卡死了，浪费了整整2天解决这个问题，答案就是解决不了。
#### gcc版本与编译问题
- 现在版本是4.8.5，一般情况下都够用，不够用降低要安装的包的版本，最好不要乱动了
- 版本切换需要将/usr/bin/目录下的gcc，c++，g++三个文件的版本全部换为一致的，不同的版本储存在/usr/local/bin/目录下
- 编译如果有问题可以尝试CMakeLists加入`add_definitions("-std=c++11")`
#### openni问题
既然不要远程的图形显示了，这个问题也不存在了，不要再试图安装了！
## 2021-12-18
c++和Python不一样啊！！！！“；”很重要啊！！！不要再忘记了啊！！！
## 2021-12-20
- 之前换回gcc4.8.5的时候将9.4.0版本的c++和gcc完全抹除了……
- 重装gcc-9.4.0, --prefix /usr/local/gcc，参考链接为 [Centos7安装指定版本gcc](https://www.codeleading.com/article/41615623197/)
- /usr/bin目录下均已更换为9.4.0版本gcc, g++, c++, 但是pcl make报错找不到：`c++: fatal error: cannot execute ‘cc1plus’: execvp: No such file or directory`
- 移动include文件夹，但是貌似只是移动没有激活
- which g++ indicate /bin/g++, means not not correctly installed
- 回退4.8.5，pcl编译成功，但是which g++仍然 /bin/g++
- ~~硬替换方法失败，重回ls链接，参考 [linux（centos）--gcc高低版本切换](https://blog.csdn.net/chenpe32cp/article/details/89481601)~~
- cmake update to 3.6.2, but ccmake became not found，导致pcl编译cmake失败！卸载cmake的时候把pcl依赖cmake文件也误删了……
- 再一次感慨centos是TM垃圾啊！！！！！！！
- 又找不到cc1plus了啊！！！！！！
- 通过yum history undo，参考 [yum history](https://mlog.club/article/4253653) 恢复了pcl，莫名其妙又正常了……应该是和cc1plus没关系,不碰了，崩溃！
## 2021-12-22
### PCL编译问题
- 重新安装了库需要把之前生成的cmakecache等文件删掉（直接删掉build）重新搞，不然永远无法解决问题，不会重新加载
- 从源码编译比较适合centos，毕竟大部分包无法yum安装
- 禁用不合适的、用不到的包有助于make
- ccmake有助于进行详细configuration
## 2021-12-25
### 无法使用GPU问题解决
- 问题报错 `Error: no kernel image is available for execution on the device`
- 多方验证后确认是arch和code版本与此显卡算力(75)不符的原因，此处无法直观看见具体使用的架构，通过重回pcl进行cmake输出得到的信息，之前使用的是86算力框架
- `pcl_find_cuda.cmake`文件不好更改，直接进入ccmake更改`CMAKE_CUDA_FLAGS`后重新编译
- 根据 [NVIDIA](https://docs.nvidia.com/cuda/ampere-compatibility-guide/index.html#building-ampere-compatible-apps-using-cuda-10-0)在此处添加flags确定合适架构，两句**缺一不可**
```
-gencode=arch=compute_75,code=sm_75
-gencode=arch=compute_75,code=compute_75
```
## 2022-02-17
### 可视化桌面
进入机房试图解决未果，主要尝试与问题有
- Ctrl + Alt + F2进入命令行可以用命令行登录操作
- 默认为init 5模式但是无效果
- startx仅有home和trash
- gnome-shell被kill，且无法启动
### 试图直接连接有线网
- eth0在本机名称为em1,其他一致
- seu-wlan更新后更难破译自动登录
- 此路不通