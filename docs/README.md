# `App::Fasops` - operating blocked fasta files
利用`App::Fasops`操作blocked fasta文件



## Compiling  
编译

```bash
curl -L https://cpanmin.us | perl - --sudo App::cpanminus
安装 CPANMinus

cpanm App::Fasops
安装 App::Fasops
```



## Test 
测试

### 1.  Help
查看Faops信息

```bash
输入：
fasops help


Usage: fasops <command> [-?h] [long options...]
	-? -h --help  show help
用法：fasops <命令> [-?h] [选项...]
       -? -h --help  显示帮助


Available commands:

commands  list the application's commands
命令  列出所有的命令

help  display a command's help screen
帮助  展示一个命令的帮助页面

axt2fas  convert axt to blocked fasta
axt2fas文件转换  将UCSC axt成对文件转换为blocked fasta文件

check  check genome locations in (blocked) fasta headers
检查  在（blocked）fasta的标题检索基因组位置

concat  concatenate sequence pieces in blocked fasta files
连接  将序列片段链接成blocked fasta文件

covers  scan blocked fasta files and output covers on chromosomes
覆盖  扫描 blocked fasta文件，输出可以覆盖染色体的部分

join  join multiple blocked fasta files by common target
添加  通过共有标签添加多个blocked fasta文件

links  scan blocked fasta files and output bi/multi-lateral range links
链接  扫描blocked fasta文件并且输出两边或是多边范围链接

maf2fas  convert maf to blocked fasta
maf2fas文件转换  将maf文件转变为成块的fasta文件

names  scan blocked fasta files and output all species names
名字  扫描blocked fasta文件，输出所有物种的名字

refine  realign blocked fasta file with external programs
优化  根据外置程序重排blocked fasta文件

replace  replace headers from a blocked fasta
替换  从一个blocked fasta 文件中替换标题

separate  separate blocked fasta files by species
分组  按照物种分组blocked fasta文件

slice  extract alignment slices from a blocked fasta
切割  从blocked fasta文件中提取可比对的片段

split  split blocked fasta files to separate per-alignment files
分离  将blocked fasta文件分割成比对前文件

stat  basic statistics on alignments
统计  比对文件的基本数据统计

subset  extract a subset of species from a blocked fasta
子文件  提取一个blocked fasta文件的一个物种的子文件

xlsx  paint substitutions and indels to an excel file
xlsx  在一个excel文件中绘制替换和缺失
```

### 2. axt2fas
axt2fas文件转换

```bash
输入：
fasops axt2fas [options] <infile> [more infiles]
fasops axt2fas  [选项] <输入文件> [多个输入文件]

Convert UCSC axt pairwise alignment file to blocked fasta file.
将UCSC axt成对文件转换为blocked fasta文件

       <infiles> are paths to axt files, .axt.gz is supported
       <输入文件>是axt文件的路径，.axt.gz也是可以支持的。
	infile == stdin means reading from STDIN

Options:
选项：

       -o STR --outfile STR  Output filename, [stdout] for screen
       -o STR --outfile STR  输出文本的文件名 [stdout]输出到屏幕

       -l INT --length INT   the threshold of alignment length
	                      (default value: 1)
       -l INT --length INT   对比序列的长度
                             (缺省值：1)

       -t STR --tname STR    target name
	                      (default value: target)
       -t STR --tname STR    靶标的名字
                             (缺省值为target）

       -q STR --qname STR    query name
	                      (default value: query)
       -q STR --qname STR    查询的名字
                             (缺省值为query）

       -s STR --size STR     query chr.sizes. Without this file, positions of negtive strand of query will be wrong
       -s STR --size STR     查询染色体的大小. 如果缺少这个文件，负链位置可能会出错
```



#### Example

```bash
实例：
fasops axt2fas -o example_axt2fas example.axt
fasops axt2fas -o example_axt2fas_2 -t S288c -q RM11_1a -l 100 example.axt
fasops axt2fas -o example_axt2fas_3 -t S288c -q RM11_1a -s RM11_1a.chr.sizes example.axt


解释：
- 首先我们会从网站上下载得到axt文件，例如example.axt：

4 I 2717 2798 scaffold_14 3634 3714 + 5667
ACTTGTGAACTCTCGGCAAATGCCTTGGTGCAATTACGTAATTTTAGCCGCTGAGAAGCGGATGGTAATGAGACAAGTTGAT
ATTTGTGAATTCTCCGCGAATGCCTTGGTGCAATTACGTAATTGTAGCCGCTGA-AGGCGGATGGTTTTGAGAGAAGTTGAT

22 I 201838 201959 scaffold_17 174845 174965 - 5565
AATTACAAGTAATTACTTGTAGGAACATCCTCTACTAGTGAATATGAAAGAGCAGAG---GTTAGCTCCGTCTC--AACCAATTTTGTACAAGTCGTTGAAAAG--GACGGCTCTACTGTAGACAGGTC
AATTACAAATCATCACTTTCATGAACATCCTCTACTAGCTATTATGAAGGCGCAGAGAGTG--------GTCTCGTAACCAATTTTATGCAAGTCGTTGAAAAAACGGCGGCTCTTTAGTAGACTGGTC

axt比对文件是有Blastz产生的，每一个部分（block）包含了一个总结行（a summary line）和两个序列行（2 sequence lines），每个部分之间用空行隔开。[UCSC axt format](https://genome.ucsc.edu/goldenPath/help/axt.html)
以example.axt中第一个部分为例：
**4**是Alignment number，
**I**是Chromosome(primary organism)，
**2717 2798**是Alignment start(primary organism)和Alignment end(primary organism)，
**scaffold_14**是Chromosome(aligning organism)，
**3634 3714**是Alignment start(aligning organism)和Alignment end(aligning organism)，
**+**是Strand(aligning organism)，
**5565**是Blastz score。

- 通过实例中第一个操作，我们可以将axt文件转变成fasta文件，即example_axt2fas：

>target.I(+):2717-2798
ACTTGTGAACTCTCGGCAAATGCCTTGGTGCAATTACGTAATTTTAGCCGCTGAGAAGCGGATGGTAATGAGACAAGTTGAT
>query.scaffold_14(+):3634-3714
ATTTGTGAATTCTCCGCGAATGCCTTGGTGCAATTACGTAATTGTAGCCGCTGA-AGGCGGATGGTTTTGAGAGAAGTTGAT

>target.I(+):201838-201959
AATTACAAGTAATTACTTGTAGGAACATCCTCTACTAGTGAATATGAAAGAGCAGAG---GTTAGCTCCGTCTC--AACCAATTTTGTACAAGTCGTTGAAAAG--GACGGCTCTACTGTAGACAGGTC
>query.scaffold_17(-):174845-174965
AATTACAAATCATCACTTTCATGAACATCCTCTACTAGCTATTATGAAGGCGCAGAGAGTG--------GTCTCGTAACCAATTTTATGCAAGTCGTTGAAAAAACGGCGGCTCTTTAGTAGACTGGTC

这里的target相当于上文中的primary organism，而query相当于上文中的aligning organism。

- 如果我们想要重新命名**target**和**query**，那么需要通过实例中第二个操作，得到example_axt2fas_2：

>S288c.I(+):201838-201959
AATTACAAGTAATTACTTGTAGGAACATCCTCTACTAGTGAATATGAAAGAGCAGAG---GTTAGCTCCGTCTC--AACCAATTTTGTACAAGTCGTTGAAAAG--GACGGCTCTACTGTAGACAGGTC
>RM11_1a.scaffold_17(-):174845-174965
AATTACAAATCATCACTTTCATGAACATCCTCTACTAGCTATTATGAAGGCGCAGAGAGTG--------GTCTCGTAACCAATTTTATGCAAGTCGTTGAAAAAACGGCGGCTCTTTAGTAGACTGGTC

注意：由于我们在代码中设置了-l 100，即比对长度最少要有100nt，因此在example_axt2fas_2中去除了比对长度较小的第一部分。

- RM11_1a.chr.sizes是query每一条染色体大小的文件：
scaffold_1	1481020
scaffold_10	586214
scaffold_11	550861
scaffold_12	536628
scaffold_13	469589
scaffold_14	412858
scaffold_15	307626
scaffold_16	255049
scaffold_17	19769
scaffold_2	1074767
scaffold_3	1056105
scaffold_4	916809
scaffold_5	920105
scaffold_6	795018
scaffold_7	784941
scaffold_8	723081
scaffold_9	668618
当我们使用实例中第三个操作时，可以得到example_axt2fas_3:

>S288c.I(+):2717-2798
ACTTGTGAACTCTCGGCAAATGCCTTGGTGCAATTACGTAATTTTAGCCGCTGAGAAGCGGATGGTAATGAGACAAGTTGAT
>RM11_1a.scaffold_14(+):3634-3714
ATTTGTGAATTCTCCGCGAATGCCTTGGTGCAATTACGTAATTGTAGCCGCTGA-AGGCGGATGGTTTTGAGAGAAGTTGAT

>S288c.I(+):201838-201959
AATTACAAGTAATTACTTGTAGGAACATCCTCTACTAGTGAATATGAAAGAGCAGAG---GTTAGCTCCGTCTC--AACCAATTTTGTACAAGTCGTTGAAAAG--GACGGCTCTACTGTAGACAGGTC
>RM11_1a.scaffold_17(-):22732-22852
AATTACAAATCATCACTTTCATGAACATCCTCTACTAGCTATTATGAAGGCGCAGAGAGTG--------
GTCTCGTAACCAATTTTATGCAAGTCGTTGAAAAAACGGCGGCTCTTTAGTAGACTGGTC

我们会发现RM11_1a.scaffold_17(-)中显示的位置发生了改变，即显示的是在正链中的位置：22732-22852。
```


### 3. check
检查

