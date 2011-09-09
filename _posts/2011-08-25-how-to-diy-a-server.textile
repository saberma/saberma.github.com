---
layout: post
title: 服务器DIY
category: other
---

h2. CPU

目前AMD服务器CPU主要有Opteron(皓龙)4000和6000系列，AMD的CPU都带内存控制器，直接与内存交互，而不再通过北桥芯片
价格差异点在于内核数量、主频及功耗，功耗节能版对服务器领域来说意义不大，而且价格高昂，不考虑(型号后缀为HE低功耗或EE超低功耗)

h3. "4000系列":http://www.amd.com/cn/products/server/processors/4000-series-platform/Pages/4000-series-platform.aspx

核心数量有4核、6核，最高支持DDR3-1333，核心独占一二级缓存，共享三级缓存
候选表格如下：

|型号|内核|主频|插槽类型  |功耗|价格|
|4184|6   |2.8 |Socket C32|75W |3500|
|4180|6   |2.6 |Socket C32|75W |2500|
|4130|4   |2.6 |Socket C32|75W |1650|
|4122|4   |2.2 |Socket C32|75W |1050|


h3. "6000系列":http://www.amd.com/cn/products/server/processors/6000-series-platform/Pages/6000-series-platform.aspx

核心数量有8核、12核，候选表格如下：

|型号|内核|主频|插槽类型  |功耗|价格|
|6174|12  |2.2 |Socket G34|80W |8500|
|6172|12  |2.1 |Socket G34|80W |7500|
|6168|12  |1.9 |Socket G34|80W |6000|
|6136|8   |2.4 |Socket G34|80W |5600|
|6134|8   |2.3 |Socket G34|80W |3890|
|6128|8   |2.0 |Socket G34|80W |3111|

初步选用 6134，性价比高

h3. 芯片组

使用AMD自家的服务器芯片组:北桥SR5690/SR5670/SR5650、南桥SP5100，详见 "AMD平台规范":http://www.amd.com/cn/products/server/processors/Pages/amd-platform-specifications.aspx

* 用于高能效云的SR5650芯片组
* 用于可扩展SMB 的SR5670芯片组
* 用于高I/O系统的SR5690

h2. 主板

