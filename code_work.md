# code work timeline
[toc]{type: "ul", level: [2,3]}

## 2021-12-17
- 完成了yank note的安装使用，git仓库的管理，Markdown语言的相关设置
- 进行了一部分pcl tutorials的阅读
### PCL tutorials
- [x] ~~2021-12-19 10:55~~  Object Recognition based on Correspondence Grouping
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
- [x] ~~2021-12-20 10:46~~ Cylinder model segmentation
- [x] ~~2021-12-20 15:34~~ Region growing segmentation
### Downsample
- 降采样Demo
    - 将GZB_3_Cloud_6.pcd进行了降采样，使用的是voxel方法，leaf_size 0.01f
    - pcd尺寸极大降低，但是与之伴随的是精度最大只能到1cm了，有得有失需要后续验证
    - 需要注意pcl读取的pcd文件的格式，voxel仅支持pointcloud2，随后经过转化为pointcloudXYZ输出的
    - pointcloud2的读取速度貌似比XYZ要快，不知道写出的时候是否可以用这一格式
- [x] ~~2022-02-21 11:10~~  批量化操作, io文件目录方式待探索
**无必要性**
- [x] ~~2022-02-21 11:10~~  voxel方式全局统一变化，后续需要探索knn或者octree，争取保留局部特征变化，变换K值比radius更合适
**无必要性**
## 2021-12-19
### PCL tutorials
- Object Recognition based on Correspondence Grouping
需要有模型的原型然后进行匹配，与主题内容不符合，后续不再考虑
- Random Sample Consensus model 即RANSAC
应用在单体上的ransac，是cylinder的前序程序
## 2021-12-20
### PCL tutorials
- Cylinder model segmentation
    - 演示单一圆柱和单一平面提取，多重圆柱多重平面提取此处未提及
    - 应在Extracting indices from a PointCloud中寻求indices的相关操作
