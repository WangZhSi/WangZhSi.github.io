# 软件介绍

`EviAnn` 是 *Steven L. Salzberg* 团队开发的一款编码基因注释软件。不查不知道，这也是个超级大牛团队了，像 `Bowtie`, `Bowtie2`, `Tophat`, `MUMmer`, `Liftoff`, `Kraken` 等等等等 , 都是这个团队的 [软件](https://salzberg-lab.org/software/), 敬佩。

`EviAnn` 是基于"证据"实现编码基因注释的，包括但不限于转录组/转录本数据，近缘物种蛋白序列等。软件的优势是运行稳定，速度快，我自己测试下来，15 G 小麦基因组不到 24 小时就能完成。当然了软件测试下来也有一些问题和 BUG , 这篇文章我们先介绍软件的安装使用，并分享一些我使用过程中的 tips, 欢迎大家沟通。

# 软件安装

软件居然是基于 `shell+CMake` 的，少见啊；基本安装非常简单，软件目前发布在 [GitHub](https://github.com/alekseyzimin/EviAnn_release) 上，从 `release` 页面下载 current 版本的压缩包，安装就可以了：

```bash
$ tar xvzf EviAnn-X.X.X.tar.gz
$ cd EviAnn-X.X.X
$ ./install.sh
```

同时需要你的环境变量中有两个软件：
- `minimap2`
- `HISAT2`

这里有几个注意事项：
1. 我测试的版本是 `v2.0.2` , 这个版本的 `TransDecoder` 似乎是有点问题，猜测是代码 bug, 解决这个问题可以手动 `export` 指定目录的软件，或者使用作者 pre-release 的 `v2.0.3` [版本]([https://github.com/alekseyzimin/EviAnn_release/raw/refs/heads/main/EviAnn-2.0.3.tar.gz](https://github.com/alekseyzimin/EviAnn_release/raw/refs/heads/main/EviAnn-2.0.3.tar.gz)), 估计后续正式 release 版本会修复；
2. 软件是需要 `Cmake` 编译的，这个大部分集群都有吧，主要是提醒一下，我曾经想把这个软件打到镜像里，居然因为没有 `Cmake` 报错了= =;

# 简单使用

接口不逐一解释了，介绍几个关键的：
- `-t`: 线程数，软件涉及到比对，内存控制还可以，越多越快；
- `-g`: 需要注释的基因组，GitHub 上写的是基因组不需要 mask, 这个问题后面再说；
- `-r`: 记录了转录组/转录本数据的 `paired.txt` 文件，下面详细说明；
- `-s`: 近缘物种蛋白序列；

关于 `paired.txt` 的说明 （文件不是非得叫这个名字）:
1. 如果是双端 RNA fastq 文件，使用 tab 分隔，一行一对 reads; 可以使用 `.gz` 压缩格式；
2. 如果是其他格式的输入，可以使用 **tag** 对数据做出声明，形如：

```
 /path/filename /path/filename /path/filename tag
 # or
 /path/filename /path/filename tag
 # or
 /path/filename tag
```

其中，**tag **支持以下几种格式，我直接复制不翻译了：

  1. `fastq`: indicates the data is Illumina RNA-seq in fastq format, expects one or a pair of /path/filename.fastq before the tag
  2. `fasta`: indicates the data is Illumina RNA-seq in fasta format, expects one or a pair of /path/filename.fasta before the tag
  3. `bam`: indicates the data is aligned Illumina RNA-seq reads, expects one /path/filename.bam before the tag
  4. `bam_isoseq`: indicates the data is aligned PacBio Iso-seq reads, expects one /path/filename.bam before the tag
  5. `isoseq`: indicates the data is PacBio Iso-seq reads in fasta or fastq format, expects one /path/filename.(fasta or fastq) before the tag
  6. `mix`: indicates the data is from the sample sequenced with both Illumina RNA-seq provided in fastq format and long reads (Iso-seq or Oxford Nanopore) in fasta/fastq format, expects three /path/filename before the tag
  7. `bam_mix`: indicates the data is from the same sample sequenced with both Illumina RNA-seq provided in bam format and long reads (Iso-seq or Oxford Nanopore) in bam format, expects two /path/filename.bam before the tag

这里可以直接读 isoseq 还是挺方便的，好评。

使用的时候需要注意，软件默认输出是 `./`, 所以简单使用的话：

```bash
cd ${outdir} && eviann.sh -t 60 -g genome.fa -r pairs.txt -s homo.pep.fa
```

# 个人经验

用了几天，总结一些实用经验和大家分享：
1. 软件本身是基于转录证据和近缘物种证据，没有从头预测证据，最终输出的基因数会少一点，这里可以参考国内其他团队对软件的修改，支持并行，支持从头预测合并，修改后的版本发布在 [GitHub](https://github.com/linyuiz/EviAnn_update) 上，**不过我还没有测过**;
2. 软件写的是不需要对基因组做 mask, 但我测试下来还是 mask 一下比较好，可以显著减少运行时间，也不会带来一些误判；
3. 近缘物种序列可以直接把 busco 的序列合并进去，以防 busco 评估异常；
4. 软件输出是带可变剪切的，但是 gff 中三级标签如 CDS, exon 行只有 parent, 没有 ID, 也没有 URT 标签，可以使用 [`AGAT`](https://github.com/NBISweden/AGAT) 补一下；
5. 如果输入 mRNA 的 reads 的话，软件中的比对是**串行**的，如果比较介意周期可以自己传入比对后的 bam;

# 总结

目前论文处于 [预印阶段](https://www.biorxiv.org/content/10.1101/2025.05.07.652745v1), 软件也存在一些可优化的空间和 bug, 但是整体是个比较好用的工具，推荐大家使用；目前我还没来得及做常见的评估如外显子比对率，busco 等，但着重考虑转录的话，想来也不会很差吧。证据等到软件完成后续更新，可能会再做一次测试。