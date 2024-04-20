# nilmtk和nilmtk-contrib环境配置



## 前言

以下配置教程均是一步步记录问题并实时解决的，配置过程讲解稍微有些冗余，但为了配置过程的连续性和bug的完整性，教程将搭配图片信息进行讲解。建议先简单过一遍完整流程，第二遍再根据教程进行环境配置。



## 1.准备工作

- 从`https://github.com/nilmtk/nilmtk`和`https://github.com/nilmtk/nilmtk-contrib`克隆以下代码包：

`git clone https://github.com/nilmtk/nilmtk-contrib.git`

`git clone https://github.com/nilmtk/nilmtk.git`

`git clone git@github.com:nilmtk/nilm_metadata.git `

- 进入文件目录，解压压缩包，确保文件如下：

![image-20240311112530757](.\pic\image-20240311112530757.png)

- 查看和添加`conda`镜像

```
# 显示检索路径
conda config --set show_channel_urls yes
# 查看源：
conda config --show-sources

# 添加镜像：建议添加和conda-forge,nilmtk和tensorflow相关的镜像
conda config --add channels nilmtk
conda config --add channels conda-forge

# 以及常用镜像
conda config --add channels http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
conda config --add channels http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
conda config --add channels http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r

```



- **打开科学上网工具，切换全局模式，确保下载速度和访问正常**！！



## 2.nilmtk配置

- 使用`conda`进入`nilmtk-master`目录下，执行以下命令：（请再次确保科学上网工具以全局模式运行）

`conda env create -f environment.yml`

安装完成，该命令会连同`nilm_metadata`一起安装，省去了命令行构建。

![image-20240311114556203](.\pic\image-20240311114556203.png)

查看环境：

`conda activate (your env)`

`conda list`

![image-20240311114930039](.\pic\image-20240311114930039.png)

- 环境测试：载入数据

![image-20240311115306200](.\pic\nilmtk和nilmtk-contrib环境配置.md)

```
常用的nilmtk数据集下载地址：
REDD:(该地址已失效)
http://redd.csail.mit.edu/

AMPds2:
https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/FIE0S4

UKDALE:
https://data.ukedc.rl.ac.uk/browse/edc/efficiency/residential/EnergyConsumption/Domestic/UK-DALE-2015

REFIT:
https://repository.lboro.ac.uk/articles/dataset/REFIT_Smart_Home_dataset/2070091/1
```



--------------------------------------------------------------`nilmtk`环境配置完成。---------------------------------------------------------------------------------



## 2.nilmtk-contrib

### 深度学习环境配置

我个人的显卡为`RTX 4070`，经过查阅相关资料后得到：

- `cuda 11.2`是比较稳定的，能用则用

- `cudnn 8.1`和`cuda 11.2`适配性高

`cuda和cudnn`安装过程此处不过多介绍，需要根据自身显卡型号和驱动版本来进行安装



在配置环境之前，请先从`https://github.com/nilmtk/nilmtk-contrib/tree/master/sample_notebooks`复制一个demo，方便随时对环境进行debug：

![image-20240311212116811](.\pic\image-20240311212116811.png)



### 正式配置

从`conda`终端进入`nilmtk-contrib-master`目录，运行以下命令：

```
python setup.py install
```

终端输出如下：

![image-20240311193200752](.\pic\image-20240311193200752.png)

可以看到，python正在从其官网镜像寻找`nilmtk`包，但我们已经安装了该包，并且python官网不收录`nilmtk`，此时可以`Ctrl + C`终止命令行。

打开`nilmtk-contrib-master`目录下的`setup.py`文件，查看：

![image-20240311193721920](.\pic\image-20240311193721920.png)

可以看到，`setup`命令安装依赖中包含了`nilmtk`，我们将其注释掉，只保留`tensorflow`和`cvxpy`的安装，同时在`nilmtk-contrib-master`目录下创建`setup.cfg`文件，将以下语句添加并保存，从国内镜像安装`tensorflow`和`cvxpy`的速度会很快。

![image-20240311211646005](.\pic\image-20240311211646005.png)

![image-20240311194237786](.\pic\image-20240311194237786.png)

再次尝试运行：

```
python setup.py install
```

报错如下：

![image-20240311201410799](.\pic\image-20240311201410799.png)

虽然相关依赖安装失败，但是从终端可以看出，python已经将`nilmtk-contrib`复制到环境的`site-packages`目录下了，只不过缺少`tensorflow`和`cvxpy`依赖：

![image-20240311203531804](.\pic\image-20240311203531804.png)

既然环境目前还缺少`tensorflow`和`cvxpy`，并且`python setup.py install `报错，那不妨考虑直接用`conda`安装：

```conda install tensorflow
conda install tensorflow
```

报错如下：

![image-20240311203631132](.\pic\image-20240311203631132.png)

总而言之，上述`conda`提示为在当前python3.8环境下已安装的包和将要安装的包不兼容，简单来说，`conda`判定python3.8无法兼容所有的包。此时不着急从头构建新的python版本，毕竟`conda`只是寻求最兼容最稳定的版本，不代表其他版本安装不了，随着`tensorflow`和其他包的不断更新，以及显卡的不断升级，` nilmtk-contrib`越来越难兼容最新的包了，但不妨碍我们手动下载和构建满足当前环境的兼容版本。

继续运行命令：

```
conda search tensorflow-gpu
```

![image-20240311204226782](.\pic\image-20240311204226782.png)