```bash
输入：
fasops check [options] <infile> <genome.fa>
fasops check [选项] <输入文件> <genome.fa>

Check genome locations in (blocked) fasta headers.
在（blocked）fasta的标题检索基因组位置

       <infile> is the path to blocked fasta file, .fas.gz is supported.
       infile == stdin means reading from STDIN
       <输入文件>是blocked fasta文件的路径，.fas.gz 也是支持的

       <genome.fa> is one multi fasta file contains genome sequences.

       <genome.fa>是一个多fasta文件，包含了基因组文件

Options:
选项：

       -o STR --outfile STR  Output filename. [stdout] for screen.
       -o STR --outfile STR  输出文本的文件名 [stdout]输出到屏幕

       -n STR --name STR     Which species to be checked, omit this will check all sequences
       -n STR --name STR     只检测这个物种
```



#### Example

```bash
实例：
fasops check -o example_check Arabid_thaliana.pair.fas NC_000932.fa
（需要安装samtools：sudo agt-get install samtools）
fasops check -o example_check_2 --name Arabid_thaliana Arabid_thaliana.pair.fas NC_000932.fa

解释：
NC_000932.fa是Arabidopsis thaliana chloroplast的全基因组文件,全长154,478 bp，circular DNA。
Arabid_thaliana.pair.fas是我们需要检查的序列

- 通过实例中第一个操作，我们可以得到example_check:
Arabid_thaliana.NC_000932(+):84171-110434	OK
Arabid_thaliana.NC_000932(-):128215-154478	OK
FAKE.NC_000932(+):84171	FAILED
说明在Arabid_thaliana.pair.fas中第一个和第二个序列都是存在于NC_000932.fa中，而专门设置的Fake是不存在的。

- 通过实例中第二个操作，我们可以得到example_check_2:
Arabid_thaliana.NC_000932(+):84171-110434	OK
Arabid_thaliana.NC_000932(-):128215-154478	OK
我们发现由于设置了name，因此只会检查名字中含有Arabid_thaliana的block，其他的都不被检查。
```

### 4. concat
连接

```bash
输入：
fasops concat [options] <infile> <name.list>
fasops concat [选项] <输入文件> <名单>

Concatenate sequence pieces in blocked fasta files.
将序列片段链接成blocked fasta文件

       <infile> is the path to blocked fasta file, .fas.gz is supported.
       <输入文件>  是blocked fasta文件的路径，.fas.gz 也是支持的
       infile == stdin means reading from STDIN

       <name.list> is a file with a list of names to keep, one per line.
       <名单>是一个名单的文件，一行一个
	Names in the output file will following the order in <name.list>.

Options:
选项：

       -o STR --outfile STR  Output filename. [stdout] for screen
       -o STR --outfile STR  输出文本的文件名 [stdout]输出到屏幕

       -t INT --total INT    Stop when exceed this length
	                     (default value: 10000000)
       -t INT --总INT    当超过这个长度时就停止
                             (缺省值：10000000)

       --relaxed             output relaxed phylip instead of fasta
       --relaxed             以relaxed phylip输出，而不是fasta
```



#### Example

```bash
实例：
fasops concat -o example_concat example.fas example.name.list
fasops concat -o example_concat_2 example.fas --relaxed example.name.list
fasops concat -o example_concat_3 -t 100  example.fas example.name.list

解释：
- name.list是一个名单，是我们想要连接的物种的名称，例如example.name.list：

Spar
YJM789

- 而example.fas是一个经过比对后形成的fasta文件：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

- 通过实例中的第一个操作我们可以得到example_concat：

>Spar
TCATCAGTTGGCAAACCGTTAAAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTTGC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--
>YJM789
TCGTCAGTTGGTTGACCATTAAAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTTGCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA

即把example.fas每一个block中Spar和YJM789的序列按照顺序连接在一起。

- 通过实例中的第二个操作我们可以得到example_concat_2：

2 239
Spar TCATCAGTTGGCAAACCGTTAAAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTTGC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--
YJM789 TCGTCAGTTGGTTGACCATTAAAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTTGCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA

输出的格式是[Relaxed Phylip Format](http://scikit-bio.org/docs/0.2.3/generated/skbio.io.phylip.html)，这是一种多序列比对格式，最早源自于Joe Felsenstein’s PHYLIP package，而现阶段适用于其他多种生物信息学工具（例如 RAxML）
**2**是the number of sequences，
**239**是the length of the sequences。
```

### 5. covers
覆盖

```bash
输入：
fasops covers [options] <infile> [more infiles]
fasops covers [选项] <输入文件> [更多输入文件]

Scan blocked fasta files and output covers on chromosomes.
扫描 blocked fasta文件，输出可以覆盖染色体的部分

	<infiles> are blocked fasta files, .fas.gz is supported.
        <输入文件>  是blocked fasta文件的路径，.fas.gz 也是支持的
	infile == stdin means reading from STDIN

Options:
选项：

       -o STR --outfile STR  Output filename. [stdout] for screen
       -o STR --outfile STR  输出文本的文件名 [stdout]输出到屏幕

       -n STR --name STR     Only output this species
       -n STR --name STR     只输出这个物种

       -l INT --length INT   the threshold of alignment length
	                     (default value: 1)
       -l INT --length INT   对比序列的长度
                             (缺省值：1)

       -t INT --trim INT     Trim align borders to avoid some overlaps in lastz results
	                     (default value: 0)
       -t INT --trim INT     修整比对的边界，避免在lastz结果中有重叠
                             (缺省值：1）
```



#### Example

```bash
实例：
fasops covers -o example_covers  example.fas
fasops covers -o example_covers_2 -n S288c -l 50 -t 10 example.fas

解释：
- example.fas是一个经过比对后形成的fasta文件：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

- 通过实例中的第一个操作我们可以得到example_covers：

--- 
RM11: 
  gi_61385832: 5590-5610
  gi_61385833: 3291-3445,3668-3730
S288c: 
  I: 13267-13287,184896-185050,185273-185334
Spar: 
  gi_29362400: 2477-2497
  gi_29362478: 1719-1873,2102-2161
YJM789: 
  gi_151941327: 5668-5688,156291-156445,156665-156726

即可以得到example.fas文件中所有可以覆盖染色体的部分。

- 通过实例中第二个操作我们可以得到example_covers_2：

--- 
I: 184906-185040,185283-185324

由于我们在代码中设置了-n,-l 和-t，我们在example_covers_2结果中只会得到S288c，并且去除了其中比对长度小于50的部分，还对于比对区域做了修正，前后各去除了10nt，避免在[lastz](http://www.bx.psu.edu/miller_lab/dist/README.lastz-1.02.00/README.lastz-1.02.00a.html)结果中有重叠。
```



### 6. join
添加

```bash
输入：
fasops join [options] <infile> [more infiles]
fasops join [选项] <输入文件> [更多文件]

Join multiple blocked fasta files by common target.
通过共有标签添加多个blocked fasta文件

       <infiles> are blocked fasta files, .fas.gz is supported.
       <输入文件>  是blocked fasta文件的路径，.fas.gz 也是支持的
	infile == stdin means reading from STDIN

Options：
选项：

       -o STR --outfile STR  Output filename. [stdout] for screen
       -o STR --outfile STR  输出文本的文件名 [stdout]输出到屏幕

       -n STR --name STR     According to this species. Default is the first one
       -n STR --名字 STR     根据这个物种，缺省值就是第一个
```



#### Example

```bash
实例：
fasops join -o example_join -n S288c S288cvsRM11_1a.slice.fas S288cvsYJM789.slice.fas S288cvsSpar.slice.fas

解释：
S288cvsRM11_1a.slice.fas, S288cvsYJM789.slice.fas, S288cvsSpar.slice.fas为S288c分别与其他三个物种之间序列比对的fasta文件，而通过上述实例，可以根据S288c将三个文件合并成一个fasta文件。
```



### 7.  links
链接


```bash
输入：
fasops links [options] <infile> [more infiles]
fasops links [选项] <输入文件> [更多文件]

Scan blocked fasta files and output bi/multi-lateral range links.
扫描blocked fasta文件并且输出两边或是多边范围链接

       <infiles> are paths to blocked fasta files, .fas.gz is supported.
       <输入文件>  是blocked fasta文件的路径，.fas.gz 也是支持的
       infile == stdin means reading from STDIN


Options:
选项：

       -o STR --outfile STR  Output filename. [stdout] for screen.
       -o STR --outfile STR  输出文本的文件名 [stdout]输出到屏幕

       -p --pair             pairwise links
       -p --pair             配对的链接

       -b --best             best-to-best pairwise links
       -b --best             最优对最优的配对链接
```



#### Example

```bash
实例：
fasops links -o example_links -p example.fas
fasops links -o example_links_2 -b example.fas
fasops links -o example_links_3 example.fas

解释：
- example.fas是一个经过比对后形成的fasta文件：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

- 通过实例中的第一个操作可以得到example_links：

S288c.I(+):13267-13287	YJM789.gi_151941327(-):5668-5688
S288c.I(+):13267-13287	RM11.gi_61385832(-):5590-5610
S288c.I(+):13267-13287	Spar.gi_29362400(+):2477-2497
YJM789.gi_151941327(-):5668-5688	RM11.gi_61385832(-):5590-5610
YJM789.gi_151941327(-):5668-5688	Spar.gi_29362400(+):2477-2497
RM11.gi_61385832(-):5590-5610	Spar.gi_29362400(+):2477-2497
S288c.I(+):184896-185050	YJM789.gi_151941327(+):156291-156445
S288c.I(+):184896-185050	RM11.gi_61385833(-):3291-3445
S288c.I(+):184896-185050	Spar.gi_29362478(-):1719-1873
YJM789.gi_151941327(+):156291-156445	RM11.gi_61385833(-):3291-3445
YJM789.gi_151941327(+):156291-156445	Spar.gi_29362478(-):1719-1873
RM11.gi_61385833(-):3291-3445	Spar.gi_29362478(-):1719-1873
S288c.I(+):185273-185334	YJM789.gi_151941327(+):156665-156726
S288c.I(+):185273-185334	RM11.gi_61385833(-):3668-3730
S288c.I(+):185273-185334	Spar.gi_29362478(-):2102-2161
YJM789.gi_151941327(+):156665-156726	RM11.gi_61385833(-):3668-3730
YJM789.gi_151941327(+):156665-156726	Spar.gi_29362478(-):2102-2161
RM11.gi_61385833(-):3668-3730	Spar.gi_29362478(-):2102-2161

即每一个block中两两序列之间可以比对的区域。

- 通过实例中的第二个操作可以得到example_links_2：

S288c.I(+):13267-13287	YJM789.gi_151941327(-):5668-5688
S288c.I(+):13267-13287	RM11.gi_61385832(-):5590-5610
S288c.I(+):13267-13287	Spar.gi_29362400(+):2477-2497
S288c.I(+):184896-185050	RM11.gi_61385833(-):3291-3445
S288c.I(+):184896-185050	YJM789.gi_151941327(+):156291-156445
S288c.I(+):184896-185050	Spar.gi_29362478(-):1719-1873
S288c.I(+):185273-185334	RM11.gi_61385833(-):3668-3730
S288c.I(+):185273-185334	YJM789.gi_151941327(+):156665-156726
RM11.gi_61385833(-):3668-3730	Spar.gi_29362478(-):2102-2161

这里得到的是每一个block中最优对最优的比对区域。

- 通过实例中的第三个操作可以得到example_links_3：

S288c.I(+):13267-13287	YJM789.gi_151941327(-):5668-5688	RM11.gi_61385832(-):5590-5610 Spar.gi_29362400(+):2477-2497
S288c.I(+):184896-185050	YJM789.gi_151941327(+):156291-156445	RM11.gi_61385833(-):3291-3445 Spar.gi_29362478(-):1719-1873
S288c.I(+):185273-185334	YJM789.gi_151941327(+):156665-156726	RM11.gi_61385833(-):3668-3730 Spar.gi_29362478(-):2102-2161

这里得到的就是简单的将每一个block可以比对的区域进行提取。
```



