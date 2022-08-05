---
title: Ubuntu18.04安装Jupyter Notebook
tags: [人工智能]
---

## 1. 基础环境准备：


```bash
# 更新软件包
sudo apt-get update
```
```bash
# 安装 Python 3
sudo apt-get install python3
```
```bash
# 安装 Python3 版本的标准库管理器
sudo apt-get install python3-pip
```
```bash
# 查看 Python 3 版本，确认安装成功
python3 -V
```
```bash
# 查看 python3-pip 版本，确认安装成功
pip3 -V
```


## 2. 安装运行 Jupyter Notebook


```bash
# 指定清华源，安装 Jupyter Notebook
pip3 install jupyter -i https://pypi.tuna.tsinghua.edu.cn/simple
```


安装完成后，在 ~/.local/bin/ 目录下应该能找到 jupyter-notebook 可执行文件。


为了能够让 Ubuntu 系统找到  jupyter-notebook ，接下来需要修改 PATH 变量。


使用下面的命令打开 ~/.bashrc 文件：


```bash
sudo vi ~/.bashrc

# 如果不习惯用 vi 编辑器，也可以用 sudo gedit ~/.bashrc 命令
```


在文件的最后，添加如下内容：


```bash
export PATH=~/.local/bin:${PATH}
```


保存文件修改并退出。


在当前命令行终端，执行如下命令使刚才的修改生效：


```bash
source ~/.bashrc
```
此时，执行如下命令，即可运行 Jupyter Notebook：


```bash
jupyter-notebook
```