可以看到，`tensorflow>=2.0`的最新架构为`2.6.0`，经过查阅相关博客([链接在此](https://blog.csdn.net/K1052176873/article/details/114526086))，发现适应`cuda=11.2,cudnn=8.1`和`python3.8`的`tensorflow-gpu`版本刚好包括`2.6.0`，所以我们可以直接通过`pip`安装：

```
pip install tensorflow-gpu==2.6.0 -i https://pypi.tuna.tsinghua.edu.cn/simple
```

安装完成后导入`tensorflow`进行调试，继续报错：

![image-20240311210034340](.\pic\image-20240311210034340.png)

继续使用`pip`安装`protobuf`：

```
pip install protobuf==3.20 -i https://pypi.tuna.tsinghua.edu.cn/simple 
```

报如下错误：查阅博客后发现是`tensorflow`和`keras`版本不对应

![image-20240311210216830](.\pic\image-20240311210216830.png)

![image-20240311151448966](.\pic\image-20240311151448966.png)

![image-20240311151531191](.\pic\image-20240311151531191.png)

*（Tips：`tensorflow`版本后缀和`keras`版本后缀一致，其兼容性会更好，例如`tensorflow=2.6.0,keras=2.6.0`）*

继续安装`keras`：

```
pip install keras==2.6.0 -i https://pypi.tuna.tsinghua.edu.cn/simple 
```

安装完成后继续测试，报错如下：

![image-20240311152509021](.\pic\image-20240311152509021.png)

报错显示`h5py`库没安装，这是因为之前我们使用`python setup.py install`安装了`nilmtk-contrib`时，`nilmtk-contrib`只对相关依赖包进行”挂钩“操作，并非真正安装。此时使用`pip`卸载后重新安装对应版本的`h5py`，经查阅后`h5py==2.10`版本比较稳定：

`pip install h5py==2.10 -i https://pypi.tuna.tsinghua.edu.cn/simple`

安装后继续测试，还是会报错，原因在于`nilmtk`依赖的`hdf5`与`h5py`版本冲突，并且`pytable`也没有安装。三者都是`nilmtk-contrib`依赖的包，他们之间的关系如下：

![image-20240311162521858](.\pic\image-20240311162521858.png)

经过多次测试之后，这三个包的兼容关系如下：最终使用如下命令安装对应版本

```
pip uninstall h5py

pip install h5py==2.10.0 -i https://pypi.tuna.tsinghua.edu.cn/simple

conda install hdf5=1.10.6(会自动安装兼容的pytable依赖)
```



再次运行`nilmtk-contrib`官网提供的demo，训练正常进行，至此环境配置工作结束。

![image-20240311162850983](.\pic\image-20240311162850983.png)



# 总结

`nilmtk`和`nilmtk-contrib`安装过程要注意以下几点：

- 确保科学上网的全局模式正常运行
- 在配置过程中，在每个`package`安装后使用`conda create -n (your env) --clone (new env)`来管理新环境，避免`conda`和`pip`命令安装和卸载包时导致整个环境作废。同时多尝试创建多个环境，根据实际情况尝试不同的配置步骤。本文配置过程仅为个例，不代表普遍情况。
- `cudnn 8.1`和`cuda 11.2`适配性高，并且`cuda 11.2`较为稳定，能用则用



# 其他说明

最终配置的环境文件导出在`requirement.txt`和`requirement.yaml`文件下，如果您的显卡和`cuda`版本与本次教程对应，可以尝试`conda env create -f requirement.yaml `来安装环境



这是本人的第一篇博客，如果对您有用请点个赞吧。





# 2024-03-13 补充

- 使用基于`setuptools`库的`setup.py`构建的`nilmtk-contrib`包，会在`site-packages`目录下生成`nilmtk_contrib-0.1.2.dev1+git.692320c-py3.8.egg`文件，该文件为压缩文件，文件内部有完整的`nilmtk-contrib`模块，能在代码中正常导入该包。但关键问题是无法对其进行编辑，这对于后续负荷分解实验的自定义对比、模型保存和绘图不友好。

解决方法：重写`nilmtk-contrib-master`目录下的`setup.py`文件，将`from setuptools import setup`换为`from distutils.core import setup`，基于`distutils`来构建`setup.py`并再次进行

````
python setup.py install
````

重新安装`nilmtk-contrib`。`distutils`安装方式为直接将`nilmtk-contrib`复制到`site-packages`目录下，并生成`nilmtk-contrib.egg-info`的元信息，后续可对代码进行编辑。

![image-20240313083002061](.\pic\image-20240313083002061.png)

![image-20240313083114011](.\pic\image-20240313083114011.png)

![image-20240313083208665](.\pic\image-20240313083208665.png)





# 2024-04-20 补充：

为了适配新一轮的Embedding 工作，需要配置tensorflow1.x环境，此次更新补充相关内容：

根据tensorflow官网测试后的环境配置，打算先从`tensorflow1.15.0`和`cuda10入手`：

![image-20240420143831929](./pic\image-20240420143831929.png)

先直接用以下命令安装，测试阶段报错。现有的`cuda=11.2`无法适配该版本的`tensorflow`

`pip install tensorflow-gpu==1.15 -i https://pypi.tuna.tsinghua.edu.cn/simple `

![image-20240420145835915](./pic\image-20240420145835915.png)

![image-20240420150025475](./pic\image-20240420150025475.png)

解决方案1：配置`cuda=10.x,cudnn=7.x`的系统环境

```
cuda:
https://developer.nvidia.com/cuda-10.0-download-archive?target_os=Windows&target_arch=x86_64&target_version=10&target_type=exelocal

cudnn:
https://developer.nvidia.com/rdp/cudnn-archive#a-collapse765-10
```



`tensorflow 1.x`环境配置完毕，接下来安装其他需要的库即可。

![image-20240420152100827](./pic\image-20240420152100827.png)