### 8. maf2fas  
maf2fas文件转换

```bash
输入：
fasops maf2fas [options] <infile> [more infiles]
fasops maf2fas [选项] <输入文件> [多个输入文件]

Convert UCSC maf multiple alignment file to blocked fasta file.
将UCSC maf多序列比对文件转换为blocked fasta文件

       <infiles> are paths to maf files, .maf.gz is supported
       <输入文件>是maf文件的路径，.maf.gz也是可以支持的。
       infile == stdin means reading from STDIN


Options:
选项：

       -o STR --outfile STR  Output filename. [stdout] for screen
       -o STR --outfile STR  输出文本的文件名 [stdout]输出到屏幕

       -l INT --length INT   the threshold of alignment length
	                     (default value: 1)
       -l INT --length INT   对比序列的长度
                             (缺省值：1)
```



#### Example

```bash
实例：
fasops maf2fas -o example_maf2fas example.maf
fasops maf2fas -o example_maf2fas_2 -l 100 example.maf

解释：
- 首先我们会从网上下载得到maf文件，例如example.maf：

##maf version=1 scoring=multiz
# multiz.v11.2 M=10 /home/wangq/data/alignment/example/scer/Scer_n3_Spar_mz/VIII.step1.maf /home/wangq/data/alignment/example/scer/Pairwise/S288cvsSpar/mafSynNet/VIII.synNet.maf 1 /home/wangq/data/alignment/example/scer/Scer_n3_Spar_mz/VIII.out1 /home/wangq/data/alignment/example/scer/Scer_n3_Spar_mz/VIII.out2 
# multiz.v11.2 M=10 /home/wangq/data/alignment/example/scer/Pairwise/S288cvsRM11_1a/mafSynNet/VIII.synNet.maf /home/wangq/data/alignment/example/scer/Pairwise/S288cvsEC1118/mafSynNet/VIII.synNet.maf 1 /home/wangq/data/alignment/example/scer/Scer_n3_Spar_mz/VIII.out1 /home/wangq/data/alignment/example/scer/Scer_n3_Spar_mz/VIII.out2 
a score=514600.0
s S288c.VIII          13376 34 + 562643 TTACTCGTCTTGCGGCCAAAACTCGAAGAAAAAC
s RM11_1a.scaffold_12  3529 34 + 536628 TTACTCGTCTTGCGGCCAAAACTCGAAGAAAAAC
s EC1118.FN393072_1    8746 34 + 161280 TTACTCGTCTTGCGGCCAAAACTCGAAGAAAAAC
s Spar.gi_29362578      637 33 -  73522 TTACCCGTCTTGCGTCCAAAACTCGAA-AAAAAC

a score=36468.0
s S288c.VIII          193447  99 + 562643 CG--GCATAATTTTTTCCAGGCACTTTCCGCTGCAG---TTGTTGTGCTGACAATAGTCCCATCTAGGTCAAAAAGACAAAGATCTACTGAAAATTGTGGCAtt
s RM11_1a.scaffold_12 189216 101 + 536628 CGTAACACAACTTGGTCCATGC---TTTCTCTGCGGCCACTGTTGTACTCACTATGGTACCATCTAGGTCAAAAAGACATAGATCAGCTGAAAATTCTGCCATT
s EC1118.FN393073_1    25682  99 +  44323 CG--GCATAATTTTTTCCAGGCACTTTCCGCTGCAG---TTGTTGTGCTGACAATAGTCCCATCTAGGTCAAAAAGACAAAGATCTACTGAAAATTGTGGCAtt
s Spar.gi_29362604    100946  97 - 143114 CG--ACATAGTTTTTTCCAGGCACTTTCAGCTGCGG---TTGTTGTGCTAACAATGGTCCCATCTAGGTCAAAAAGGCAGAGATCTACTGAAAATTGTGGCA--

MAF（[multiple alignment format](https://genome.ucsc.edu/FAQ/FAQformat#format5)）是一种存储多序列比对文件的格式，它可以存储整个基因组DNA水平的多序列比对文件，但易于处理也易于阅读。maf格式是基于行的，没一个多序列比对文件都以空行作为结束。一个对比中的每一个序列都占单独一行，可以很长且没有限制。行以**#**开头时，被认为是评论；以##开头会被绝大多数的程序忽略。
一个文件由多个空行分隔的段落组成。在每一个段落中，每一行的首字母代表了它的类型。通常每一个多序列比对都是以一个字母**a**作为分隔段的开始行的首字母，然后包含了多个以**s**开始的行是比对的序列。一些MAF文件还会包括其他的行类型：**i**行包含的信息是前述的**s**行的DNA物种信息，**e**行包含的信息是比对文件中间隔（gap）的大小。**q**行指的是物种间每个对比碱基的质量。
上面example.maf中##maf...是标题行，**version=1**，**scoring=multiz**，以#显示的是评论行，用来描述比对程序中的用到的参数。

- 通过实例中第一个操作，我们可以将maf文件转变成fasta文件，即example_maf2fas：

>S288c.VIII(+):13377-13410
TTACTCGTCTTGCGGCCAAAACTCGAAGAAAAAC
>RM11_1a.scaffold_12(+):3530-3563
TTACTCGTCTTGCGGCCAAAACTCGAAGAAAAAC
>EC1118.FN393072_1(+):8747-8780
TTACTCGTCTTGCGGCCAAAACTCGAAGAAAAAC
>Spar.gi_29362578(-):638-670
TTACCCGTCTTGCGTCCAAAACTCGAA-AAAAAC

>S288c.VIII(+):193448-193546
CG--GCATAATTTTTTCCAGGCACTTTCCGCTGCAG---TTGTTGTGCTGACAATAGTCCCATCTAGGTCAAAAAGACAAAGATCTACTGAAAATTGTGGCAtt
>RM11_1a.scaffold_12(+):189217-189317
CGTAACACAACTTGGTCCATGC---TTTCTCTGCGGCCACTGTTGTACTCACTATGGTACCATCTAGGTCAAAAAGACATAGATCAGCTGAAAATTCTGCCATT
>EC1118.FN393073_1(+):25683-25781
CG--GCATAATTTTTTCCAGGCACTTTCCGCTGCAG---TTGTTGTGCTGACAATAGTCCCATCTAGGTCAAAAAGACAAAGATCTACTGAAAATTGTGGCAtt
>Spar.gi_29362604(-):100947-101043
CG--ACATAGTTTTTTCCAGGCACTTTCAGCTGCGG---TTGTTGTGCTAACAATGGTCCCATCTAGGTCAAAAAGGCAGAGATCTACTGAAAATTGTGGCA--

- 通过实例中第二个操作，我们可以将maf文件转变成fasta文件，即example_maf2fas_2：

>S288c.VIII(+):193448-193546
CG--GCATAATTTTTTCCAGGCACTTTCCGCTGCAG---TTGTTGTGCTGACAATAGTCCCATCTAGGTCAAAAAGACAAAGATCTACTGAAAATTGTGGCAtt
>RM11_1a.scaffold_12(+):189217-189317
CGTAACACAACTTGGTCCATGC---TTTCTCTGCGGCCACTGTTGTACTCACTATGGTACCATCTAGGTCAAAAAGACATAGATCAGCTGAAAATTCTGCCATT
>EC1118.FN393073_1(+):25683-25781
CG--GCATAATTTTTTCCAGGCACTTTCCGCTGCAG---TTGTTGTGCTGACAATAGTCCCATCTAGGTCAAAAAGACAAAGATCTACTGAAAATTGTGGCAtt
>Spar.gi_29362604(-):100947-101043
CG--ACATAGTTTTTTCCAGGCACTTTCAGCTGCGG---TTGTTGTGCTAACAATGGTCCCATCTAGGTCAAAAAGGCAGAGATCTACTGAAAATTGTGGCA--

由于我们设置了 -l 100 因此长度小于100nt的比对会被去除。 
```



### 9. names  
名字

```bash
输入：
fasops names [options] <infile> [more infiles]
fasops names [选项] <输入文件> [多个输入文件]

Scan blocked fasta files and output all species names.
扫描blocked fasta文件，输出所有物种的名字

	<infiles> are blocked fasta files, .fas.gz is supported.
	<infiles>是blocked fasta文件的路径，.fas.gz 也是支持的
       infile == stdin means reading from STDIN

Option:
选项：

       -o STR --outfile STR  Output filename. [stdout] for screen
       -o STR --outfile STR  输出文本的文件名 [stdout]输出到屏幕

       -c --count            Also count name occurrences
       -c --count            写出名字的数量
```



