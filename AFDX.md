title: AFDX
date: 2016-04-29 09:45:21
categories: [安全]
tags:
---
航空电子全双工交换式以太网（AFDX）技术基于IEEE 802.2 以太网和TCP/IP 通用原理，利用商用货架网络化技术来实现飞机设备间的
数据高速通信，AFDX 与普通商用以太网最大的不同点在于AFDX采用了虚拟链路技术和冗余管理，提高了数据传输的可靠性和服务质量。

冗余管理机制  -- 》 缓冲区溢出攻击 。系统在接收到冗余帧后，是将接受到的帧与其冗余帧进行比较，来确定是否接受该帧。随着
缓存队列的增加，查找的速度也会变慢。
AFDX 端口，提供每一个航空电子综合计算机与AFDX网络交换机的连接方式，通过AFDX端口，实现各航空电子子系统间数据的安全、可靠
传输
AFDX 网络交换机，实现内部全双工网络数据的交换，它的主要功能是构建一个机载内部网络，并把相关数据信息及时的传送到不同的
航空电子子系统或设备中 。
拓扑结构（环形、星形、全连接和星环形） 星环形最为可靠，可用于关键领域
A380 航电系统采用了由星环形和环形组合的拓扑结构：对于关系飞行安全的关键任务功能区域（驾驶舱、飞控、发动机），选择了星环形
拓扑（交换机 1-4 和交换机 9）；而对于其他非关键任务功能区域（客舱、燃油、保障的动力等）选择了环形拓扑。

目前在计算工业全双工以太网延迟上界中普遍采用的方式是网络演算理论。该理论计算出的结果是一种极端悲观的结果，得出的是最差
情况下系统的延迟 。

AFDX 包括 虚拟链路(Virtual Link ,VL)、终端系统(End System ,ES)、交换机 (Switch)
ES 的主要功能是流量整形，限制突发流量的出现。按照定义好的配置表将数据流整形成各个虚拟链路中定义的参数，然后通过虚拟
链路调度器调度之后发送到交换机中。虚拟链路主要功能是传输数据。交换机作为数据交换的中心枢纽，可以快速的把以太网数据转发
给适当的目的地。

网络演算是基于最小加代数的一系列结论，利用它可以分析缓冲区调度以及网络时延的一些根本属性，已经被成功应用到工业以太网的
时间延迟分析中。网络演算的主要概念包括进入曲线、服务曲线以及最小加代数的卷积和反卷积运算。
利用网络演算理论计算AFDX的网络延迟

AFDX 最大带宽可达100Mbps,采用虚拟链路代替电缆连接数目众多的ARINC429单向传输总线，降低了既有设备改造的风险，保护
投资，成为下一代航空数据网络标准的最佳选择。研究并实现AFDX网络的应用，对于我国研制具有自主知识产权的大型飞机
具有积极作用。
ARINC664 规范定义了一个用于飞行器环境的以太网数据网络，支持飞行器平台与客舱之间的数据传输需求，分为8个部分。
 特性：
1）使用可配置： AFDX 网络启动的时候可以载入AFDX网络集成者的参数配置信息
2）全双工 物理层使用双绞线，有两对独立的发送信道和接受信道
3）交换网络 网络拓扑为星形结构，每个交换机最多可连接24个端系统，交换机间可级联以拓展成更大的网络
4）确定性 在物理链路中使用虚拟链路技术模拟了一个点到点的、具有确定性的网络
5）冗余网络 采用两个互为冗余的网络传输数据，提高网路欧通讯的可靠性并降低传送时延。

引入虚拟链路和冗余机制来实现确定性通信和可靠性通信的目的 。
通常情况下一个端系统可以支持多个航空电子子系统的数据收发处理。
在单个的物理通讯链接上建立多个逻辑通讯连接是可行的，将这种逻辑通讯链接称为虚拟链路。

