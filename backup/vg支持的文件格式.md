**主要是对官方文档的翻译, 简单看看得了**


# 1. 序列图文件 (Sequence Graph Files)

*   **`.vg`**: `vg` 的原生格式。基于 [Protocol Buffers](https://developers.google.com/protocol-buffers/) 的二进制格式。用于存储序列图及其比对（alignments）。可以使用 `vg view` 将其转换为其他格式。这是 `vg` 大多数命令的默认输入/输出格式。
*   **`.xg`**: 高度压缩的索引格式，针对图的快速遍历（traversal）和查询进行了优化。由 `vg index` 构建。是 `vg map`、`vg find`、`vg snarls` 等命令的主要索引。
*   **`.gcsa` / `.gcsa2.lcp`**: 图的 [GCSA2](https://github.com/jltsiren/gcsa2) 索引。由 `vg index` 构建。是 `vg map` 用于种子定位（seed finding）的索引。
*   **`.gbwt`**: 图的 [GBWT](https://github.com/jltsiren/gbwt) 索引（Haplotype 索引）。由 `vg index` 构建。是 `vg map` 和 `vg call` 使用的索引。
*   **`.snarls`**: 存储图的 [snarl](https://github.com/vgteam/vg/wiki/Snarls) 分解信息（由 `vg snarls` 生成）。
*   **`.dist`**: 存储节点之间的距离信息（由 `vg index` 构建）。
*   **`.vg.sql`**: 图的 SQL 数据库表示（beta）。
*   **`.og`**: 覆盖图（overlap graph）格式（由 `vg overlap` 生成）。
*   **`.pg`**: 路径图（path graph）格式（由 `vg mod -P` 生成）。

# 2. 比对文件 (Alignment Files)

*   **`.gam`**: `vg` 的原生比对格式（基于 Protobuf 的二进制格式）。存储相对于图的读取数据（reads）比对结果。是 `vg map` 的默认输出格式。可以使用 `vg view` 转换为其他格式。
*   **`.gamp`**: 与 `.gam` 相同，但包含路径信息（由 `vg annotate` 生成）。
*   **`.gaf`**: [GAF 格式](https://github.com/lh3/gfatools/blob/master/doc/rGFA.md#the-gaf-format)。一种基于文本的、描述相对于图的序列比对的格式。
*   **`.gam.index`**: `.gam` 文件的索引（由 `vg index` 构建）。用于快速按位置查询比对结果。
*   **`.bam` / `.cram` / `.sam`**: 标准序列比对格式（相对于线性参考基因组的比对）。`vg` 可以通过 `vg surject` 将 `.gam` 转换为这些格式，也可以通过 `vg mpmap` 直接输出 CRAM（beta）。
*   **`.hts`**: 一种描述单倍型定相路径（haplotype-phased paths）的格式（beta）。

# 3. 变异文件 (Variant Files)

*   **`.vcf`**: 标准变异调用格式（Variant Call Format）。`vg` 可以通过 `vg deconstruct` 从图中提取变异，也可以通过 `vg call` 从 `.gam` 比对结果中调用变异。
*   **`.tsv`**: 由 `vg find` 生成的变体位点（variant site）表格。

# 4. 其他文件 (Other Files)

*   **`.gfa`**: [图形片段组装格式](https://github.com/GFA-spec/GFA-spec)（Graphical Fragment Assembly format）。一种基于文本的序列图交换格式。`vg` 可以通过 `vg view` 在 `.vg` 和 `.gfa` 之间转换。也支持 [rGFA](https://github.com/lh3/gfatools/blob/master/doc/rGFA.md)（参考 GFA）。
*   **`.fa` / `.fasta`**: 标准 FASTA 格式（用于序列）。`vg` 可以通过 `vg view` 从图中提取路径或节点的序列。
*   **`.fq` / `.fastq`**: 标准 FASTQ 格式（用于带质量的测序读取数据）。`vg` 可以读取 FASTQ 作为 `vg map` 的输入。
*   **`.bed`**: BED 格式（用于定义基因组区间）。`vg` 可以通过 `vg find` 使用 BED 文件来查询图或提取子图。
*   **`.json`**: 由 `vg viz` 生成的图形可视化描述文件（供 [Bandage](https://rrwick.github.io/Bandage/) 使用）。
*   **`.odgi`**: [ODGI](https://github.com/vgteam/odgi) 格式。另一种基于 Protobuf 的序列图格式（`vg` 可以通过 `vg convert` 进行转换）。

# Reference 

*   [Command documentation](https://github.com/vgteam/vg/wiki/Command-Overview)
*   [Building an Index](https://github.com/vgteam/vg/wiki/Building-an-index)
*   [HTSLib](https://github.com/samtools/htslib) (用于处理 BAM/CRAM/SAM/VCF)
*   [GFA Spec](https://github.com/GFA-spec/GFA-spec)
*   [GCSA2](https://github.com/jltsiren/gcsa2)
*   [GBWT](https://github.com/jltsiren/gbwt)
*   [ODGI](https://github.com/vgteam/odgi)