#### Example

```bash
实例：
fasops names -o example_names example.fas

解释：
- example.fas是一个经过比对后形成的fasta文件：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

- 通过实例中的操作，可以得到example_names:

S288c
YJM789
RM11
Spar
```



### 10. refine  
优化

```bash
输入：
fasops refine [options] <infile>
fasops refine [选项] <输入文件>

Realign blocked fasta file with external programs.
根据外置程序重排blocked fasta文件

List of msa:
       * mafft
       * muscle
       * clustalw
       * none:	means skip realigning

       <infile> are paths to blocked fasta files, .fas.gz is supported.
       <输入文件>是blocked fasta文件的路径，.maf.gz也是可以支持的
	infile == stdin means reading from STDIN

       -o STR --outfile STR   Output filename. [stdout] for screen
       -o STR --outfile STR   输出文本的文件名 [stdout]输出到屏幕

       --outgroup             Has outgroup at the end of blocks
       --outgroup             在blocks最后显示外类群

       -p INT --parallel INT  run in parallel mode
	                       (default value: 1)
       -p INT --parallel INT  平行模式运行
                              (缺省值为1）

       --msa STR            Aligning program
	                      (default value: mafft)
       --msa STR            比对项目
                              (缺省值为mafft)

       --quick              Quick mode, only aligning indel adjacent regions.
                               Suitable for multiz outputs
       --quick              快速模式，只比对indel连接处
                               适用于多输出

       --pad INT            In quick mode, enlarge indel regions
	                      (default value: 50)
       --pad INT            在快速模式中，最大化indel区域
                              (缺省值为50）

       --fill INT           In quick mode, join indel regions
	                      (default value: 50)
       --fill INT           在快速模式中，加入indel区域
                              (缺省值为50）

       --chop INT           Chop head and tail indels
	                      (default value: 0)
       --chop INT           切掉头尾的indel
                              (缺省值为0）
```



#### Example

```bash
实例：
fasops refine -o example_refine refine.fas 
fasops refine --msa none -o example_refine_2 -p 2 example.fas
fasops refine --msa muscle --quick -o example_refine_3 refine.fas
fasops refine --msa muscle -o example_refine_4 refine2.fas 
fasops refine --msa muscle -o example_refine_5 --outgroup refine2.fas
fasops refine --msa none -o example_refine_6 example.fas
fasops refine --msa none -o example_refine_7 refine2.fas
fasops refine --msa none -o example_refine_8 --chop 10 example.fas

ps：
需要先安装mafft和muscle：
sudo apt-get install aptitude
sudo aptitude install mafft
sudo aptitude install muscle


解释：
- example.fas是一个经过比对后形成的fasta文件：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

- refine.fas文件：

>S288c
TCGTCAGTTGGTTGACCATTA
>YJM789
TCGTCAGTTGGTTGACCATTA
>RM11
TCGTCAGTTGGTTGACCATTA
>Spar
TCATCAGTTGGACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCATTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

- refine2.fas文件：

>S288c
TCGTCA--GTAGGTTTACCA
>RM11
TCGTCAC-GTAGGTTTACCA
>Spar
TCGTCACAGTAGG---ACCG


- 通过实例中的第一个操作我们可以得到example_refine：

>S288c
TCGTCAGTTGGTTGACCATTA
>YJM789
TCGTCAGTTGGTTGACCATTA
>RM11
TCGTCAGTTGGTTGACCATTA
>Spar
TCATCAGTTGG---ACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCA---TTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCA--------CGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

通过对比refine.fas和example_refine，我们可以发现多序列比对文件被修正，特别是indel区域被标识。实例中第一个操作默认的比对方式是mafft。

- 通过实例中的第二个操作我们可以得到example_refine_2：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

- 通过实例中的第三个操作我们可以得到example_refine_3：

>S288c
TCGTCAGTTGGTTGACCATTA
>YJM789
TCGTCAGTTGGTTGACCATTA
>RM11
TCGTCAGTTGGTTGACCATTA
>Spar
TCATCAGTTGGACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCATTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

- 通过实例中的第四个操作我们可以得到example_refine_4：

>S288c
TCGTCA--GTAGGTTTACCA
>RM11
TCGTCAC-GTAGGTTTACCA
>Spar
TCGTCACAGTAGG---ACCG

- 通过实例中的第五个操作我们可以得到example_refine_5：

>S288c
TCGTCA-GTAGGTTTACCA
>RM11
TCGTCACGTAGGTTTACCA
>Spar
TCGTCACGTAGG---ACCG

- 通过实例中的第六个操作我们可以得到example_refine_6：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

- 通过实例中的第七个操作我们可以得到example_refine_7：

>S288c
TCGTCA--GTAGGTTTACCA
>RM11
TCGTCAC-GTAGGTTTACCA
>Spar
TCGTCACAGTAGG---ACCG

- 通过实例中的第八个操作我们可以得到example_refine_8：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185276-185332|species=S288c
TATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTC
>YJM789.gi_151941327(+):156668-156724|species=YJM789
TATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTC
>RM11.gi_61385833(-):3670-3727|species=RM11
TATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTC
>Spar.gi_29362478(-):2102-2159|species=Spar
TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT
​```



### 11. replace
替换

```bash
输入：
fasops replace [options] <infile> <replace.tsv>
fasops replace [选项] <输入文件> <replace.tsv>

Replace headers from a blocked fasta.
从一个blocked fasta 文件中替换标题

       <infile> is the path to blocked fasta file, .fas.gz is supported.
       <输入文件>是blocked fasta文件的路径，.maf.gz也是可以支持的
	infile == stdin means reading from STDIN

       <replace.tsv> is a tab-separated file containing more than one fields.
       <replace.tsv>是一个tab-separated 文件，包含不止一个部分

	     original_name	 replace_name	      more_replace_name
              原始的名字           替换的名字            更多替换的名字

	   With one field will delete the whole alignment block.
              如果只有一个部分会去除整个比对的block

	   With three or more fields will duplicate the whole alignment block.
              如果有三个或更多的部分会复制整个比对的block

Options:
选项：

       -o STR --outfile STR  Output filename. [stdout] for screen.
       -o STR --outfile STR  输出文本的文件名 [stdout]输出到屏幕
```



#### Example

```bash
实例：
fasops replace -o example_replace example.fas replace.tsv
fasops replace -o example_replace_2  example.fas replace.fail.tsv
fasops replace -o example_replace_3  example.fas replace.remove.tsv

解释：
- example.fas是一个经过比对后形成的fasta文件：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

- 通过实例中第一个操作，我们可以得到一个新的文件example_replace：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>query.XIV(-):774355-775596
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>target.VIII(+):214264-216251
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>query.VIII(+):212266-214253
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

当我们打开replace.tsv文件后可以看到：

|:------------------------------:|:--------------------------:|:-------------------------:|
|YJM789.gi_151941327(-):5668-5688|query.XIV(-):774355-775596  |	                          |
|RM11.gi_61385833(-):3291-3445	 |target.VIII(+):214264-216251|query.VIII(+):212266-214253|

因此在原文件example.fas中，第一个block的第二个序列名称由YJM789.gi_151941327(-):5668-5688改成了query.XIV(-):774355-775596，而第二个block中第三个序列名称RM11.gi_61385833(-):3291-3445要替换成两个新的名称：target.VIII(+):214264-216251和query.VIII(+):212266-214253，因此在example_replace中形成了两个新的block，并在相应位置进行了替换。

- 通过实例中第二个操作，我们可以得到一个新的文件example_replace_2：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

我们发现经过操作后的fasta文件与原文件没有差别，并且在运行命令的时候，出现报错：
Don't support multiply records in one block. S288c.I(+):185273-185334 Spar.gi_29362478(-):2102-2161

因此我们打开replace.fail.tsv：

|:---------------------------:|:--------------------------:|
|S288c.I(+):185273-185334     |target.VIII(+):212266-214253|
|Spar.gi_29362478(-):2102-2161|query.VIII(+):212266-214253 |

发现S288c.I(+):185273-185334和Spar.gi_29362478(-):2102-2161存在于同一个block中，由于软件不支持同时在一个block中进行替换，因此出现报错。

- 通过实例中第三个操作，我们可以得到一个新的文件example_replace_3：

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

我们发现在example_replace_3中相比于原文件少了一个block。
因此我们打开replace.remove.tsv：

|:------------------------------:|
|YJM789.gi_151941327(-):5668-5688|

发现只有原序列名称而没有新的名称，因此执行的命令是将原序列所在的block整体删除。
```



### 12. separate
分组

```bash
输入：
fasops separate  [options] <infile> [more infiles]
fasops separate [选项] <输入文件> [更多的输入文件]

Separate blocked fasta files by species.
按照物种分组blocked fasta文件

       <infiles> are paths to blocked fasta files, .fas.gz is supported.
       <输入文件>是blocked fasta文件的路径，.maf.gz也是可以支持的
	infile == stdin means reading from STDIN

Options:
选项：

       -o STR --outdir STR  Output location, [stdout] for screen
	                    (default value: .)
       -o STR --outdir STR  输出位置， [stdout]输出到屏幕
                            (缺省值为 .)

       -s STR --suffix STR  Extensions of output files
	                    (default value: .fasta)
       -s STR --suffix STR  输出文件的扩展名
                            (缺省值为 .fasta)

       -r --rm              If outdir exists, remove it before operating
       -r --rm              如果outdir存在，在操作前去除

       --rc                 Revcom sequences when chr_strand is '-'
       --rc                 revcom 序列当chr_strand是'-'

       --nodash             Remove dashes ('-') from sequences
       --nodash             将序列中的dash ('-') 去除
```



#### Example