主要生产商:超微(Supermicro)、泰安(TYAN)、Intel、华硕
具体要求:支持SAS硬盘、DDR3(1333MHZ最大内存至少32
有些主板带iKVM功能(Keyboard,Video,Mouse)，用来实现远程管理功能(KVM Over IP)

"超微C32系列主板":http://www.supermicro.com/c32/AMD_C32.cfm?pg=MOBO
"超微G34系列主板":http://www.supermicro.com/g34/AMD_G34.cfm?pg=MOBO

|型号   |最大内存容量                               |插槽|数量|SAS|结构 |价格|
|H8DCL-6|128GB Registered ECC or 32GB Unbuffered ECC|C32 |2   |SAS|ATX  |$408|
|H8DG6  |256GB Registered ECC or 64GB Unbuffered ECC|G34 |2   |SAS|E-ATX|$573|

"代理商":http://www.supermicro.com/wheretobuy/asia.cfm?rgn=133
海红微电子,深圳市福田区天安数码城创新科技广场A座1803室,0755-83434282
星宏宝博,深圳市福田区福华一路98号卓越606B-608B,0755-83005009,13823305008,覃碧玉

h3. "泰安主板":http://www.tyan.com.cn/product_boards2.aspx

"泰安C32系列主板":http://www.tyan.com.cn/product_boards2_list_socket.aspx?cpuid=4&socketid=24&chipsetid=all&socketnum=all&formfactor=all
"泰安G34系列主板":http://www.tyan.com.cn/product_boards2_list_socket.aspx?cpuid=4&socketid=25&chipsetid=all&socketnum=all&formfactor=all

在不确定选择哪一个主板的情况下，可以先看主板的编号；要求主板支持SAS，则编号一定要带W字母，比如:WAGM4NRF型号是支持SAS的，而AGM4NRF不支持，具体编号意义可查看 "产品矩阵":http://www.tyan.com.cn/tech/product_matrix.aspx 的底端说明
所以型号一定要包含WGNR(G=Onboard Gf / N=LAN / R= RAID / W= SAS)

候选表格，泰安主板AMD系列在国内找不到网上报价，先从 "newegg.com":newegg.com "比较价格":http://www.newegg.ca/Product/Productcompare.aspx?Submit=ENE&N=100007628%2050001424%20600008002&IsNodeId=1&bop=And&CompareItemList=302|13-151-227^13-151-227-TS%2C13-151-225^13-151-225-TS%2C13-151-215^13-151-215-TS%2C13-151-214^13-151-214-TS%2C13-151-220^13-151-220-TS

|型号          |最大内存容量         |插槽|数量|SAS   |主板结构    |价格|
|S8010WGM2NR   |64GB RDIMM/16GB UDIMM|C32 |1   |SAS   |ATX         |$365|
|S8225WAGM4NRF |64GB RDIMM/32GB UDIMM|C32 |2   |SAS   |Extended ATX|$615|
|S8226WGM3NR   |96GB                 |C32 |2   |SAS   |SSI EEB     |$560|
|S8230WGM4NR-LE|128GB                |G34 |2   |SAS2.0|Extended ATX|$550|
|S8230WGM4NR   |128GB                |G34 |2   |SAS2.0|Extended ATX|$580|
|S8236WGM3NR   |128GB                |G34 |2   |SAS2.0|SSI EEB     |$570|

"华南区代理商":http://www.tyan.com.cn/wtb_asia.aspx#CHINA
深圳市亿时空科技有限公司,深圳市福田区华强北路宝华大厦A629室,0755-88863168,孙先生

h2. 内存

以下应用技术也是影响内存价格的重要因素

|Unbuffered Ecc DIMM|低端服务器工作站内存|
|Registered DIMM    |高端服务器工作站内存|
|Fully Buffered DIMM|全缓冲服务器内存    |

在产品报价上看到产品名称带FB的，一般都为全缓冲服务器内存，暂不需要考虑此类产品

要带ECC纠错技术，通过IC颗粒的分布可以看出是否带有ECC功能，ECC比普通NO ECC内存多1颗IC芯片
买DDR3的，电压低至1.5v，数据传输率(800/1066/1333/1600)

为了让双通道技术发挥作用，假如需要的内存总量为8G的，则买两条4G的，分别插入 *同颜色* 的DIMM

候选表格如下:

|型号                     |价格|
|Ramax REG/DDR3/1333/4G   |959 |
|三星8GB DDR3 1333 REG ECC|799 |
|三星4GB DDR3 1333 ECC    |350 |


h2. 硬盘

最大的选择点在于是选择传统的机械硬盘还是SSD固态硬盘，后者在IOPS上有极大地物理优势，但价格较高

*机械硬盘HDD*

这里只考虑希捷的硬盘，选用SAS2.0（串行连接SCSI）6Gb/s接口；2.5英寸的Savvio 15K系列没有货源；只能选择3.5英寸
根据 "希捷官网面向企业级Cheetah® 15K":http://www.seagate.com/www/zh-cn/products/enterprise-hard-drives/cheetah-15k 的描述，支持SAS2.0，15K的候选表格如下:

|型号                                     |编号       |缓存|盘片|报价|
|希捷 Cheetah(捷豹) 15K.7 SAS 6Gb/秒 300GB|ST3300657SS|16MB|2   |1520|
|希捷 Cheetah(捷豹) 15K.7 SAS 6Gb/秒 450GB|ST3450857SS|16MB|3   |1840|
|希捷 Cheetah(捷豹) 15K.7 SAS 6Gb/秒 600GB|ST3600057SS|16MB|4   |2410|

*固态硬盘SSD*

如果服务器应用并不需要海量存储，可以选择SSD固态硬盘。SSD产品非常多，主要选择适用企业应用的产品

固态硬盘的价格差异点在于主控、存储方式、容量

主控：连接闪存和外部SATA接口，决定数据要存储在哪个闪存block，常见品牌有:
# intel 芯片(PC29AS21BA0)采用34nm颗粒工艺的Gen2版本，X25-E Extreme SATA系列专用于服务器，也为威刚、金士顿等品牌代工
# SandForce 芯片(SF-1565TA2-SBH)的SF1500面向服务器，代表性产品:海盗船Force系列
# Jmicron *别买来做服务器*

存储方式分为:
# SLC(单层式储存) 一个block只记录0和1数值，速度快(特别是随机写)，稳定，寿命长(10万次擦写)，成本高
# MLC(多层式储存) 一个block记录00,01,11,10四组数值，存储空间翻倍，寿命短(1万次擦写)，成本低

候选表格如下(尺寸都为2.5英寸,NAND闪存类型都为SLC):

|型号                 |编号           |读取速度|写入速度|接口类型   |价格|
|Intel X25-E 50nm(32G)|SSDSA2SH032G1GN|250MB/S |170MB/S |SATA II    |2385|
|Intel X25-E 50nm(64G)|SSDSA2SH064G101|250MB/S |170MB/S |SATA II    |4550|
|希捷Pulsar XT.2(100G)|ST100FX0002    |360MB/S |300MB/S |SAS(6-Gb/s)|    |

4K随机读写性能，顺序读写速度对于大数据量来说非常重要，但作为系统盘(即非单纯的数据存储)，最关键的还是4k随机读写这一参数

供应商:

|名称         |地址                   |联系人|电话         |手机       |网店                               |
|风驰计算机   |华强北国利大厦1711-1716|杨颖  |0755 83743198|13823752475|"网址":http://m.pconline.com.cn/shop495935|
|服务器专卖   |现代之窗b座8楼8i       |谢先生|             |13715330262|"网址":http://shop34747127.taobao.com     |
|LBC服务器专卖|赛格电子市场5楼        |黄先生|             |13510979215|"网址":http://diyservers.taobao.com       |

h2. 机箱

h3. 大小

宽(48.26cm＝19英寸)，高(1u=4.445cm的倍数)
长度主要分为两组
# 标准组 长度约为625－725毫米，支持双CPU服务器主板(含12×13英寸超大尺寸)
# 短款组 长度约为360－525毫米，仅支持单CPU服务器主板

h3. 主板结构

# ATX 12"x9.5" (30.5cmx24.2cm)
# MEB 16.2"x13" (411x330mm)
# SSI EEB 12"x13" (305x330mm)
# EATX 12"x13" (305x330mm) 大小于EEB相同，但是两者不可替换使用(因为螺丝口都不一样)

h3. 散热

重中之重，安装6个以上的风扇
主要散热点为CPU、主板芯片组、内存、硬盘

h3. "超微机箱":http://www.fywx.cc/Product_List.php?root_id=5&cat_id=28

|型号                        |CPU  |适用主板           |硬盘位      |IO slots    |电源功率|报价|
|SC815TQ-560UV/SC815TQ-560UB |DP/UP|13.68"x13"E-ATX    |4x3.5英寸SAS|2x FF, 1x LP|560W    |3200|
|SC815TQ-560V/SC815TQ-560B   |DP   |12"x13"E-ATX       |4x3.5英寸SAS|1x FF, 1x LP|560W    |3200|
|SC815TQ-560CV/SC815TQ-560CB |DP   |(12",13.68")x10"ATX|4x3.5英寸SAS|1x FF       |560W    |    |
|SC813TQ-520/SC813TQ-520B    |DP/UP|12"x13"E-ATX       |4x3.5英寸SAS|1x FF, 1x LP|520W    |3178|
|SC813TQ-520C/SC813TQ-520CB  |DP/UP|12"x13"E-ATX       |4x3.5英寸SAS|1x FF       |520W    |    |
|SC813TQ-500/SC813TQ-500B    |DP   |12"x13"E-ATX       |4x3.5英寸SAS|1x FH, 1x LP|500W    |3743|
|SC813MFTQ-520CB             |DP/UP|12"x10"ATX         |4x3.5英寸SAS|1x FH       |520W    |2635|
|SC813MTQ-520C/SC813MTQ-520CB|DP/UP|12"x10"ATX         |4x3.5英寸SAS|1x FH       |520W    |2688|
|SC813MT-420C/SC813MS-420CB  |DP/UP|12"x10"ATX         |4x3.5英寸SAS|1x FH       |420W    |    |
|SC813MT-410C/SC813MT-410CB  |DP/UP|12"x10"ATX         |4x3.5英寸SAS|1x FH       |410W    |    |
|SC813MT-300C/SC813MT-300CB  |DP/UP|12"x10"ATX         |4x3.5英寸SAS|1x FH       |300W    |    |
|SC811TQ-520/SC811TQ-520B    |DP/UP|12"x10"ATX         |2x3.5英寸SAS|1x FH       |520W    |2368|
|SC811TQ-350B                |DP/UP|12"x10"ATX         |2x3.5英寸SAS|1x FH       |350W    |    |

型号带Twin，是专为双子星产品而设计的，1u空间可以装入2块主板；
型号带Q，支持SAS/SATA；S，支持SCSI；R，冗余双电源，U指支持单路主板；V或者B指颜色
IO slots:FF=全高，全长的扩展卡;FH=全高，半长的扩展卡;LP=矮板扩展卡

h2. 其他

操作过程一定要带防静电手套
在查询时应该多使用"实测"、"评测"、"企业级"、"如何选购"，以最大可能地了解各种配件在实际中的使用情况

h2. 后记

由于AMD的CPU在服务器市场的占用率非常少,各种型号都经常缺货,就算有货价格也特别高,对应的主板款式也非常少,因此决定放弃采用AMD架构

h3. intel

intel的E5645,集成6核,主频2.4GHZ,具备超线程技术,单核可同时处理两个任务,性价比最高

h3. 主板

E5645属于intel至强 "5000":http://www.intel.com/zh_CN/products/server/processor/xeon5000/index.htm 系列,支持此系列的 "芯片组":http://www.intel.com/cd/products/services/apac/zho/server/chipsets/360574.htm#s1=%u670D%u52A1%u5668&s2=all&s3=all 主要有 intel5500,intel5520,主要差别在于 "最大内存容量":http://ark.intel.com/compare/36784,36783
主要选择基于5500芯片组的双路主板

*超微*

|型号    |最大内存容量|插槽|数量|SAS   |结构 |价格|
|X8DTH-6F|192GB       |1366|2   |SAS2.0|E-ATX|    |
|X8DT6-F |192GB       |1366|2   |SAS2.0|E-ATX|3300|
|X8DTL-6F|96GB        |1366|2   |SAS2.0|E-ATX|    |

6-SAS2.0(6G),F-带IPMI功能

最好带IPMI芯片,用于远程监控cpu,风扇等状态
参考 "使用IPMI远程控制管理":http://blog.5ilinux.com/archives/2006/07/ipmidell2850.html "使用 ipmitool 实现 Linux 系统下对服务器的 ipmi 管理":http://www.ibm.com/developerworks/cn/linux/l-ipmi/index.html

h2. 参考资源

"AMD服务器新平台第二波：Opteron 4100登场":http://news.mydrivers.com/1/167/167758.htm
"AMD服务器平台":http://www.amd.com/cn/products/server/processors/Pages/server-processors.aspx
"AMD CPU 报价":http://detail.zol.com.cn/servercpu_index/subcate383_55_list_s2775_1.html

"UDIMM与RDIMM区别":http://www.liusuping.com/it-tech/server-rdimm-udimm.html

"如何选购固态硬盘":http://hi.baidu.com/tyc6982/blog/item/b75d55ce519deb37b700c870.html
"测试探究SSD固态硬盘性能下降的谜题":http://storage.it168.com/a2011/0104/1145/000001145668_all.shtml

"DIY服务器机箱组装经验谈":http://www.ittong.net/News_37191.html