对于机载数据网络，确定性是最紧迫的要求，即源端系统发送出去的每个数据帧都必须在确定的时间范围内被目的端系统接收。
因此需要端系统对传输到虚拟链路的数据进行调度，控制数据帧的传输时间。
AFDX 端系统系统协议栈虚拟链路层是实现AFDX网络确定性机制和冗余机制的关键，也是实现端系统通讯功能的核心模块。
同一条虚拟链路的每个连续发送帧的SN从0-255依次递增，到了255再返回1。端系统协议栈虚拟链路层对接受的AFDX帧的SN进行
顺序检查，判断帧是否按顺序达到或是否丢失，以确定端系统接受的帧序列的完整性，同时接受冗余管理通过判断接受帧是否
为冗余拷贝帧。
重点研究 虚拟链路层的冗余管理、流量整形和虚拟链路调度等关键技术
由于应用程序产生的帧是不规律的，即进入协议栈虚拟链路层的帧的时间间隔（BAG）是不规律的，所以在虚拟链路层需要对传输的帧
在发送前进行流量整形。虚拟链路层的流量整形是在单条虚拟链路上调整帧的发送时间，使每条虚拟链路在各自配置的时间
间隔内只能发送一个帧。 BAG  (Bandwidth Allocation Gap)带宽分配间隔，是指在没有抖动的情况下，同一条虚拟链路上的
两个连续AFDX帧的起始位之间的最小时间间隔。　
基于FIFO调度策略的调度控制模型
接受： 虚拟链路层进行完整性检查，然后进行冗余管理，丢弃被判定为无效或冗余的帧

卫星通信系统。飞机使用这个系统的发射机向外高空的通信卫星发射信号，由卫星再把这个信号转发给地面接收站，再经过一系列的传输，
就实现了飞机与地面之间的电话或图像通信。

黑匣子有两种功能：记录飞行数据和记录飞机停止工作或失事前半小时的语音数据
在黑匣子落入水中后，黑匣子会依靠自身的蓄电池，连续30天自动向四面八方发射信号，让人们来确定它的位置
集成电路存贮器已经非常成熟，能记录阻力、升力、飞行姿态等200多种飞行数据，而且准确度非常高。

增压机
从机身方面来说，航空器损伤包括疲劳损伤、应力腐蚀裂纹、耐环境性功能退化、磨损、摩擦、碎裂、凹陷、划伤和由鸟类或其他外来
物体撞击所带来的各种形式的影响，以及由雷击或强热导致的损伤。

中兴推出地空宽带方案：航班4G上网将成现实
http://tech.qq.com/a/20140415/004219.htm
4G地空宽带采用LTE无线接入技术，采用定制的无线收发设备，沿飞行航路或特定空域架设地面基站，向高空进行覆盖，可以为不同
高度层航线的飞机提供最高100Mbps以上的无线数据带宽，从而更加迅捷地访问外部互联网。

卫星宽带连接的空中上网可以更好的让旅客感受到“天地一体”的无国界网络生活
http://www.csair.com/cn/about/news/news/2014/18roh8rkd553r.shtml
升级改造后的飞机安装有可以360度旋转的极化天线及支架用于接收追踪并持续上传下载卫星信号，将信号转化后输送给遍布于客舱的
6个WIFI无线热点，实现无线网络信号完全覆盖整架飞机客舱，乘客在飞行过程中只需打开手中的笔记本电脑或PAD设备就可以轻松上网
改造后的客机通过了最严格的飞机EMI电磁兼容系统的测试，可以确保上网设备不会对飞机导航设备产生影响从而保证航空安全。
南航飞机采用Ku波段技术属于最新一代的机上宽带上网技术，已经覆盖全世界绝大部分区域
相较早前国内其他航空公司采用的星基SBB传输和ATG地基机上上网解决方案，通过Ku波段传输的高速卫星解决方案在数据带宽和稳定性
方面均有明显优势，更省去了建设地面基站的高昂费用和复杂调试流程，特别是不受航路区域限制，可以与国际航路无缝接轨。