```bash
实例：
fasops separate -o example_separate example.fas
fasops separate -o example_separate_2 --nodash --rc example.fas

解释：
- example.fas是一个经过比对后形成的fasta文件：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

- 通过实例中第一个操作，我们可以得到一个新的目录example_separate，里面包含了四个新的文件：
RM11.fasta  S288c.fasta  Spar.fasta  YJM789.fasta

我们打开其中一个文件RM11.fasta：

>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA

因此这个命令是将example.fas中每个block的相同物种的序列进行提取，分组成一个新的文件，最终形成一个新的目录。

- 通过实例中第二个操作，我们可以得到一个新的目录example_separate_2，里面包含了四个新文件：
RM11.fasta  S288c.fasta  Spar.fasta  YJM789.fasta

我们打开其中一个文件RM11.fasta：

>RM11.gi_61385832(+):5590-5610|species=RM11
TAATGGTCAACCAACTGACGA
>RM11.gi_61385833(+):3291-3445|species=RM11
AAACATGATCGGTTTACAATGAGCAATTTCATAAAGTACATAACTCAGATGTGAACTGAATGTGTCTTCGGGAAGAGCTACATTCTCTTCGATTAAATGTGCACTGGGTTCGTCGAGAGTATCCATCTTAACGTCGGTATTTTCTGAAGGTGTTT
>RM11.gi_61385833(+):3668-3730|species=RM11
TAGAAATAGTACGGGGATATCATAGTCTCTCCATGAAATAGATATTGGTTCATATTATATGC

我们发现新文件相比与实例中第一个操作得到的序列有差别，即去除了序列中的dash，并且将原文件中所有的负链序列转变为正链序列。
```



### 13. slice
切割

```bash
输入：
fasops slice [options] <infile> <runlist.yml>
fasops slice [选项] <输入文件> <runlist,yml>

Extract alignment slices from a blocked fasta.
从blocked fasta文件中提取可比对的片段

       <infile> is the path to blocked fasta file, .fas.gz is supported.
       <输入文件>是blocked fasta文件的路径，.maf.gz也是可以支持的。
	infile == stdin means reading from STDIN

       <runlist.yml> is a App::RL dump.
       <runlist.yml>是一个App::RL dump（sudo cpanm App::RL dump）

       -o STR --outfile STR  Output filename. [stdout] for screen.
       -o STR --outfile STR  输出文本的文件名 [stdout]输出到屏幕

       -n STR --name STR     According to this species. Default is the first one.
       -n STR --名字 STR     根据这个物种，缺省值就是第一个

       -l INT --length INT   the threshold of alignment length
 	                     (default value: 1)
       -l INT --length INT   对比序列的长度
                             (缺省值：1)
```



#### Example

