# 写在前面
准备搞个泛基因组系列文章, 走一下图形泛基因组 (graph pan-genome) 的流程, 当前主流的几款软件, 以及分析流程都测一下, 做技术储备. 尽量复现一下结果, 跑一跑下游的分析流程. 本人能力有限, 希望大家多多讨论, 共同进步.

# Cactus 的安装

正常情况下软件安装不应该单独拎出来发一篇, 但是这个软件属实不太好安装, 踩了不少坑. 如果完全按照 GitHub 的步骤安装, 分析中大概率会报错. 我的建议是 **单独创建一个 conda 环境, 在这个环境里面执行后续安装.** 我的环境是 `CentOS 7`, `ldd (GNU libc) 2.17`. 另外请注意, **不要使用 conda 直接安装完整的 cactus**, 版本非常老旧, 很多新的 feature 都没有.

首先是 conda 部分, 创建一个 `python` 环境:

```bash
conda create -n python3.12 -y python=3.12.11 numpy dnachun::kent-tools virtualenv conda-forge::rsync bioconda::vcflib
```

需要说明的是: 
1. `python` 版本实际上 ` >3.9 ` 就可以了, 我这里直接搞了个 `3.12.11`;
2. `numpy`: 按照软件的安装方法, 这个包走 `pip` 安装对环境有要求, 我干脆扔给 `conda` 解决;
3. `kent-tools`: 这个是 `Cactus` 中部分功能会依赖, 直接下载是通过 `wget` 方法, 太慢了;
4. `virtualenv`: 必须安装, 是 `Cactus` 安装重要依赖;
5. `rsync`: 软件里会检测这个工具, 我是集群里没有, 有的话就不用装了;
6. `vcflib`: 处理 `vcf` 需要这个套件, 安装流程里面写的是选装, 但是对于图泛流程, 尤其是需要 `vcf` 输出的情况, 这个软件是必装;

基于这个环境, 就可以走下面的安装步骤了:

```bash 
# 启动刚才的 python 环境, 这里 conda 环境根据你的实际情况来
source /conda/bin/activate python3.12
# 下载, 自己下载后传到集群里也可以
wget https://github.com/ComparativeGenomicsToolkit/cactus/releases/download/v2.9.9/cactus-bin-v2.9.9.tar.gz
# 解压
tar -xzf cactus-bin-v2.9.9.tar.gz
cd cactus-bin-v2.9.9
# 安装
virtualenv -p python3 venv-cactus-v2.9.9
printf "export PATH=$(pwd)/bin:\$PATH\nexport PYTHONPATH=$(pwd)/lib:\$PYTHONPATH\nexport LD_LIBRARY_PATH=$(pwd)/lib:\$LD_LIBRARY_PATH\n" >> venv-cactus-v2.9.9/bin/activate
source venv-cactus-v2.9.9/bin/activate
python3 -m pip install -U setuptools pip wheel
python3 -m pip install -U .
python3 -m pip install -U -r ./toil-requirement.txt
```

看着多, 一步一步走就可以, 这里需要注意的是, **命令中的 `python3`, 一定要检查一下是不是刚刚安装环境里面的**, 或者都使用 `python`, 不带那个 `3`. 我自己由于 `alias` 了一个 `python3`, 导致这里安装有问题; 

如果 `pip` 安装过慢, 可以通过设置全局 `pip` 镜像解决; 如果哪个包安装错误, 直接使用 conda 安装就可以, 比较灵活.

全部安装完成后, 就可以 `cactus -h` , 可以正常显示帮助了. 后续使用的话, 记得我们是激活了两个环境的: conda 环境以及 cactus 的虚拟环境. 我把这两个环境存在了一个 `env.sh` 里, 后面直接使用比较方便, 仅供参考.

下一篇将介绍 Cactus 软件的基本使用.
