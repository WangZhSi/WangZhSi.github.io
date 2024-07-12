
之前我写过 HapHiC 的安装与使用，有幸被作者摸了过来，指正了我教程中的部分问题，也对不少我含糊的地方作出详细说明和解释。老师非常有经验，我收获很大。沟通内容我整理到此篇文章中，希望对你也有一些帮助。

# 一、数据准备方向
Zeng 老师提出一个观点：

> 在处理了大量真实项目后，我发现出现问题很多是因为错误的实验设计、低质量的组装、错误选择的组装版本、低质量的 Hi-C 文库、不合适的比对和过滤方法、错误的理念等原因造成的。只是 scaffolding 作为组装的下游，和可视化的第一步才把这些问题显现出来。

这一点我深表认同，有时候非得对着 CLR 数据组装真的很困难。对于数据比对，上一篇我提到了 HapHiC 建议 `bwa mem`, 这里 Zeng 老师详细解释了原因：

> bwa mem 使用-5SP 这个参数，是因为 Hi-C reads 属于 long range data，比对的时候其假设和短片段的 PE reads 有所不同，可以参考一下 Phase Genomics 对这个参数的推荐和描述
> 
> “We use bwa mem to align Hi-C data, with the -5, -S, and -P options. These options aren’t documented that well in the online bwa documentation, but they are documented in the usage of bwa mem. -5 is sometimes called “the Hi-C option” as it was designed to help the aligner handle the statistical properties of Hi-C libraries better, mainly by reducing the amount of secondary and alternate mappings the aligner makes as those cause Hi-C data to become ambiguous. The -S and -P options cause the aligner not to try to use assumptions about the reads that might be true for shotgun or mate pair libraries in an effort to rescue more reads. In fact, using these options to avoid those rescue efforts usually results in more of the Hi-C data having a useful alignment!”

对于 chromap 比对，老师指出了关于比对错误率的问题，这块确实是个盲点，chromap 超高的时间成本优势让我忽略了这个问题；对于简单基因组可能问题不大，对于复杂基因组，或者分型 scaffolding, 这些比对问题可能给挂载带来更多问题。

> 这里需要说明的是，虽然 chromap 比较快，但是同样按照 MAPQ>=1 进行过滤的错误率明显高于 bwa mem，且有效 read pair 更少（如果需要数据我可以提供）。这也是我为什么推荐 bwa mem 的原因。如果是做分型的 scaffolding，首选 bwa mem。如果非要用 chromap，建议始终加上--remove_allelic_links 参数防止不同 haplotype 聚类到一起。Juicer pipeline 比对错误率比 chromap 还要高很多，且速度慢，所以任何时候都不推荐使用。

同时也展示了 `bwa mem` 与 chromap 比对结果组装效果的对比：