```bash
实例：
fasops slice -n S288c -l 2 -o example_slice slice.fas slice.yml

解释：
slice.fas是一个长的多序列比对文件：
>S288c.I(+):13288-17133
GTGCCAGTGACAGTAGTCATCTCAGTAGAAGTCGAAGTG------------------------GTAGTTGCGATGGTGCTAGTAGTATGTTTTGAAGGATCAGGGATAGTACAATTGGGCTG--TTAGGTCATCGTCAAAAGAGTAAACGTGCCCTGCAAAATCATCGCTAACAGTAGTAACATCAGGCAATTCCACGCTAACTGGCAGTGTATGCCAAGAAACAGCGTTTGAGTAGACAATCTTCATTGGACAGTAGAATCCAGCATACATATACACAGTCCCTGCAATGTTATCGGGCAATCTTCCTTGCCATGGCTTGATACCATTAATCGTAAAGTTTGTGGATGTAATTGGAGGTTGCTCTTGTGCACAGCATCCGAATGCAACGTCGCCACCGACTGATAGAATTGCAGAGTCATCGACCTTAGCAAACCTGAACGTGTAAGAACTTGTTTGTGGTGGTAAAAAGTAACCTGTCATTTCTAGAGTAGCGTTTTTTGGGATAGTATAGAAACCAAGTAAATCAGTACTCCAATAGGT-CTTTCCTGGCTATTTTGAACATTCACTATGACCTCTGCATCCCCAGTTTCCATAAGCATCTGATTGAGCACATTTAAAAGTTCCTGAAGAGATAACACAAGAAAGATCGTAGTCAATTGAGAAATCCGTCTGTCCGCCAACGGAACCTAATTCCAA-TTCGAGGCATATCCAGAAGTCATATATTGCGGGTAGGAATACGTTGTTGAATTCAGTAATGAGTAC----AAAAGTTGACATTCATCCCATTTTTCCTCGGGTCCGTTGGCAAGCATGCTTGTGTACTTCCTGAAACAACATTGGACGTGGCCAGGTAGGTGAAGAGTGCCAAAAATAAATAGTGATGAGGC------ATTTCTTGGTTCGTCAATCAGGTATGGGTATATTAAACAAT-AACTTTAAGCCATGCTGTTAATGATGAAAATTCTGATGCTGGTCTCTTCATA-TGTCATTGTTTTGGGAACTGTCTTTTTTCGATCAGATGTATGCGCAACGATT---------------TGTTTA-GCGTAGTGATGTTCCCGCGTAGTACTTAGGAAATTTTGGTATCGAAACGTCCCTACAACACTTTTTGTCGCTGATATCTGCACATGTGAGTAACGCAGATTCCATTGCAT----------------------TATTACCTTCCCCTTCCCAGAAACGATTTATTACGACTCAACAAGTTCAAGAAAAAAATTAAAAAGGTTACAACCGCATTTTGCGTTTAGGTGCAGCGAGACTTACCTTGATGCA--GTCCTAAAGGGTCCTGCAGCGGTTGTAACGAATCCTATTTGCTTTTGTGCATTCCTAAACGGGCTGAAGTAAAAGTCTTGAGGAACTCCAGGAGAAGATACGATAAGCTCCGAAGTGCCAAATTTTATTATCAAAAAAAAATATTTGTACGAGCGGAAAAGGATTATACTCTTCCTCTTTTGCTTCATCACAAGAGGATTATTTTGACGTCGTCTGTTGTACTACTTCTTTATCGGTGTGCGCAGGAAAA---GAAAGAAAAAGGAAGGAAAAAT-AGATGTCAAAAATCGATGTCAAAAAATCTCGTGAGACCTCCGGAATTTTGACGCTGCAAGTCAATCTACGGGAAAGAAGAAAT-TTTTTAAACCTAATGCAAAATAAGC--TTTTCTTGGAAAATAAGATTTTCGGCAATAAAAGGTAAATGCAGCCAAAAATCAAAATACTTCAGAAGAAGTCGTAGCGAGGACTGC-TAAGGGGAAGCGGATTTGAAGATCCTTTCCAGAACAAGAAGGAGCCGAAAGCTGTCAGGAACTGTTCCTGATTTTTTAGGAAAACAATTAATAGGTATCTCGTCTAGAGTAGTATCTCGAGCTT-CCAGAAGTTGCAGATAAT----CAAAATCATTGTTTTA-TCCCTTTTTTTAGATTACAGCTTAGAAGAGTAGAGAGCAAGTTTACTGAAACGGTTCCTTGTTTACAATAATATTCCTAACAAACTTTACGAATTAGGATGCAGCATGATTTTTTATA-TTGCTTCACTTCCTAAAGTATGAATTTTTATCCGTAGTCGCAAACAAAACAGCTACTGGAAATCTGCAGCTTGTTAAAAACCGGTAGTTTCCGAATACTCCTCGT-CCTTGAGTTGTATACCGTTAAACTTCCTAGGGTGTCATGTGTCTGGCCC--AATTGGCCCACAAAATCTGGTCCTATTGACGG-TTTTCTTTTGATTTTCAGCATCTTCCTCTAAGAAGGACAGAAAATTATGTAATATATGGGAGAAACGGCCTCCCAACTGCTAAGTGTCCCC--GGCAGCACGAGTAAGCAAAATTCAGGCAAACTATTGCATTAAGAAGCCGTA-----------------------------CATAATTCAGCGTGATATGATGAAATTTTGTTAATTGCAAATTTTAGTACGATTTGGTTGTTAGTGTGTGTTT-ATGCAAGTAATTATTGAACCCTAAGTAGTTACTGTCTTCTTTTGCTGTAATTCGTGGATTCACGGCC-CTCCAGCAACATGGATTG--AAAGGTTCTTTAAAGTTTTCAATCCGTAAAGTTCTGAAATGTATTTTAATCATGTCAATAATTTTACTGGTGAGTAGCATTTATGACCAAAAGCGTACTTAAATTAGCAGCAAAAAAATTTT--TAATAACGAAACTATAAGG-----AAAATACGAGGTACTGATTAT-GAGAGTCCCCGTTTCTCA-TTTTTGAGACATGATCTGAACAAGGCTGAAAACAGCAATCTTTTTCGATAACTTTTGCAA-------------------------------------------------------------------------AAATTTCAAACATTGTTGTTTGAATGCAGCCAATTTTTATA-GGGTACAGAGCTTAATGCTTTACATGTGCTTTATTTTCGGTACTTTCCTTAAAGTGTCTACATTATCTCTCAGGACTTGAATGTCTTCGGCTG----AATTACTATAAAATCTTGAGTTTTCTCTGAAGTTTAATCCTAAGACAATAGTGGTGAGTGATGTAGTTCACGTGTGTGCCACTGGTAATAATAGAGATAACTATCTCAGTTAAGTTTGAAAAGGTAAAAAATAG-TTTAAGTAGTCATTT-TTTGCGACGGTCATTCTTCTCTGATGCACGTTCTTTAGACTACCTATAAACACCATTCTTACGGAATTATAATGGAAATAAAACATCAGTACG------------------TGTTGCTGTCGGTGATAGAGGGGTAACAGAACCTTAATTGAAAAATTAGCACAGTGCATAATTTATTAACAT-GATTGTTTTCTGTGGAAAATAAGAAAT-----------------TCAGCACCAGTAAAAGACGAGAAATATAGGCACATAAATGCGCTCTTACTCGTATGTTCCAGGAT-GAAAATGTTTAGGGCATCA---AGTATTGCCGAAAGGGCAATATGCTT--------------------TAACACCAGAAAATCCACTGTATACTCGTTACGGGTAAACAAAGCAAAACGC-AGTGCGTGATAATGTTTCTAAAATCTCTGCACACTGTTGAAATGCGGCTCTGATAC-TTTAGCCCTTAGTACCTGACGGTGCCTAAAATGAGGATAAGTATATGAGT-TTACAACGAATATAATGCT-TTTTACCTCATCATATAAAGAATAATATTAATTTAAAAGTAAAAATTAGACCTAGGTGGTAATGAGGAATGTGTCCTAATGAATTTATGTAGTTGATGCTTTTGCAGCGTTTTTTTTTTTTTGTATTAATC-------------------AAAGTGCACTACGTGAC--AAAAATAAAAAGTGGATGAAAACAAAAAAATCACAGATTTTCAT-AAAAATAACATGGTATTCTTAATCGTATATCG-TAATGGATTGTCAAAAATAAAGAGAATATAGTCGGTTCAAACCACTTT-----GTACGTCTACAGAGTATAGTGTAGAAATTATAGTTAGGATTGTGACATCCCTTTTTCTTTAACATAATAAGTTTGCTTCAAATTAATTGATCAATTAGGATAATAAC
>RM11_1a.scaffold_17(+):6748-10601
GAACCGGTGACCGTGGTCATTTCAGTAGATGTAGAAGTAAAAGTACCAGTCCACAGTTCGGTAGTAGTTGTGACGATGCTAGTAGTATGTTTTGAAGGATCAGGGATAGTACAATTTGACTGACTTAAATCATCGTCAAAAGAGTAAACGTACCCTTCAAAGTCATCACTAACAGTAGTACCATCAGGCAATTCCACGCTAATTGGAAGCGTGCCCCAGGAAACGGCATTGGAGTAAACAACCTTCAGCGGATAATAGTAGCCTGCATACATGTAGACAGTCCCTCCGATGTTATCAGGCAAACTTCCTTGCCATGGCTTAATACCATTGATTGTAAAATCCGTCGATGTAATTGGAGGTTGTTCTTGTGCACAACATTCGAACGCAACGTTACCACCGACTGATAGAATTGCAGAGTCATCGACCTTAGCAAACCTGAACGTGTAAGAACTTGTTTGTGGTGGTAAAAAGTAACCTGTCATTTCTAGAGTAGCGTTTTTTGGGATAGTA-AGAAACCAAGTAAATCAGTACTCCAATAGGT-CTTTCTTGGCTATTTTGAACATTCACTATGACCTCTGCATCCCCAGTTTCCATAAGCATCTGATTGAGCACATTTAAAAGTTCCTGAAGAGATAACACAAGAAAGATCGTAGTCAATTGAGAAATCCGTCTGTCCGCCAACGGAACCTAATTCCAA-TTCGAGGCATATCCAGAAGTCATATATTGCGGGTAGGAATACGTTGTTGAATCCAGTAATGAGTATTTATAAAAGTTGACATTCATCCCATTTTTCCTCGGGCCCACTGGCAGGCATGCTTGTGTACTTCCTGAAACAACATTGGACGTGGCCAGGTAGGTGAAGAGTGCCAAAAATAAATAGTGATAAGGC------ATTTCTTGGTTCGTCAATCAGGTATGGGTATATTAAACAAT-AACTTTTAGCCATGCTGTTAATGATGAAAATTTTGATGCTGGTCTCTTCATA-TGTCATTGTTTTGGGAACTGTCTTTTTTCGATCAGATGTATGCGCTACGATT---------------TGTTTA-GCGTAGTGATGTTCCTGCGCTGTATTTAGGAAATTTTGGTATCGAAACGTCCCTACAACACTTTTTGTCGCTGATATCTGCACATGCGAGTAACGCCGATTCCATTACAT----------------------TATTACCTTCCACTTCCCAGAAACGATTTATTACGACTCAACAAGTTCAAGAAAAAAATTAAAAAGGTTACAACCACATTTTGCGTTTAGGTGCAGCGAGGCTTACCTTGATGCA--GTCCTAAAGAGTCCTGCAGCAGTTGTAACGAATCCTATTTGCTTTTATGCATTCCTAAACGGGCTGAAGTATAAGTCTTGAGGAACTCCGGGAGAAGATACGATAAGCTCTGAAGTGCCAAATTTTATTATC-AAAAAAAATATTTGTACAAGCGGAAAAGGAGTATACTCTTCCTCTTTTGCTTCATCACAAGAGGATCATTTTGACGTTGTCTGTTGTACTACTTCTTTATCGGCGTGCGCAGGAAAA---GAAAGAAAAAGGAAGGAAAAAG-AGATGTCAAAAACCGATGTCAAAAAATCTCGTGAGGCCTCCGGAATTTTGACGCTGCAAGTCAATCTACGGGAAAGAAGAAAT-TTTTTAAACTTAATGCAAAATAAGCTTTTTTCTTGGAAAATAAGATTTTCGGCAATAAAAGGTAAATGCAGCCAAAAATCAAAATACTTCAGAAGAAGTCGTAGCGAGGACTGC-TACGTGGAAGCGGATTTGAAGATCCTTTCCAGAACAAGAAGGAGCCGAAAGCTGCCAGGAACTGTTCCTGATTTTTTAGGAAAACAATTAATAGGTATCTCGTCTAGCGTAGTATCTCGAGTTT-CCAGAAGTTGCAGATAAT----CAAAATCATTGTTTTA-TCGCTTTTTTTAAATTACAGCTTAGAAGAGTAGAGAGCAAGTTTACTGAAACGGTTCCTTGTTTACAATAATATTCCTAACAAACTTCACGAAGTAGGATGCAGCATGATTTTTTATC-TTGCTTCACTTCCTAAAGTATGAATTTTTATCCGTAGTCGCAACCAAAACAGCTACTGGAAATTTGCAGCTTGTTAAAAACCGGTAGTTTCCGAATACTCCTCGT-CCTTGAGTTGTATACCGTTAAACTTCCTAGGATATCATGTGTCTGGCCCAAAATTGACCCACAAAATCTGGTCCTATTGACGG-TCTTCTTTTGATTTTCAGCATCCTACTCTAAGAAGGACAGAAAATTATGAAATATATGGGAGAAACGGCCTCCCAACTGCTAAGTGTTCCC--GGCAGCACGAGTAAGCAAAATTCAGGCAACCTATTGCATTAAGAAGCCCTA-----------------------------CATAATTCAGCGTGATATGATGGAGTTTTGTTAATTGCAAATTTTAGTACGATTTGGTTGTTAGTGTGTGTTT-ATGCAAGTAATTATTGAACCCTAAGTAGTTACTGTCTTCTTTTGCTGTAATTCGTGGATTCATGGCC-CTCCAGCAACATGGATTG--AAAGGTTCTTTAAAGTTTTTAATCCGTAAAGTTCTGAAATGTATTTTAATCATGTCAATAATTCAACTGGTGGGTAGCATTTATGACCAAAAGCGTACTTAAATTAGCAGCAAAAAAATTTT--TAATAACGAAACTATAAGGAAAAAAAAATACGAGGTACTGATTAT-GAGAGTTCCAGTTTCTCA-TTTTTGAGACATGATCTGAACAAGGCTGAAAACAGCAATCTTTTTCAATAACTTTTGCAA-------------------------------------------------------------------------AAATTTCAAACATTGTTGTTTGAATGCAGCTAATTTTTATA-GAGTACAGAGCTCAATGCTTTACATGTGCTTTATTTCCGGTACTTTTCTTAAAATGTCTACATTTTCTCTCAGGACTTGAATGTCTTCGGCTG----CATTACTATAAAATCTTGAGT----------------TCCTAAGACGATAGTGGTGAGTGATGTAGTTCGCGTGTGTGCCACTGGTAATAATAGAGATAACTATCTCAGTTAAGTTTGAAAAGGTAAAGAA----------TAGTCATTT-TTTGCGACAGTCATTCTTCTCTGATGCACGTTCTTTAGACTACCTATAAACACCATTCTTACGGAATTATAATGGAAATAACACATCATTACG------------------TGTTTCTGTCGGTGATAGAGGGGTAATAGAACCTTAATTGAAAAATTAGCACAGTGCATAATTTATTAACAT-GATTGTTATCTGTGGAAAATAAGAAAT-----------------TCAGCACCAGTAAAAAACGAGGAATATAGGCACATAAATGTGCTCTTACTCGTATGTTCCAGGAT-GAAAATGTTTAGGGCATCA---AGTATTGCCGAAAGGGCAATATGCTT--------------------TAGCACCAGAAAATCCACTGTATACTCATTACGGGTAAACAAAGCAAAAGGC-AGTGCGTGATAATGTTTCTAAAATCTCTGTACACTGTTGAAATGCGGCTCTGATAC-TTTAGCCCTTAGTCCCTGATGGTGCCTAAAATGAGGATAAGTATATGAGT-TTACAACGAATATAATGCT-TTTTACCTCATCATATAAAGAATAATATCAATTTCAAAATAAAAATTAGACCCAGGTGGTAATGAGGAATGCGTCCTAATGAATTTATGTAGTCGATGCTTTTGCAGCA----TTTTTTTTTGTATTAATC-------------------AAAGTGCACTACGTGAC--AAAAATAAAAAGTGTATGAAAACAAAAAAATCACAGATTTTCAT-AAAAATAACATGGTATTCTTAATCATATATCG-TAAAGGATTGTCAAAAATAAAGAGAATATAGTCGGTACAAACCATTTT-----GTACGCCTACAGAGTATAGTGCAAAAATTATAGTTAGGATTGTGACATCCCTTTTTCTTTAACATAATAACTTTGCTTCAAATTAATTGATCAATTAGGATAATAAC
>Spar.gi_29362400(+):2498-6574
GTACCGGTGACAGTGGTCATTCCAGTAGATGTGGTGGCAAAAGTACCAGTCCATGGTTCAGTTTTAGTTGTAATTATGCTAGTAGTATGCTCTGAAGGGTCAGGGACAGTACAGATTGACTGACTTAGATAATCGTCAAAAGAGTAAACGTATGCTGCGAAGTCATCACTAACAGTAGTACCATCAGGCAATTCCACGCTAATTGGAAGTGCATCCCAGGAAACAGCATTTGAGTGGACAATCTTCATTGGGTAGTAGAATCCAGCATGCATATAGACAGTTCCTTCGATGTTATCGGGAAAACTTCCTTTTCATGGCTTGCTACCATTGATTGTAAAGTTCCTCGATGTAATTGAAGGTTGTTCTTGTGCACAATATTCGAATGCAACGT-ACCACCGACTGATAAAATTGCAGAGTCGTCAACCGTAGCAAACTTGGACGTGTAAGAACCTGTCCGTCGTGGTAACAAGTAATTTGCCATTTCTAGAATAACGTTTGTTGGGGTAGCATAGAAACCAAATAAATCTGTACCCCAGTAGGCACTTGCCTGGGTA-TTGGAGTATTCTCCATGACCTCTGCACCCCCAGTTTCCATGAGCATTTGATTGGGCACATTTGAAAGTTCCTGAAGGGGTAACAC----AAGATCGTAGTTAATCGAGATATCCGTCTGTCCGCTGACAGAACCTAGTTTCAATTTTAAGGCGTATCCAGAAGCCATATATTGCGAGAAGGAATACGTGGTTGAATCCAATAATTTGTACTTACAAGAGTTGACATTCATCCCATTATTTCTCTAGTCCGCTGGCAAACATGCTTGTGTACTTCCTGAAACTACATTGAATGTGGCTAGGAAGGTGCAGAGTGCC-CAAATATATAGTTATGAGACATCGAAATTTTTGGGTTTATCATTCAATTATGGGCATAGTAAATAATGAATTTTTAGCCATACTTTTAATGATGGGAATT---ATGTTGGACTTTTTATATTTTCAGTATTTTCGGAACTGTCTTGTTTTGAGCAGGCACATGCGCAACGGTTTGGTATTTAGCGTGATGTTTATGCGTAGTAATGTTTCTGCTTAGTACGTAGGAAA-TTTGATATTAAAACGTTCCTACGACACTTTTTATCACTGCTATTTGCACATGCTAGCAACGCCAATTCTATTACATTATTTTTGAGTTTTATGGGTAATATTACCTTCCACTTCCCAAAAACAATTTACCACGACTCAATAAGTTCAAGAAAAAAAATAAAAAGGTTGCAACTGCGCTTTTTGTTCAGGTACAGCGAGGCTTGCCTCGCTGCACTCTCCTAAAGGGTCCTACGGCAGTCGTAACAAACCCTATTCCCCTCTGTGCATTCCTAAACGGACTGAAGTATAAATCTTGAGGAAATCAGGGAAAGGATATGATAAGCTTTGAAGCGCCAAATTTTATTATC----AAAAATATTTGTACGAGCTGAAAAGGAGTTTACTTTTCCTCTTTTGTTTCACCACAACCGG-TCATTTGGGTGACG--TGTGGTATTACTTCTCTGTCGGCGTGCGCCGGAAAAAAGAAAGGAAAAAGGAAGGAAAAAACCGATGTCAAAAACCGACGTCAAAAAATCTCGTGAGACCTCCGGAAGTTCGACGCTGCAAGTCAATCTACGGAAAAGAAGAAATATTTCAAAACTTAACGCAAAATAAGC--TTTTTTCGGAAAATAAGCTTTTCGGCAATAAAAGGTAAACGCGGC-------CAAAGTATGTTAGAAGAAGTCGTAGCAAGGGCTGCTTCAGTGAAAGCGGTTTTGAAGATCCTTTCCGGAACAAGAAAGAGCCGACAGGTGGCGGGAACTTTGCCTTGATTTT---------------TAGGTACCTCATCTAGTGCAGTATACCAAGCTTACCGGAAGTTACAGATAATGAGAAAAAATCATTATTTTATTCCCCTTTTTAAGATTACAGCTTAGATGAGTAGAGAGCAAATCTACTGAAAGGATACCTTGTTTACAATAATATACCTAAAAAACTTCACGAATTAAGATGCAACACAATTTTTCATAGGTGCTGCTCTTCC-AAAATGTGTATTTTTATCCTTAGCCGCAAACAAACCAGCTACTGAGAATGGGCAGTTTACTAAAAAATGGTTCTTTCCGAATACCCCTCGCACCCTGAATTGATTGCTATTAAAATCGCTAGAGTGTGGAGTGACCG------AATTAACCCACAGAAAGTGATCCTATGGACGGTTTTTCTTCCGATGTTCAGCATCCTGT----ATAAGAGCAGAAAATTATGAAATGTATGGGAGGCCCGGCCTTACAACCACAAGGTGTCCCCCTGGCTGTACCTGTCAGCAAAATTCAGGCAATTTATTTCATTAAGATACCCTATCCTAATTAAATTGGCAATAATCTTCGTTCAGCATTCAGCATGATGTAATGGAGT----TTAGTTGCAAATTGCAGTACGA--TGGCTGTCAACTTGTGTTCGATGCGAGTGATAGTTGGACCCTAAATAGTTACTGTCTCCTTTCGTTGTAAATCGTCGATTCATTGCCTTTTCAGCAACATGAATTGGCAAAGGTTCTCTTAAGTGTCCAATCCGTAAATTTCTGAGATGTA--CTAACCATGTAAATGATGCTACCGGTGAGTAGCATTTATATCCTATAGTGTATTTAAATTAGCAGCCAATTTTTTTTTCTGGAAACCAAGCTATAAGG-----AGCATACGAGGCATTGATTATGGGGGGGGTTTGTTCCTCAGGTTTTAAGACCTGATCTGAACTAGCTTGAAAATAGCAATCATTTTCAGTAACTAGTGCAAACAGATTACACAAAGTTAAGATTTCACTGGCTGAAGGGTTGAGATCTTGGTAGACGATTGATAAATTTGAGCCAACTCTCAAGCATTATTGTTTGAATGTAGCCAATTTCTGTATAAATACAGAACTCAATGCTTTTCATTTGCTTTATTTCCAGTACTTTTCTTAAAGCGCCTACATTTTGTTCCAGAACTTGAACGTCTTTGGTTGGATTAATTATTAT-GAATCCTGAGTTTTTTCTGAACTTTAATTTCAAGATAATCGCGATGAGTGGTATACTTCGCTTGTGTACCGCTAATAATAATAGAAATAACTGTCTCAGTTAAATTTGAAAAGGTAAAAAATAGTTTTTAGTAGCTATTGATTTGCGGTGGCCAAGCGTCCCTGGTGGATGTTCTTTAGACTACCTGCAAACACCAT----------TGGTAATGGAAATAAAAAATTGGTATGACTTATTGATGAGTAAAATGTTGCTGTCAGTAATAAAGTGGTAGCAGAACCTTAATCGAAAAAATCGCACAGTGCATATTTTATTGACATAAATCGTTTTCTGGGGCAAAGTAAAAATTTTGTCATCAATGTAAGTCAGAGCCAGCAAAATATGAGGCAGGCGGACACGTAAATGCGTCCTTATTTGTAGGTTTCAGTATAAAAAAGGTTCAGGGGATCCAGTAGTATTGCAGAAAGGGCGGCATGCATCGTTAAGGACATTATTACACCAGCGCCGGAAAGTCCACTGTATACCAATTAGGGGCAAACCAAGCAAAACGCAAGCGCGTGATTATGTATCTAAAACCTCTGTACACTGCCAAAATGCGGCTCTGATACTTTTAGCTTTCAATTCCTGGCGGTGCCTAAGAAGGGGAGAAGTACATGAGCATGAAGACAAATATAACGATACTTTAATACATCATATGAAGAATGATATTAATTTCAAAATAAGCATTAGACCCAGGAAGCAACAAGGGATGCGTCCAATTGAATACATGTAGTCAATGTTGCTGCAACA----TTTTTTTTGTTAATTATCAAATTTTCTCATTCTATCGAAAGTTTACGGCACGACaaaaaaacaaaacttaaaagaaaacaaaaaaaTGTCAGATATTCATAAAAAATAACAGAGTATTCCTAATTATATATCGTTAAGGAATTCCAAAAAATATAGAAAATATA--CTGTTCGAACCATTTTTGTGCATTTACCTACAGAACCTAGTGCAG-AATTATAGTTGAAATTATGGCATCCTTTTTTTTTAGGTATAATACGCATGGTTCAAATTAATCGATCACTTAGGATAATAAC

如果我们想要提取slice.fas中的一部分，即可以用到实例中的操作：

首先需要设置提取的范围即slice.yml：

---
I: 13301-13400

并且我们在命令中还设置了-n S288c -l 2，即以S288c为.yml文件设置范围的物种，且其他物种至少与S288c有长度为2的比对区域。

因此最终得到example_slice：

>S288c.I(+):13301-13400
TAGTCATCTCAGTAGAAGTCGAAGTG------------------------GTAGTTGCGATGGTGCTAGTAGTATGTTTTGAAGGATCAGGGATAGTACAATTGGGCTG--TTAGGTCATCGTCAA
>RM11_1a.scaffold_17(+):6761-6886
TGGTCATTTCAGTAGATGTAGAAGTAAAAGTACCAGTCCACAGTTCGGTAGTAGTTGTGACGATGCTAGTAGTATGTTTTGAAGGATCAGGGATAGTACAATTTGACTGACTTAAATCATCGTCAA
>Spar.gi_29362400(+):2511-2636
TGGTCATTCCAGTAGATGTGGTGGCAAAAGTACCAGTCCATGGTTCAGTTTTAGTTGTAATTATGCTAGTAGTATGCTCTGAAGGGTCAGGGACAGTACAGATTGACTGACTTAGATAATCGTCAA
```



