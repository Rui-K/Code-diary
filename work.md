# 干了啥
[toc]{type: "ul", level: [2,3]}

## 2021-12-17
- 完成了yank note的安装使用，git仓库的管理，Markdown语言的相关设置
- 进行了一部分pcl tutorials的阅读
### PCL tutorials
- [ ]  Object Recognition based on Correspondence Grouping
模型是桌面上有milk盒子（存在download->Google里），预计实现分割和grouping功能？涉及到visualization没有跑，明天改一下代码看看效果。
- Plane model segmentation
必须整个模型是一个平面+离散点，单独应用意义不大，coefficients指代的ax+by+cz+d=0相关参数相对有意义。可以使用RANSAC。
- [x] ~~2021-12-18 19:10~~ Extracting indices from a PointCloud
模型是一个桌子，上有一个盒子（IDM），嵌套使用plane segmentation，明天跑
## 2021-12-18
新建立了一个git仓库，point-prepare，主要用来进行点云的预处理，今天进行的是降采样
### PCL tutorials
- Extracting indices from a PointCloud
    - 盒子的两个主要平面被提取出来了，这一方法应该可以被用于基坑的大平面的提取
    - 经过了**downsample**，这个必须要采用，使用了**voxel**相关
- [ ] Cylinder model segmentation
- [ ] Region growing segmentation
### Downsample
- 降采样Demo
    - 将GZB_3_Cloud_6.pcd进行了降采样，使用的是voxel方法，leaf_size 0.01f
    - pcd尺寸极大降低，但是与之伴随的是精度最大只能到1cm了，有得有失需要后续验证
    - 需要注意pcl读取的pcd文件的格式，voxel仅支持pointcloud2，随后经过转化为pointcloudXYZ输出的
    - pointcloud2的读取速度貌似比XYZ要快，不知道写出的时候是否可以用这一格式
- [ ]  批量化操作, io文件目录方式待探索
- [ ]  voxel方式全局统一变化，后续需要探索knn或者octree，争取保留局部特征变化，变换K值比radius更合适