![](https://raw.githubusercontent.com/WangZhSi/WangZhSi.github.io/main/_images/haphic.jpg)

> 上图是 chromap 和 bwa mem 的直观对比，一个较高杂合的同源四倍体的案例，juicer pipeline 的情况比 chromap 还要严重很多。
>
> 同源染色体之间的互作其实本来和普通的染色体之间的信号没有什么区别，我们从热图上能观察到这种沿对角线分布的信号主要有两个来源，比对和组装错误。当使用 bwa mem 比对配合 MAPQ>=1 的过滤标准能很好地保证比对地准确性。组装错误可能来源于纠错过程或者 read 的分配过程，最终这些来源于不同 haplotype 的 reads 被 collapse 生成了 consensus 序列，造成了碱基水平的 switch error。HapHiC `--remove_allelic_links <ploidy>` 这个参数就是根据这个对角线信号识别等位 contig，并消除这些信号带来的不好影响。所以如果使用 chromap 做单倍体分型，建议加上这个参数。

# 二、挂载方向
老师回复后我也请教了一些问题，部分地方我可能理解也有一定困难，为了避免误导就不解说了；

首先对于复杂基因组或同源多倍体基因组挂载质量的问题：

> 多倍体基因组的主要难点是组装质量差（chimeric、collapsed contigs、switch errors）。HapHiC 引入了一些创新的算法，比如 rank-sum 同时识别 chimeric 和 collapsed contigs，以及利用 Hi-C 信号的特征识别等位 contig。这些方法在一定程度上能解决多倍体基因组中遇到的问题。但是如果错误太多，可能需要调参（比如：chimeric contigs 很多的同源四倍体甘蔗 Np-X，需要用--correct_nrounds 纠错；collapsed contigs 很多的 AP85-441 调整了--rank_sum_upper 和--density_upper；存在大范围 collapse 的马铃薯 C88 基因组使用了--normalize_by_nlinks 和--remove_concentrated_links）。不同的基因组特征、不同的数据类型、不同的组装方法都会导致基因组有不一样的错误类型和倾向。尤其是 HiFi 数据组装的基因组相比非 HiFi 数据组装，以及野生种相比栽培种，这种倾向的差异巨大。我们已经在这方面积累了大量经验，如果你使用中遇到了具体的问题，欢迎给我提 issue。

对于这里提到的几种组装错误问题，我进一步请教 **“针对您提出识别组装错误的问题，我们是否有办法在获得组装结果时就做出识别，或调整组装结果？”** 回复如下：

> 目前来说没有很好的办法，有些组装问题可能需要更好的数据才能解决，比如使用 ONT ultra-long reads。用 gfa 文件绘制 assembly graph 可以一定程度上识别 collapse，但比较难以统计，另外 gfa 文件中的 rd tag 可以看到 contig 的深度，高深度通常也是 collapse 导致的。

同时老师也提供了关于数据选择的建议：

> 另外，在有限的数据的情况下，选对组装版本是很重要的。比如二倍体我们倾向于使用 hifiasm 的 Hi-C 模式组装，当杂合率足够的时候选择将 hap1.p_ctg 和 hap2.p_ctg 合并，将 Hi-C reads 同时比对到合并的 contigs 上进行 scaffolding。当杂合率很低时，分开对 hap1 和 hap2 scaffolding，最后再合并比对进行验证。如果时多倍体，我们会选择 p_utg 进行 scaffolding，因为 unitigs 是准确分型的。

那么对于组装错误，在使用 HapHiC 时，scaffolding 过程中是否有好的解决办法呢 **“scaffolding 过程中，是否有些中间文件或提示，让我知道挂载问题是由于 chimeric contigs 或 collapsed contigs, 以便针对性调整？”** 

> 在 `01.cluster/HapHiC_cluster.log` 文件中，我们会输出一些可能有助于发现这些问题的信息。里面会提示利用 link density 和 rank-sum 值分别过滤了多少 fragments，如果这个比例比较高，比如超过 10%，说明基因组可能存在较多的组装错误。当然这也不是绝对的，但是算作一种 hint。因为我在自动过滤这些 fragments 时使用的策略是最常见的离群值检测方法，即 $Q3+1.5*IQR$，如果错误占比太大，这个方法会失效，所以提供了手动设置比例的选项。这些错误通常可以安全地进行过滤，因为这些过滤只是暂时的，后面 contigs 还是会补回到合适的 group。另外如果想知道具体是哪些 contig 被过滤了，可以在运行 `haphic pipeline` 或 `haphic cluster` 时加上 `--verbose` 参数查看完整的日志。

对于实际项目经验和使用参数，我也提出了问题 **“对于可公开数据，如您提到的马铃薯，甘蔗，是否方便 share 一些使用的参数？”** 答复如下：

> 我在预印文章的附件中详细提供了所有用到的命令行，可以参考。另外最近文章也快正式发表了，到时可以再看新版的附件，做了一些补充和改动。比如针对发表的 C88.v1 版本，也做了测试。我在预印文章的附件中详细提供了所有用到的命令行，可以参考。另外最近文章也快正式发表了，到时可以再看新版的附件，做了一些补充和改动。比如针对发表的 C88.v1 版本，也做了测试。

# 三、其他方向
这里就是一些经验，以及对 HapHiC 本身的问题沟通了。首先关于集群环境的问题：

> 我主要使用的服务器是 2014 年发布的 CentOS7，搭配 2015 年发布的 GCC 4.8.5，2012 年发布的 glibc 2.17，没有遇到过问题。相信绝大部分集群应该满足这些要求，只需要使用 conda 安装环境即可使用 Python 3.10。此外，虽然没有提供相应的 yml 文件，但 HapHiC 最开始是在 Python 3.8 下开发的，所以可以兼容低版本的 Python。对于更高版本的 Python 3.11 和 3.12，HapHiC 也做了支持。

我在第一篇教程中曾表示 **对于简单二倍体植物基因组，HapHiC 相比我使用的 yahs 没有明显优势**, 老师也对这一观点做了答复：

> HapHiC 和 YaHS 相比，前者更容易获得染色体水平的 scaffolds，挂载率通常较高，并且可以控制。后者的优势是无需知道染色体数，以及在染色体长度差异较大时更加稳健。HapHiC 近期在 contig 排序上也做了一些改进，如果之前遇到过类似问题可以更新。

关于 HapHiC 和 ALLHiC 之间的关系问题：

> 这里我也想讲一讲 HapHiC 和 ALLHiC 的关系。其实我本来只是 ALLHiC 的用户之一，但是在做自己的项目时遇到了一些问题，做过一些简单的外围开发（ [https://github.com/zengxiaofei/small-contig-reassignment](https://github.com/zengxiaofei/small-contig-reassignment) ）。在发现效果不错后，我决定自己从头设计一款工具，尝试解决 ALLHiC 依赖参考基因组的问题。因为总体上和 ALLHiC 的策略是一致的，都是先聚类后排序（相似的还有 LACHESIS，有别于 3D-DNA、SALSA2 和 YaHS），所以我对 ALLHiC 的文件格式做了兼容。实际上如果用得熟练的话，这两款工具可以混合使用。另外在排序的最后一步，我调用了一个自己修改过的 allhic 程序（ [https://github.com/zengxiaofei/allhic](https://github.com/zengxiaofei/allhic) ），从 fast sorting 的初步排序结果进行优化。当然，用`--skip_allhic`参数跳过 allhic 也是可以的。事实上 fast sorting 的结果其实非常稳健。最近的更新也是基于 fast sorting 和 allhic 的结果进行比较，从中择优。总的来说，这款工具和 ALLHiC 相比，区别还是很大的，无论是 contig 的纠错、过滤、等位 contig 的识别、聚类方法、reassignment 的思想，排序都做了比较多的创新。

# 总结
可以看出老师对复杂基因组组装挂载非常有经验，也提供了很多帮助和建议；其中很多问题，如 chromap 比对错误率，组装错误类型等，都是我之前没有注意过的。完整沟通记录请移步我的 [issue](https://github.com/WangZhSi/WangZhSi.github.io/issues/3) 中查看。

再次感谢 Zeng 老师的帮助。