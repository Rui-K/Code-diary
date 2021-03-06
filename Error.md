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
- w查看连接的ip
```
export DISPLAY=10.203.236.15:0.0
echo $DISPLAY确认
```

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
## 2022-02-19
### PCL downsample加RGB报错
稳定报错`[pcl::VoxelGrid::applyFilter] Leaf size is too small for the input dataset. Integer indices would overflow.`切小点云无效
## 2022-02-23
### gnome桌面联网问题修复
- gnome被强行终止，界面只有home和trash（误加入net server），重装无效，放弃修复
- 联网弹窗可通过firefox直接打开，虽然没有桌面但是浏览器可以使用，问题解决
- x11转发可以直接转发Firefox，就是非常慢……
- `ssh -YC4c aes128-ctr,aes192-ctr user@server`更改加密方式会快一点，也就只有一点，等待几分钟才会有响应，参考 [Link](https://blog.elleryq.idv.tw/2018/07/ssh-x11-forward.html)
- 非极端情况都是去机房更快一点……
### 连外网机房攻略
- 去机房，4楼BIM实验中心，进门账户名和密码都是1
- 服务器一直是开机状态，黑屏下用键盘`Ctrl+alt+F1(或者F2)`调出命令行界面
- root权限登录
- startx开启桌面模式
- 鼠标右键调出terminal，直接输入`firefox`即可弹出浏览器上网。
## 2022-02-25
### gcc版本切换记录-失败！！
- 原链接信息
```
/usr/bin/gcc -> /opt/rh/devtoolset-8/root/bin/gcc
/usr/bin/g++ -> /opt/rh/devtoolset-8/root/bin/g++
/usr/bin/c++ -> /opt/rh/devtoolset-8/root/bin/c++
```
版本为8.3.1
- 进行软连接，参考 [linux（centos）--gcc高低版本切换](https://blog.csdn.net/chenpe32cp/article/details/89481601)
- 对应操作为`sudo ln -snf /usr/local/bin/c++-4.8.5/c++ /usr/bin/c++`，其他同
- 失败！！！缺少cc1plus！切回

## 2022-03-18
### pointconv tf1.11环境配置报错
`tensorflow.python.framework.errors_impl.NotFoundError: /home/kangrui/pointconv/tf_ops/3d_interpolation/tf_interpolate_so.so: undefined symbol: _ZN10tensorflow8internal21CheckOpMessageBuilder9NewStringEv`

参考[git-issue评论](https://github.com/charlesq34/pointnet2/issues/48#issuecomment-608135179)找到未链接原因，但是连上之后又引发了其他问题，初步判断是gcc版本问题，多次安装gcc不同版本后原动态链接库应该还是4.8的旧版本的，现卸载conda安装的tf，转pip安装

pip无法解决问题，且因cuda版本报错，转回conda，解决libc++版本不足问题，参考[博客园libstdc](https://segmentfault.com/a/1190000041012397),下载在Downloads里，已替换解决问题

出现新的`tensorflow.python.framework.errors_impl.NotFoundError: /home/kangrui/pointconv/tf_ops/3d_interpolation/tf_interpolate_so.so: undefined symbol: _ZN10tensorflow7strings6StrCatERKNS0_8AlphaNumE`

失败告终，回Pointnet2环境适配
## 2022-03-28
### git 22 port time out
参考[github官方](https://docs.github.com/en/authentication/troubleshooting-ssh/using-ssh-over-the-https-port)

## 2022-03-29
### evaluate class 0 issue 
- total_correct_class[0]一直为0，格构柱和圆柱的识别效果也不好，初步怀疑柱的label有问题
- train、val的class 0均无问题
- ` # batch_pred_label = np.argmax(pred_val[:, :, 1:], 2) + 1 #BxN why + 1??`scannet的class应该是从0开始的，去掉+1尝试
- 最后两个数字都变成0了，而且数据非常差
- pred_label全部+1，whole_scene_label从0开始没有变
- 改回原来的形式，暂时舍弃class 0
## 2022-03-30
### open3d显示
- 颜色只支持0-1,255的rgb要归一