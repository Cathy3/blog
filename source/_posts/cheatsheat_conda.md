title: Conda常用命令
date: 2018-02-14
categories: 
		- cheatsheet
tags:  
		- 笔记
		- basic
---

# Conda 环境

```
# 创建一个名为python34的环境，指定Python版本是3.4
 conda create --name python34 python=3.4

# 激活某个环境
activate python34 # for Windows
source activate python34 # for Linux & Mac

deactivate python34 # for Windows
source deactivate python34 # for Linux & Mac

# 删除一个已有的环境
conda remove --name python34 --all

# 创建制定python版本的环境
conda create --name your_env_name python=2.7
conda create --name your_env_name python=3
conda create --name your_env_name python=3.5

# 创建包含某些包的环境
conda create --name your_env_name numpy scipy

# 创建指定python版本下包含某些包的环境
conda create --name your_env_name python=3.5 numpy scipy

# 列举当前所有环境
conda info --envs
conda env list

# 进入某个环境
activate your_env_name

# 退出当前环境
deactivate 

# 复制某个环境
conda create --name new_env_name --clone old_env_name 
```
<!-- more -->
# Conda包管理
```
# 安装xxxx
conda install xxxx

# 查看当前环境下已安装的包
conda list

# 查看某个指定环境的已安装包
conda list -n python34

# 查找package信息
conda search numpy

# 安装package
# 如果不用-n指定环境名称，则被安装在当前活跃环境 也可以通过-c指定通过某个channel安装
conda install -n python34 numpy 

# 加一个-c表示从http://anaconda.org下载资源包
conda install -c spyder-ide spyder=3.0.0

# 更新所有库
conda update --all
```
[cheat-sheet下载](https://conda.io/docs/_downloads/conda-cheatsheet.pdf)

## 安装指定包

安装Flask
```python
conda create --name flask_app # 创建新的虚拟环境，名为flask_app
source activate flask_app # 进入这个虚拟环境（linux环境下）
conda install -c anaconda flask # 安装 flask 包
deactivate #退出环境
```
-	安装好之后，可新建 Flask 项目。
-	[Flask 快速入门](http://docs.jinkan.org/docs/flask/quickstart.html#quickstart)