### 14. split
分离

```bash
输入：
fasops split [options] <infile> [more infiles]

Split blocked fasta files to separate per-alignment files.
将blocked fasta文件分割成比对前文件

       <infiles> are paths to blocked fasta files,.fas.gz is supported.
       <输入文件>是blocked fasta文件的路径，.fas.gz也是可以支持的。
	infile == stdin means reading from STDIN

       -o STR --outdir STR  Output location, [stdout] for screen
       -o STR --outdir STR  输出位置， [stdout]输出到屏幕

       -r --rm              If outdir exists, remove it before operating.
       -r --rm              如果outdir存在，在操作前去除

       --chr                Split by chromosomes.
       --chr                根据染色体分离
```



#### Example

```bash
实例：
fasops split -o example_split example.fas
fasops split -o example_split_2 --chr  example.fas

解释：
- example.fas是一个经过比对后形成的fasta文件：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

- 通过实例中第一个操作，我们可以得到一个新的目录example_split，其中包含了三个新文件：
S288c.I.+.13267-13287.fas    S288c.I.+.185273-185334.fas    S288c.I.+.184896-185050.fas

我们打开其中一个文件S288c.I.+.13267-13287.fas：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

发现该命令是将原fasta文件中每一个block进行分离，形成新的文件并以每一个block的第一个序列名称进行命名。

- 通过实例中第二个操作，我们可以得到一个新的目录example_split_2，其中包含了一个新文件：
I.fas

打开I.fas:
>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

由于在第二个操作中设置了--chr，因此是在分离的过程中会按照染色体进行分割。example.fas中S288s都是chr I，因此最终只分出了一个新的文件I.fas。
```



