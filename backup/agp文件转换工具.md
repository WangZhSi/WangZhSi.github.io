# 工具说明

依据不同需求转换 agp 文件, 支持输出/输出 4 列或 9 列 agp 格式, 其中 9 列 agp 格式为标准 agp 格式, 4 列 agp 格式如下:

```bash
# chrom	contig	strand	order
chr1	ctg001	0	1
chr1	ctg002	1	2
chr1	ctg003	0	1
chr2	ctg004	1	1
```

其中第三列: `0` 为正向, `1` 为负向;

# 接口说明

工具接口较多, 具体说明如下:
## 必需参数
- `-i`: 输入 agp 文件, 读取时自动忽略注释行;
- `-o`: 输出 agp 文件, 如存在会覆盖, 不检查路径, 如果路径不存在会报错;
- `-F`: 输出 agp 格式, 4 或 9;

## 非必需参数
- `-s`: tab 分隔的 contig 大小文件, 可以直接使用 `.fai`;
- `-g`: 指定 gap 大小, 默认 100 bp, 只有生成 9 列 agp 会用到, 不要 gap 就设置为 0;
- `--select`: 筛选包含指定字符串的染色体, 像 ragtag 这种软件会把比对上的 chr 和 ctg 放在一起, 可以使用这个接口筛选出包含 chr 或_RagTag 的内容做分析; 支持逗号分隔的多个字段;
- `--filter`: 过滤指定字符串 (replace 方法), 比如去掉_RagTag 后缀; 支持逗号分隔的多个字段;
- `--id2id`: tab 分隔的两列表, 用于替换输出的染色体 id, 如 `group1 -> chr1`;
- `--reverse`: 整条反转某染色体, 支持逗号分隔的多个字段;
- `--nature`: 自然顺序排列染色体序号, 修正 `1,10,2,3....` 排序问题;
- `--sizeOrder`: 基于染色体大小重命名染色体, 最大的染色体为 `chr1`;
- `--prefix`: 重命名染色体时的前缀, 默认为 `chr`;

# 使用示例

例: 针对 ragtag 软件输出的 agp 文件, 仅选择包含字段 chr 的内容, 过滤掉_RagTag 后缀, 输出的 agp 格式为 9 列, gap 大小为 200 bp, 染色体号依据大小重新排序, 新的染色体 ID 前缀为 `NC000`, 则:
```bash
python3 convert_agp.py -i input.agp -o output.agp -F 9 --select chr --filter _Ragtag -g 200 --sizeOrder --prefix NC000
```

# 工具下载

软件部署在 [GitHub](https://github.com/WangZhSi/Bioinformatics_tool/tree/main/convert_agp) 上, 欢迎下载使用.