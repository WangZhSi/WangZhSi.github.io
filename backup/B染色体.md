
B 染色体（B chromosomes，Bs）是一些真核生物体内存在的，区别于常规染色体 (A 染色体，A chromosomes, As) 的额外染色体，一般被认为是可有可无的核型成分 (dispensable karyotypic components) $^1$. B 染色体于 1928 年首次定义，与 A 染色体不同，B 染色体不参与常规的减数分裂配对或遗传重组，表现出非孟德尔遗传特征。目前，已知约有 15%的真核生物携带 B 染色体。

![](https://raw.githubusercontent.com/WangZhSi/WangZhSi.github.io/main/_images/B%E6%9F%93%E8%89%B2%E4%BD%93_1.png)

# B 染色体的起源，构成与进化

## B 染色体的起源

B 染色体的起源及其在不同物种间的演化机制一直是遗传学领域的核心问题之一。研究表明，B 染色体的许多序列与 A 染色体具有相似性，支持了 B 染色体源自 A 染色体的假设 $^2$. 利用基因组学研究手段，人们发现 B 染色体并非由单一的 A 染色体片段形成，而是通过多条 A 染色体的片段积累而成。通过基因重排 (transposition), 重复 (replications) 和转座 (rearrangement events) 等机制，这些 A 染色体片段逐渐在 B 染色体中整合。在玉米 (maize) $^3$ 和慈鲷 (cichlid fishes) $^4$ 的研究中发现，B 染色体包含多个来源于不同 A 染色体的基因片段。黄喉姬鼠的 FISH 和解剖实验结果表明，B 染色与性染色体存在一定同源性 $^5$. 这些结果表明 B 染色体具有复杂的起源过程。

## B 染色体的构成

B 染色体的组成复杂，结构多样，目前的研究成果主要包括以下内容：

1. 重复 DNA 元素 (repetitive DNA): 包括 DNA 类重复序列如 SINEs, LINEs 等；
2. 转座子特别是逆转座子：有研究认为转座子是 B 颜色题进化的关键驱动力 $^6$;
3. 功能基因：早期研究认为 B 染色体缺乏功能基因，但是近代研究发现 B 染色体上包含与细胞周期，染色体分裂和结构维持相关的基因。
4. 假基因：假基因是不能直接编码蛋白质，已经失去功能的基因。B 染色体上的假基因可能是 A 染色体基因的衍生物，通过基因重复，重排或突变等机制，部分演化为假基因。尽管它们在功能上不再活跃，但是 B 染色体上的假基因表明 B 染色体在基因组进化过程中经历了复杂的结构变化。
5. ncRNA 和 rRNA
6. B 必需基因：B essential genes, 定位在 B 染色体上的，对遗传和生存有至关重要的作用。和上面“功能基因”部分有重叠

B 染色体的组成远比最初想象的要复杂很多，不仅包含大量重复序列和转座子，还包含功能基因，假基因，ncRNA, rRNA 等多种分子元素。这些祖坟不仅对 B 染色体的维持和进化至关重要，还可能通过影响 A 染色体的功能促进生物体的适应性进化。

## B 染色体的进化

文章 $^7$ 中提出了一种新的 B 染色体进化模型：
最初，多染色体基因组 DNA 中的进化热点经历了转座、重复和/或基因组重排等不同事件，这些事件可以被视为主要的进化动力。这些事件导致了“B 前体 DNA”的产生，该 DNA 通过任何基因组重排与 A 染色体分离。B 前体 DNA 可能包含 B 染色体必需基因（例如组蛋白、DNA 结合和包装蛋白），这些基因的表达形成 B 染色质，并促使其重组，随后积累了包括重复序列和其他基因在内的额外 DNA。经过一系列进化事件，最终形成了初始的原始 B 染色体。
![](https://raw.githubusercontent.com/WangZhSi/WangZhSi.github.io/main/_images/B%E6%9F%93%E8%89%B2%E4%BD%93_2.png)

# B 染色体的生信分析

以盐草为例，看一下近期文章对 B 染色体的生信分析主要做些什么。

## 盐草中的 B 染色体

在盐草 $^8$ 的文章中，作者除了解析盐草的 A 染色体外，也对 B 染色体做了以下研究：

1. B 染色体的组成：盐草中的 B 染色体富含重复元素，特别是 Copia 和 Gypsy 类 LTR 逆转录转座子。同时文章在研究 A 染色体时鉴定到了 CEN162 着丝粒重复单元，通过 FISH 实验，在 B 染色体上同样发现了 CEN162 单元
![](https://raw.githubusercontent.com/WangZhSi/WangZhSi.github.io/main/_images/B%E6%9F%93%E8%89%B2%E4%BD%93_4.png)

2. k-mer 分析：分析发现 B 染色体与 A 染色体共享大量相似的序列片段，表明 B 染色体可能起源于 A 染色体的片段重组、转座子插入以及基因组重排等事件。
![](https://raw.githubusercontent.com/WangZhSi/WangZhSi.github.io/main/_images/B%E6%9F%93%E8%89%B2%E4%BD%93_3.png)

3. 基因注释：这部分基本和常规 *de novo* 一样了，注释出基因，然后功能注释。文章的结论是 B 染色体可能在有丝分裂染色体凝缩中发挥一定作用，从而帮助 B 染色体在细胞分裂过程中自我复制和传递。

# 讨论
> 以下内容为个人思考，不保证准确。

除了综述文章外，我也阅读了一些针对单一物种 B 染色体的研究性文章，但仍未能明确如何在组装的草图和大量的 contig 碎片中识别并鉴定出 B 染色体。盐草的研究给了我一些启发。该文中，作者首先组装并鉴定了 B 染色体，随后通过着丝粒重复单元、FISH 实验、免疫荧光分析和 oligo-FISH 实验等多种方法验证了其鉴定结果的可靠性。

尽管实验部分对我来说超出了掌握范围，但在生信分析上，如果我能先确认 A 染色体的着丝粒重复单元，然后利用该重复单元对所有 contig 进行检验，找到除 A 染色体外仍包含重复单元的 contig 片段，是否可以认为这些 contig 是“值得关注的”？若某条 contig 中同时鉴定出着丝粒和端粒重复单元，在实验表明该物种基因组包含 B 染色体的前提下，是否可以据此推测该 contig 序列即为 B 染色体序列？

# 参考文献

1. Longley, A.E. Supernumerary chromosomes in Zea mays. J. Agric. Res. 1927, 35, 769–784.[↩︎](app://obsidian.md/index.html#fnref-1-f9348cb901cb7b84)
2. Jones, R. (2018). Transmission and Drive Involving Parasitic B Chromosomes. _Genes, 9_.[↩︎](app://obsidian.md/index.html#fnref-2-f9348cb901cb7b84)
3. Jin, W., Lamb, J.C., Vega, J., Dawe, R.K., Birchler, J.A., & Jiang, J. (2005). Molecular and Functional Dissection of the Maize B Chromosome Centromere. _The Plant Cell Online, 17_, 1412 - 1423.[↩︎](app://obsidian.md/index.html#fnref-3-f9348cb901cb7b84)
4. Valente, G.T., Conte, M.A., Fantinatti, B.E., Cabral-de-Mello, D.C., Carvalho, R.F., Vicari, M.R., Kocher, T.D., & Martins, C. (2014). Origin and evolution of B chromosomes in the cichlid fish Astatotilapia latifasciata based on integrated genomic analyses. _Molecular biology and evolution, 31 8_, 2061-72 .[↩︎](app://obsidian.md/index.html#fnref-4-f9348cb901cb7b84)
5. Rajičić, M., Romanenko, S.A., Karamysheva, T., Blagojević, J., Adnađević, T., Budinski, I., Bogdanov, A., Trifonov, V.A., Rubtsov, N.B., & Vujošević, M. (2017). The origin of B chromosomes in yellow-necked mice (Apodemus flavicollis)—Break rules but keep playing the game. _PLoS ONE, 12_.[↩︎](app://obsidian.md/index.html#fnref-5-f9348cb901cb7b84)
6. Coan, R.L., & Martins, C. (2018). Landscape of Transposable Elements Focusing on the B Chromosome of the Cichlid Fish Astatotilapia latifasciata. _Genes, 9_.[↩︎](app://obsidian.md/index.html#fnref-6-f9348cb901cb7b84)
7. Ahmad, S.F., & Martins, C. (2019). The Modern View of B Chromosomes Under the Impact of High Scale Omics Analyses. _Cells, 8_.[↩︎](app://obsidian.md/index.html#fnref-7-f9348cb901cb7b84)
8. Jesse Poland, Kashif Nawaz, Izamar Olivas Orduna et al. Saltgrass genomes reveal unique features of a dioecious, perennial, halophytic grass, 22 August 2024, PREPRINT (Version 1) available at Research Square [https://doi.org/10.21203/rs.3.rs-4844604/v1](https://doi.org/10.21203/rs.3.rs-4844604/v1) [↩︎](app://obsidian.md/index.html#fnref-8-f9348cb901cb7b84)