目前应用较为普遍的基于卫星方式的地空通讯和基于ATG（地面基站方式）的地空宽带通讯方式。
飞机从地面基站获 Wi-Fi 难推广 http://money.163.com/14/0721/09/A1LS1TQ700253B0H.html
将基于信任度的安全机制加入到卫星网络路由协议中，能够在检测到恶意的内部攻击或外部攻击后，迅速的做出反应并将恶意节点排除
在路径之外。

“commercial off the shelf” (COTS)   商用货架技术
威胁来自于系统本身、系统运行过程中和人为操作。

AFDX 总线示意图
![](http://7xrkr6.com1.z0.glb.clouddn.com/16-4-29/84853130.jpg)
![](http://7xrkr6.com1.z0.glb.clouddn.com/16-4-29/28686305.jpg)
![](http://7xrkr6.com1.z0.glb.clouddn.com/16-4-29/45105253.jpg)

网关(Gateway)又称网间连接器、协议转换器。网关是一种充当转换重任的计算机系统或设备。使用在不同的通信协议、数据格式或语言，甚至体系结构完全不同的两种系统之间，网关是一个翻译器。与网桥只是简单地传达信息不同，网关对收到的信息要重新打包，以适应目的系统的需求。
数据总线技术出现以后，军用飞机的航空电子设备才实现真正意义上的航空电子系统，这并不单纯体现的各个航空电子设备需要通过总线交互信息，更重要的是其设计的系统性、运用的系统性以及由此带来的巨大效益，即系统“总功能”远远大于各个航空电子设备功能的简单累加和。

系统整体包含一个“边界”，“组织”，“动态”的概念
但是由于商用以太 网存在缺乏中央调度系统的缺点 , 导致 总线上的信源发送数据的时间延迟具有不确定性 ,对安全性 、可靠性要求非常高的民用客机系统而言 ,是极其危险的。因此 ,在商用以太网的基础上增加中央调度系统 , 可以在发送及接收数据过程中进行缓存及调度 , 以解决以太 网的不确定性问题 ,从而提高其可靠性和稳定性 。

AFDX发送流程
![](http://7xrkr6.com1.z0.glb.clouddn.com/16-4-20/11021512.jpg)

### 1553B
#### 1553B控制消息传输
1553B标准规定了各种字（指令字、状态字、数据字）格式、消息组成和方式码格式，用户可以按照这些标准格式组织应用数据。
综合航空电子系统结构设计时，首先要确定系统中所有物理但愿的数据传输需求，并决定将哪一个数据在什么时候从哪里传送到哪里，以什么方式传输。数据传输需求与物理单元功能、信息交互过程密切相关，但是传输方式却与1553B传输层协议相关。在传输层，消息传输分为周期消息和非周期消息，周期消息是指必须按照固定时间间隔传输的消息，非周期消息一般受事件驱动（如操作者的操作）或者受数据驱动传输，具有随机性。在航空电子系统中，周期消息和非周期消息并存，消息传输控制应该满足周期消息的时间要求，同时又能及时响应非周期消息的传输请求。
#### 错误处理
总线数据通信通过远程终端和传输介质来完成，由噪声和干扰等引起的传输错误、远程终端错误是造成通信失败的主要错误。
传输错误处理：传输错误可以由远程终端或总线控制器检测到，远程终端检测到的错误有指令字格式错、消息字中的奇偶校验错和无效消息等。远程终端对检测到错误的处理方法是发送一个“消息差错”位置1的状态字，对无效消息则不发送状态字。
远程终端故障处理：如果远程终端没有完全丧失能力，远程终端的故障可以由终端自检测发现。一旦远程终端故障恢复正常，再将其连入总线系统。
#### 可靠性设计
一方面是保证总线控制器、传输线缆、数据传输过程的可靠，当一个总线控制器或一根传输线缆出现故障，不会导致整个系统数据传输过程的中断。另一方面是一旦在总线传输各环节出现故障，系统结构要有故障检测机制，能够识别故障，并采取相应的故障处理策略，尽可能保证总线通信功能的正常发挥。

总线控制器的可靠性
静态备份：指系统中只有一个主总线控制器，可以有若干个备份总线控制器
总线数据传输的可靠性






