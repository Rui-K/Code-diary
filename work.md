# 干了啥
[toc]{type: "ul", level: [2,3]}
## 2021-12-17
- 完成了yank note的安装使用，git仓库的管理，Markdown语言的相关设置
- 进行了一部分pcl tutorials的阅读
### PCL tutorials
- 3D Object Recognition based on Correspondence Grouping
模型是桌面上有milk盒子（存在download->Google里），预计实现分割和grouping功能？涉及到visualization没有跑，明天改一下代码看看效果。
- Plane model segmentation
必须整个模型是一个平面+离散点，单独应用意义不大，coefficients指代的ax+by+cz+d=0相关参数相对有意义。可以使用RANSAC。
- Extracting indices from a PointCloud
模型是一个桌子，上有一个盒子（IDM），嵌套使用plane segmentation，明天跑