### 15. stat
统计

```bash
输入：
fasops stat [options] <infile>
fasops stat [选项] <输入文件>

Basic statistics on alignments.
比对文件的基本数据统计

       <infile> are blocked fasta files, .fas.gz is supported.
       <输入文件>是blocked fasta文件的路径，.fas.gz也是可以支持的。
	infile == stdin means reading from STDIN


       -o STR --outfile STR  output filename. [stdout] for screen
       -o STR --outfile STR  输出文本的文件名 [stdout]输出到屏幕

       -l INT --length INT   the threshold of alignment length
	                     (default value: 1)
       -l INT --length INT   对比序列的长度
                             (缺省值：1)

       --outgroup            alignments have an outgroup
       --outgroup            比对文件含有外类群
```



#### Example

```bash
实例：
fasops stat -o example_stat example.fas
fasops stat -o example_stat_2 -l 50 example.fas
fasops stat -o example_stat_3 --outgroup example.fas

解释：
- example.fas是一个经过比对后形成的fasta文件：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

- 通过实例中的第一个操作我们可以得到example_stat：

first,legnth,comparables,identities,differences,gaps,ns,errors,D,indel
S288c.I(+):13267-13287,21,21,16,5,0,0,0,0.119047619047619,0
S288c.I(+):184896-185050,155,155,100,55,0,0,0,0.20752688172043,0
S288c.I(+):185273-185334,63,56,36,20,7,0,0,0.192523658873961,6

**first**是每一个block中第一个序列的名称,
**legnth**是这个序列的长度,
**comparables**是这个序列可以比对的长度,
**identities**是可以完全配对的个数,
**differences**是有差异的个数,
**gaps**是间隔的长度,
**ns**是N的数量, 
**errors**是排序错误的个数,
**D**是分歧度,
**indel**是indel的个数

- 通过实例中的第二个操作我们可以得到example_stat_2：

first,legnth,comparables,identities,differences,gaps,ns,errors,D,indel
S288c.I(+):184896-185050,155,155,100,55,0,0,0,0.20752688172043,0
S288c.I(+):185273-185334,63,56,36,20,7,0,0,0.192523658873961,6

由于我们在命令中设置了-l 50，因此少于50个配对的block被去除了。

- 通过实例中的第三个操作我们可以得到example_stat_3：

first,legnth,comparables,identities,differences,gaps,ns,errors,D,indel
S288c.I(+):13267-13287,21,21,21,0,0,0,0,0,0
S288c.I(+):184896-185050,155,155,118,37,0,0,0,0.161290322580645,0
S288c.I(+):185273-185334,63,60,49,11,3,0,0,0.120218579234973,3

由于我们在命令中设置了--outgroup，默认每个block中最后一个序列为外类群，因此最终的得到的统计数据与上述操作具有差别。
```



### 16. subset
子文件

```bash
输入：
fasops subset [options] <infile> <name.list>
fasops subset [选项]<输入文件> <名单>

Extract a subset of species from a blocked fasta.
提取一个blocked fasta文件的一个物种的子文件

       <infile> is the path to blocked fasta file, .fas.gz is supported.
       <输入文件>是blocked fasta文件的路径，.fas.gz也是可以支持的。
	infile == stdin means reading from STDIN

       <name.list> is a file with a list of names to keep, one per line.

       <名单>是一个名单的文件，一行一个
	Names in the output file will following the order in <name.list>.

       -o STR --outfile STR  Output filename. [stdout] for screen
       -o STR --outfile STR  输出文本的文件名 [stdout]输出到屏幕

       --first               Always keep the first species
       --first               总是保持第一个物种

       --required            Skip blocks not containing all the names

       --required            跳过没有包含所有名字的blocks
```



#### Example

```bash
实例：
fasops subset -o example_subset example.fas example.name.list
fasops subset -o example_subset_2 example.fas --first example.name.list

解释：
- example.fas是一个经过比对后形成的fasta文件：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

- 而example.name.list是一个名单：

Spar
YJM789

- 通过实例中的第一个操作我们可以得到example_subset：

>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA

>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT

>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA

即我们按照namelist中要提取的物种，将原文件中每一个block做了提取。

- 通过实例中的第二个操作我们可以得到example_subset_2：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA

由于我们在命令中设置了--first，我们发现不仅namelist中要提取的物种被保留下来，原文件中每一个block的第一个物种也会被保留下来。
```



### 17. xlsx
xlsx

```bash
输入：
fasops xlsx [options] <infile>

fasops xlsx [选项] <输入文件>

Paint substitutions and indels to an excel file.
在一个excel文件中绘制替换和缺失

       <infile> are blocked fasta files, .fas.gz is supported.
       <输入文件>是blocked fasta文件的路径，.fas.gz也是可以支持的。
	infile == stdin means reading from STDIN

       -o STR --outfile STR  Output filename. [stdout] for screen
       -o STR --outfile STR  输出文本的文件名 [stdout]输出到屏幕

       -l INT --length INT   the threshold of alignment length
	                     (default value: 1)
       -l INT --length INT   对比序列的长度
                             (缺省值：1)

       --wrap INT            wrap length
	                     (default value: 50)
       --wrap INT            换行长度
                             (缺省值：50)

       --spacing INT         wrapped line spacing
	                      (default value: 1)
       --spacing INT         换行空格
                             (缺省值：1)

       --colors INT          number of colors
	                     (default value: 15)
       --colors INT          颜色的数量
                             (缺省值：15)

       --outgroup            alignments have an outgroup
       --outgroup            比对文件含有外类群
```



#### Example

```bash
实例：
fasops xlsx -o example_xlsx.xlsx example.fas
fasops xlsx -o example_xlsx_2.xlsx -l 50 --outgroup example.fas

解释：
- example.fas是一个经过比对后形成的fasta文件：

>S288c.I(+):13267-13287|species=S288c
TCGTCAGTTGGTTGACCATTA
>YJM789.gi_151941327(-):5668-5688|species=YJM789
TCGTCAGTTGGTTGACCATTA
>RM11.gi_61385832(-):5590-5610|species=RM11
TCGTCAGTTGGTTGACCATTA
>Spar.gi_29362400(+):2477-2497|species=Spar
TCATCAGTTGGCAAACCGTTA

>S288c.I(+):184896-185050|species=S288c
AAACACCTTCAGAAAATACCGACGTAAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>YJM789.gi_151941327(+):156291-156445|species=YJM789
AAGCCTCTTCAGAAAATGCCAATACCAAATTAGATACGTTAAGTGAGCCTAGTGCACATTTAATCGAGGAAAATGTGGCTCTTCCAGAGGACACATTCCGTTCGTATCTGAGTTATTTGCTCTACGAGATGGCTCATTATAAACCGATAATGGTT
>RM11.gi_61385833(-):3291-3445|species=RM11
AAACACCTTCAGAAAATACCGACGTTAAGATGGATACTCTCGACGAACCCAGTGCACATTTAATCGAAGAGAATGTAGCTCTTCCCGAAGACACATTCAGTTCACATCTGAGTTATGTACTTTATGAAATTGCTCATTGTAAACCGATCATGTTT
>Spar.gi_29362478(-):1719-1873|species=Spar
AAACAACTTCAAAAAATAACGATTAAAAATCGGATACCCTCAGTGAACCCAATGCACAATTAATCGAGGAAAATGTAGTTCTTTCTAAGGACATATTCAATTCATATCTGAGCTATCTGTTCTACGAAACGGCTCACCGTACACCAACCATGTTT

>S288c.I(+):185273-185334|species=S288c
GCATATAATATGAACCAATATCTA-TTCATGAAGAGACTATGGTATACCCGGTACTATTTCTA
>YJM789.gi_151941327(+):156665-156726|species=YJM789
GCGTATAATATGAACCAGTATCTTTTTCATGAAG-GGCTATGGTATACTCCATATTACTTCTA
>RM11.gi_61385833(-):3668-3730|species=RM11
GCATATAATATGAACCAATATCTATTTCATGGAGAGACTATGATAT-CCCCGTACTATTTCTA
>Spar.gi_29362478(-):2102-2161|species=Spar
GC-TAAAATATGAA-CGATATTTA-CCTGTAGAGGGACTATGGGAT-CCCCATACTACTTT--

- 通过实例中的第一个操作，我们可以得到example_xlsx.xlsx，把fasta文件以excel表格形式展示。

- 通过实例中的第二个操作，我们可以得到example_xlsx_2.xlsx，把fasta文件以excel表格形式展示，由于设置了-l 50 --outgroup，因此在example_xlsx_2.xlsx去除了配对长度小于50的序列，默认将最后一个序列作为外类群，因此最终得到的xlsx表格与上述操作具有差异。


ps:
git clone https://anongit.freedesktop.org/git/xdg/xdg-utils.git
使用xdg-open ~.xlsx可以打开文件
```