- Region growing
    - 测试Demo无问题，生成区域分隔，输出带颜色的区域，也可以indices单个输出，但是未经过测试，理论上无问题
    - 输出带颜色区域时，文件大小预计膨胀到原来的3倍，对于基坑点云来说过大，后续还是考虑分开输出
    - 利用downsampled基坑点云测试，运算极度缓慢，且CPU使用率仅100%
    - 后续考虑使用~~gpu~~和多线程CPU加速，见下图.`<pcl/features/normal_3d.h>`应对应变更为`#include <pcl/features/normal_3d_omp.h>`
    ![OpenMP](https://img-blog.csdnimg.cn/20200821150538743.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0JKTXpoZW5n,size_16,color_FFFFFF,t_70#pic_center)
    - 使用OpenMP可能出现的问题参考 [线程数设置](https://www.cxybb.com/article/lch_vison/80806454)
## 2021-12-22
### GPU版本PCL尝试安装
- 参考 [CentOS 7升级gcc版本](https://www.cnblogs.com/jixiaohua/p/11732225.html)将gcc升级到8.3.1
    - 需要开启对话时每次用`scl enable devtoolset-8 bash`开启，稳定后再替换
    - 此版本满足pcl-gpu要求，后续替换也参考这个网页来
    - 已替换，额外替换了c++
- 按照 [PCL](https://pcl.readthedocs.io/projects/tutorials/en/latest/gpu_install.html#gpu-install)网页要求配置clang，目前版本3.4.2，需要6以上
    - 参考 [centos安装clang9](https://www.jianshu.com/p/d7905fe696b0)安装
    - 安装完毕卸载了clang3.4.2，但是clang --version找不到clang，路径/bin/clang不存在，根据记录clang9.4.0在/usr/local/bin里面
    - `sudo ln -s /usr/local/bin/clang /usr/bin/clang`链接上了
- Boost安装
    - 目前1.53.0，需要至少1.65.0，目录在/usr/include
    - **移除原版本boost的同时移除了flann-devel和pcl-devel**
    - 参考 [Centos下编译升级安装Boost](https://www.cnblogs.com/xuyuan77/p/10940795.html)
- FLANN应该被安装，先编译pcl，再考虑gpu，已通过源码编译安装flann
- 源码编译PCL，出现cmake版本使用2.8问题，在cmake时直接加cmake3可以解决
## 2021-12-23
### PCL编译 
- 报错Boost找不到，参考以下删除build文件重新cmake后解决
> you must remove the CMakeCache.txt file and CMakeFiles directory from your build directory. Otherwise, CMake will attempt to use cached options which may not include your new flags.
- 报错找不到CUDA，手动指定，`cmake -DCMAKE_CUDA_COMPILER:PATH=/usr/local/cuda/bin/nvcc`解决
- make报错，在ccmake里面禁用了openni
- 测试代码时kinect相关报错，已禁用

**PCL-GPU版编译成功啦，目前是PCL-1.12**

### PCL-GPU代码测试
- gpu需要用到的头文件
```cpp
#include <pcl/gpu/containers/initialization.h>
#include<pcl/gpu/features/features.hpp>
```
- 输出gpu信息，并验证gpu可用
```cpp
pcl::gpu::setDevice (device);
pcl::gpu::printShortCudaDeviceInfo (device);
```
- normal
参考 [test_normals.cpp](https://github.com/PointCloudLibrary/pcl/blob/master/gpu/features/test/test_normals.cpp)
都在pcl/gpu/features/test/目录下

## 2021-12-24
### 圆柱提取
- GZB_3_Cloud_4.pcd有6个完整的柱子，较适合
    - 不明原因有明显地面缺口，柱子不到顶，下半部分缺失一部分，完整提取后不知情况如何
    - 另有混凝土横梁支撑和方形钢柱
- 应先downsample，KNN较合适
- extract_indices争取一个文件输出所有柱，但先试验单柱输出
- [x] ~~2022-01-29 13:57~~ cylinder的seg模型不太一致，需要normal等参数，修好gpu尝试 
### 使用GPU计算normal报错
- `Error: no kernel image is available for execution on the device /home/kangrui/pcl/gpu/features/src/normal_3d.cu:249`
- 可能与cuda版本与gpu计算能力不匹配，参考 [deviceQuery](https://www.cnblogs.com/wmr95/articles/8846749.html)查看 `CUDA Capability Major/Minor version number:    7.5`
- nvcc架构问题参考 [nvcc的code、arch、gencode选项](https://zhengqm.github.io/blog/2018/12/07/cuda-nvcc-tips.html)
- ~~也可能是CMakeLists.txt写的不对……~~ CMakeLists.txt没有问题，该找到的都找到了
- 利用 [Pytorch](https://blog.csdn.net/weixin_30897079/article/details/96581542?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.nonecase)检查，Cuda正常使用无错误，只在PCL库发生，或许版本不兼容？
- bug已解决，详见Error.（2021-12-25）,gpu真快……输入进去几秒钟就算完了……
## 2021-12-25
### 尝试运行其他gpu/example/seg
- 代码并不是完整的，解决了一个`pcl::gpu::EuclideanClusterExtraction<pcl::PointXYZ> gec;`未指定数据类型问题
- 出现cuda方面错误，但是暂时不影响normal使用，搁置
```
terminate called after throwing an instance of 'thrust::system::system_error'
  what():  radix_sort: failed on 2nd step: cudaErrorInvalidConfiguration: invalid configuration argument
Aborted (core dumped)
```
### cylinder尝试
- 换一种normal方案
## 2022-01-29
### Normal 计算问题结果
- gpu环境可以使用，但是upload和download问题非常多，且网上缺少有效指导，放弃
- CPU多线程使用的OMP可以替代，速度非常快，要```#include <pcl/features/normal_3d_omp.h>```
### Cylinder提取
- normal计算在OMP后速度极快
- cylinder的seg计算非常慢，单核单进程，寻求OMP解决方案（单个column计算速度几分钟，可以忍受）
- 参考extract_indices时不仅要投入normal，还要考虑normal与剩余点数量不对应问题
- seg无OMP提取一个柱子要2400s左右,现尝试omp---失败
### OpenMP
需要在CMakeLists里面添加如下代码
```CMakeLists
FIND_PACKAGE( OpenMP REQUIRED)

if(OPENMP_FOUND)
message("OPENMP FOUND")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")
endif()
```
## 2022-02-17
### Cylinder提取
#### 出现问题
- 提取数量远多于实际柱数量，上面方形支撑被提取出来，其中一个先于柱子提取
- 柱子周边圆形钢构件没有被提取出来
#### 拟解决方案
- 限制半径更严格
- 根据方程参数直接筛掉不合格的半径柱
- 对柱周边点tolerance提高
## 2022-02-18
### Cylinder改进
1. `seg.setRadiusLimits (0.8, 1.2);`
2. `seg.setDistanceThreshold (0.2);`
3. 写出logfile记录coefficient，参考[菜鸟教程文件写入](https://www.runoob.com/cplusplus/cpp-files-streams.html)
4. 无法从点的数量判断是否圆柱,只能看log里面对应参数是否有变化
5. PointT换成PointXYZRGB,不使用downsampled文件
## 2022-02-19
### downsample报错
- `[pcl::VoxelGrid::applyFilter] Leaf size is too small for the input dataset. Integer indices would overflow.`随后未经降采样原数据输出
- 参考解决方式 [Link](https://codeleading.com/article/49103838657/)
#### downsample解决方式
1. 尝试利用ExtractIndices分块储存cloud，参考 [indices](https://stackoverflow.com/questions/65463235/how-to-create-pclpointindices-from-stdvectorint-with-pclpoint-cloud-lib)
2. c++的vector使用，参考 [菜鸟教程](https://www.runoob.com/w3cnote/cpp-vector-container-analysis.html) **WARNING！！** 必须使用.begin(),否则输入纯数字会变成另一种赋值的用法
3. [在线c++](https://c.runoob.com/compile/12/)
4. 稳定报错，pcl库bug，现放弃此方式留存思路代码
```c++
#include <iostream>
#include <ctime>
#include <pcl/ModelCoefficients.h>
#include <pcl/io/pcd_io.h>
#include <pcl/point_types.h>
#include <pcl/sample_consensus/method_types.h>
#include <pcl/sample_consensus/model_types.h>
#include <pcl/segmentation/sac_segmentation.h>
#include <pcl/filters/voxel_grid.h>
#include <pcl/filters/extract_indices.h>
#include<vector>

using namespace std;
typedef pcl::PointXYZRGB PointT;
int main()
{
    pcl::PointCloud<PointT>::Ptr cloud(new pcl::PointCloud<PointT>), cloud_tmp(new pcl::PointCloud<PointT>), cloud_tmp_f(new pcl::PointCloud<PointT>),cloud_filtered (new pcl::PointCloud<PointT>);
    pcl::PointIndices::Ptr ind (new pcl::PointIndices), ind_tmp (new pcl::PointIndices);
    pcl::ExtractIndices<PointT> extract;

    string filename = "/home/kangrui/Data/FP_point/GZB_3_Cloud_4";
    clock_t start_t, end_t;
    start_t=clock();
    pcl::PCDReader reader;
    if(reader.read(filename + ".pcd", *cloud) == -1){
        PCL_ERROR ("Couldn't read file  \n");
        return(-1);
    }
    end_t=clock();
    cout << filename << "  Successfully Loaded" << endl
                <<"Reading time: "<<(double)(end_t-start_t)/CLOCKS_PER_SEC << "s" << endl
                <<"point cloud shape: "<< cloud->width * cloud->height
                <<endl;

    //seg cloud into small block
    int nr_points = (int) cloud->size ();
    vector<int> index, index_tmp;
    for (int i=0; i<nr_points; i++){
        //index.push_back(i);
        ind->indices.push_back(i);
    }
    int K = 10000;
    int B = (int) nr_points/K;
    int start, end;
    start = 0;
    end = B -1 ;
    //cerr << "B: " <<B<<endl;
    pcl::VoxelGrid<PointT> sor;
    sor.setLeafSize (0.01f, 0.01f, 0.01f);

    for (int i = 0; i < K; ++i){
        //cerr << "start = "<<start<<" end = "<<end<<endl;
        //index_tmp.assign();
        ind_tmp->indices.assign(ind->indices.begin()+start,ind->indices.begin()+end);
        start = end;
        end = end + B;
        extract.setInputCloud (cloud);
        extract.setIndices (ind_tmp);
        extract.setNegative (false);
        extract.filter (*cloud_tmp);
        //downsample start
        start_t = clock();
        sor.setInputCloud (cloud_tmp);
        sor.filter (*cloud_tmp_f);
        end_t = clock();
        cerr << "PointCloud block " <<i<<"-- original points: "<< cloud_tmp->size() << endl <<
        "After filtering: " << cloud_tmp_f->size() << endl
        <<"Time cost: "<<(double)(end_t-start_t)/CLOCKS_PER_SEC << "s" << endl<<endl;
        //concatenate points 
        *cloud_filtered += *cloud_tmp_f;
    }

    
    
    pcl::PCDWriter writer;
    writer.write<PointT> (filename+"_RGB_downsampled.pcd", *cloud_filtered, false);
    
    return(0);
}
```
### Cylinder针对未降采样3_4参数调整提取
- 目前参数提取失败,未分清r和d,半径和直径的区别....改为0.4-0.6
- 锁定半径放大误差阈值后 意外提取了横向支撑……
- coefficients定义如下， [参数详解](https://blog.csdn.net/wokaowokaowokao12345/article/details/51457217)
```
point_on_axis.x : the X coordinate of a point located on the cylinder axis
point_on_axis.y : the Y coordinate of a point located on the cylinder axis
point_on_axis.z : the Z coordinate of a point located on the cylinder axis
axis_direction.x : the X coordinate of the cylinder's axis direction
axis_direction.y : the Y coordinate of the cylinder's axis direction
axis_direction.z : the Z coordinate of the cylinder's axis direction
radius : the cylinder's radius
```

### 其他部位提取方式
- 意外提取，可后续持续改进加手动-->根据圆柱参数直接卡死
- 基于颜色的region grow估计不行，颜色靠太近了，可以后续欧几里德聚类试一下,但是钢结构和支撑连在一起了

## 2022-03-07
### pointconv数据库建立
#### pickle
- 参考 [csdn-pickle](https://blog.csdn.net/coffee_cream/article/details/51754484)
- 按顺序储存，按顺序读取，可以压缩
- 原scannet_dataset
```python
    pickle_out = open("scannet_%s_rgb21c_pointid.pickle"%(split),"wb")
    pickle.dump(scene_data, pickle_out, protocol=0)
    pickle.dump(scene_data_labels, pickle_out, protocol=0)
    pickle.dump(scene_data_id, pickle_out, protocol=0)
    pickle.dump(scene_data_num, pickle_out, protocol=0)
    pickle_out.close()
```
输出为
```
(77012, 6) [array([[5.3242141e-01, 4.5172734e+00, 2.6304942e-01, 1.0100000e+02,
        1.0700000e+02, 9.0000000e+01],
       [5.3404164e-01, 4.5520892e+00, 2.6230201e-01, 8.8000000e+01,
        8.3000000e+01, 7.8000000e+01],
       [5.4477900e-01, 4.4811263e+00, 1.7396316e-01, 3.9000000e+01,
        3.9000000e+01, 3.5000000e+01],
       ...,
       [7.5318260e+00, 4.8856416e+00, 2.5779479e+00, 2.4200000e+02,
        2.1200000e+02, 1.4400000e+02],
       [7.5255036e+00, 4.9194841e+00, 2.6109233e+00, 1.7500000e+02,
        1.6100000e+02, 1.2700000e+02],
       [7.5323153e+00, 4.8880706e+00, 2.6260881e+00, 1.7900000e+02,
        1.6400000e+02, 1.2800000e+02]], dtype=float32)]
(77012,) [array([14., 14., 14., ...,  1.,  1.,  1.])]
(77012,) [array([    0,     1,     2, ..., 81366, 81367, 81368])]
[81368]#过滤了unlabelled数据，因此点数与实际使用的数据数不一致
```
- pickle储存的数据格式应为
    - `data->list[array(n,xyzrgb)]`
    - `data_label->list[array(n)]`
    - `data_id->list[array(n)]`
    - `point_num->list[num_before_remove]`
- 代码完成，输出的为np.array，无list在外
## 2022-03-18
### 试运行
- pickle读取时加了list在整体数据外
- tf 1.11环境不一样，新建一个，[ERROR！](./Error.md#pointconv-tf1.11环境配置报错)
- 自动换行 alt+z
- 跑通了，用小一号的_s进行测试
## 2022-03-19
### 代码结构差异
- 原代码的结构是一个room是一个pointset，送入多个room为一个batch
- 需要改为小块送入，从数据准备这一块就开始变更，使用原码的小块`[array(),array()]`模式，**学习方块**
### 代码拟调整方式
- CLOI里面是1x1x1立方米方块，大范围场景拟用5x5x5
- pickle已生成完毕
### 开跑
- batch< 8，否则挤爆无法运行
## 2022-03-20
### 500epoch结果
- 结果不理想,eval才60多
- 忘记在pickle里面把点数少的训练集删除
- 再跑
- screen查看屏幕输出`ctrl+a, ]`

## 2022-03-23
### BIMServer搭建
- 已配置完成，参考[BIMServer入门](https://blog.brucekong.com/posts/3869274485/)
- 插件已安装ifcopenshell等
- 离线插件安装参考[opensourceBIM](https://github.com/opensourceBIM/BIMserver/wiki/Installing-without-internet-connection)
- ifcengine无法安装，不要放入离线插件中

## 2022-03-24
### 排查evaluate计算IoU问题
```
        for l in range(NUM_CLASSES):
            total_seen_class[l] += np.sum((whole_scene_label==l))
            total_correct_class[l] += np.sum((pred_label==l) & (whole_scene_label==l))
            total_iou_deno_class[l] += np.sum(((pred_label==l) | (whole_scene_label==l)) & (whole_scene_label > 0))


        print(total_correct_class)
        print(total_iou_deno_class)
        print(total_seen_class)
```
输出结果为
```
visualize 33 40.ply ...
[0, 0, 0, 0, 0, 0, 0, 0, 0]
[0, 0, 0, 0, 0, 0, 0, 0, 0]
[6325484, 0, 0, 0, 0, 0, 0, 0, 0]
```
但是根据实际输出标签的结果来看，训练和eval没有问题
<img src=./FILES/code_work.md/1.png width="200" alt="predict">
<img src=./FILES/code_work.md/2.png width="200" alt="ground truth">

得到的IoU果然全部为0
```
eval point avg class IoU: 0.000000
Each Class IoU:::
Class 1 : 0.0000
Class 2 : 0.0000
Class 3 : 0.0000
Class 4 : 0.0000
Class 5 : 0.0000
Class 6 : 0.0000
Class 7 : 0.0000
Class 8 : 0.0000
```
**数据集test在打包pickle的时候没有输入label！！！**

dump2022_03_25_03_09_00包记录的为test数据集结果

## 2022-03-30
### RANSAC code
- open3d用于可视化
- [pyransac3d](https://pypi.org/project/pyransac3d/)用于进行形状提取
- RANSAC取平面之后凸包取轮廓然后再取线

## 2022-04-01
### BoundaryEstimation
- re边界估计半径、reforn法线估计半径均为0.3，角度阈值PI/4时很多边界没提取出来
<img src=./FILES/code_work.md/3.png width="300" alt="boundary_1">
- 切换kdtree search，re 0.15，reforn 30，PI/2。只是多了噪点，无有效边界提出。
- [此链接](https://www.xuehua.tw/a/5ec91bfb868e1a463e4724f4)说Ksearch的值越大越好，下一次试一下，目前是50。--不理想，噪点多，但是也提取了一些有用的线。
- 跑一次要50分钟
#### 2022-04-02
- 效果仍然不好，考虑拆分一个内支撑，一方面减少时间，另一方面看看能不能减少干扰
### Plane extraction + BoundaryEstimation
- 效果非常好！
<img src=./FILES/code_work.md/4.png width="300" alt="boundary_2">
- re=0.2; reforn=64; thresh 0.03，iteration 3500
- BoundaryEstimation原理 [angle criterion](https://onlinelibrary.wiley.com/doi/10.1111/j.1467-8667.2012.00761.x)，没有找到最原始的，[pcl paper](https://ieeexplore.ieee.org/abstract/document/5980567)
- 算法描述用第一篇文章，引用用pcl那篇
### Line and shape
>步骤(5)中生成建筑立面的方法为:首先统计出过滤后的激光点中Z坐标的最大值和最小值，分别为Zmax和Zmin，然后根据多线段P1P2......Pn的节点坐标和Zmax、Zmin值，得到多线段P1P2……Pn中任一线段PiPi+i的立面中的四个顶点的三维坐标，依次为(Xi，Yi，Zmin)、(Xi+i，Yi+i，Zmin)、(Xi+i, Yi+i,Zmax)(Xi, Yi,Zmax)，其中 I < i < n_l，根据四个顶点坐标，生成任一线段P1Pw的立面，按此循环直至生成多线段P1P2……立面。
