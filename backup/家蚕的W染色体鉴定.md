# 文献信息

Title: Multiple independent origins of the female W chromosome in moths and butterflies
Journal: Science Advances
Year: 2024
Volume: 10 

家蚕是 ZW 型性别决定，其中雄性是 ZZ 型，雌性是 ZW 型。目前鳞翅目昆虫没有较好的 W 染色体组装注释结果。文章组装了家蚕的基因组，鉴定出 W 染色体，并做了 W 染色体的起源进化。文章使用了一种 chromosome quotient (CQ) 算法用于鉴定 W 染色体，这里主要看一下这个 CQ 算法是怎么实现的。

# W 染色体鉴定

## 原理

完成雌性家蚕基因组组装，在未分型全部组装结果中 (scaffold 和 contig), 应该包含了 Z 染色体和 W 染色体，需要做的就是将 W 染色体识别出来；如果使用雄性家蚕 (ZZ) 的测序数据，以及雌性家蚕 (ZW) 的测序数据，分别比对回组装基因组上，那么对于 Z 染色体，雄性家蚕 reads 覆盖数与雌性家蚕 reads 覆盖数的比值应趋近于 2; 对于 W 染色体，雄性家蚕 reads 覆盖数与雌性家蚕覆盖数的的比值应趋近于 0; 对于长染色体，二者比值应趋近于 1.

原文如下：

> - The CQ value represents the ratio of male (ZZ) to female (ZW) read coverage in a genome region.
> - Theoretically, the CQ values obtained for the Z and W chromosomes should be close to two and zero, respectively.

## 代码实现

整体流程是使用 perl 串的，大概可以分为比对，计算 CQ 值，筛选 W 染色体三个部分；
### 比对

```perl
system"bwa index -a bwtsw $genome";
system"bwa mem -t 50 $genome $malereads1 $malereads2 >$genome\_Male.sam";
system"bwa mem -t 50 $genome $femalereads1 $femalereads2 >$genome\_Female.sam";
system"samtools view -b -o $genome\_Male.bam $genome\_Male.sam";
system"samtools view -b -o $genome\_Female.bam $genome\_Female.sam";
system"samtools view -@ 50 -bq 1 $genome\_Male.sam >$genome\_Male_uniq.bam";
system"samtools view -@ 50 -bq 1 $genome\_Female.sam >$genome\_Female_uniq.bam";
system"bamToBed -i $genome\_Male_uniq.bam >$genome\_Male_uniq.bed";
system"bamToBed -i $genome\_Female_uniq.bam >$genome\_Female_uniq.bed";
```

这里有几个注意点：
1. sam 转 bam 时使用了 `-bq 1` 的参数，只保留唯一比对
2. `bamToBed` 方法提取了每个染色体区段 reads 的覆盖情况

### 计算 CQ 值

核心计算代码大概是下面几行：

```perl
while(<BED>){
  chomp;
  my $line=$_;
  $line=~s/ +/\t/g;
  my @bed_line=split("\t",$line);
  push @ID,$bed_line[0]; 
}

$hash{$_}++ for @ID; print OUT "$_\t$hash{$_}\n" for (keys %hash);
```

实际上就是计算每条染色体比对上的 reads 有多少

### 筛选 W 染色体

首先读取计算了雌性 (F) 和雄性 (M) 的 total reads, 累加；这里作者计算了一的总 reads 数的比值，应该是考虑到测序深度不等的情况，做个简单的校准系数：

```perl
$CorrextFactore=$totalMUniqReads/$totalFUniqReads;
```

随后计算每条染色体的 CQ 值：

```perl
$CQvalue=$SpeContigMreads/($SpeContigFreads*$CorrextFactore);
if($CQvalue>1.7){push @AllZcontigs,$SpeContig;}
if($CQvalue<0.3){push @AllWcontigs,$SpeContig;}
```

通过变量名称就可以看出来，分子是每条染色体上雄性 reads 数，分母是雌性 reads 数乘校准系数；

可以看到老师实际分析中是以 1.7 和 0.3 做阈值筛选。
## 实验验证

实验验证，湿实验部分我解释不了- - 大概是这么几块实验：
- PCR 验证已知的 15 个 W 染色体片段；
- 已有报道的 Fem 前体 blast 验证，能 blast 到已知前体；
- CQ 值验证其他 ZW 型物种，证明方法可靠；

# 总结

私以为对于动物来说这种方法还是比较方便的，或者说对于有完整性染色体结构的物种是一种可以泛用的方法。但是这一方法实际上要求组装结果中，至少在组装草图中已经将性染色体组装出来，否则后续筛选可能会有问题。

对于高等植物，23 年白麦瓶草 (_Silene latifolia_) 的文章鉴定出了 X 染色体，这是第一个鉴定出性染色体的维管植物。其他高等植物的性别决定十分复杂，比如柳树就是一段在基因组上反复横跳的片段，不知道会不会有一些泛用的方法解决这种性别决定鉴定的问题。

# Reference

1. Han, M., Luo, C., Hu, H., Lin, M., Lu, K., Shen, J., Ren, J., Ye, Y., Westhof, E., Tong, X., & Dai, F. (2024). Multiple independent origins of the female W chromosome in moths and butterflies. _Science Advances, 10_.
2. Yue, J., Krasovec, M., Kazama, Y., Zhang, X., Xie, W., Zhang, S., Xu, X., Kan, B., Ming, R., & Filatov, D.A. (2023). The origin and evolution of sex chromosomes, revealed by sequencing of the Silene latifolia female genome. _Current Biology, 33_, 2504-2514.e3.
3. Wang, D., Li, Y., Li, M., Yang, W., Ma, X., Zhang, L., Wang, Y., Feng, Y., Zhang, Y., Zhou, R., Sanderson, B.J., Keefover‐Ring, K., Yin, T., Smart, L.B., DiFazio, S.P., Liu, J., Olson, M.S., & Ma, T. (2022). Repeated turnovers keep sex chromosomes young in willows. _Genome Biology, 23_.
4.  https://github.com/ruio-7/CQ-package/tree/master