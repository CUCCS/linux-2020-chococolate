## 实验四：SHELL脚本编程实验
#### 实验环境

#### 实验要求
**任务一：用bash编写一个图片批处理脚本，实现以下功能：**
- [x] 支持命令行参数方式使用不同功能
- [x] 支持对指定目录下所有支持格式的图片文件进行批处理
- [x] 支持以下常见图片批处理功能的单独使用或组合使用
    - [x] 支持对jpeg格式图片进行图片质量压缩
    - [x] 支持对jpeg/png/svg格式图片在保持原始宽高比的前提下压缩分辨率
    - [x] 支持对图片批量添加自定义文本水印
    - [x] 支持批量重命名（统一添加文件名前缀或后缀，不影响原始x文件扩展名）
    - [x] 支持将png/svg图片统一转换为jpg格式图片


**任务二：用bash编写一个文本批处理脚本，对以下附件分别进行批量处理完成相应的数据统计任务：**
- [x] 2014世界杯运动员数据
    - [x] 统计不同年龄区间范围（20岁以下、[20-30]、30岁以上）的球员数量、百分比
    - [x] 统计不同场上位置的球员数量、百分比
    - [x] 名字最长的球员是谁？名字最短的球员是谁？
    - [x] 年龄最大的球员是谁？年龄最小的球员是谁？
  
**任务三：用bash编写一个文本批处理脚本，对以下附件分别进行批量处理完成相应的数据统计任务：**
- [x] Web服务器访问日志
    - [x] 统计访问来源主机TOP 100和分别对应出现的总次数
    - [x] 统计访问来源主机TOP 100 IP和分别对应出现的总次数
    - [x] 统计最频繁被访问的URL TOP 100
    - [x] 统计不同响应状态码的出现次数和对应百分比
    - [x] 分别统计不同4XX状态码对应的TOP 10 URL和对应出现的总次数
    - [x] 给定URL输出TOP 100访问来源主机
  
#### 实验准备
1.安装task1需要的工具imagemagick


2.下载task2需要处理的文件

    wget "https://c4pr1c3.github.io/LinuxSysAdmin/exp/chap0x04/worldcupplayerinfo.tsv"
3.下载task3需要处理的文件

    sudo apt install p7zip-full
    wget "https://c4pr1c3.github.io/LinuxSysAdmin/exp/chap0x04/web_log.tsv.7z"
    7z x web_log.tsv.7z
#### 实验过程
注：本实验的bash文件及txt文件上传在另一个库中
1. [try1.sh](https://github.com/chococolate/shells/blob/master/try2.sh)


    图片结果展示
    ![]([../images/1.jp](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x04/images/1.jpg)g)
2. [try2.sh](https://github.com/chococolate/shells/blob/master/try2.sh)


   图中展示为try2.sh执行结果（有图为鉴，真的手动敲了并执行成功了哦）[link]([../images/2.png](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x04/images/2.png))
3. [try3.sh](https://github.com/chococolate/shells/blob/master/try2.sh)


   * 访问来源主机TOP 100和分别对应出现的总次数统计结果[link](https://github.com/chococolate/shells/blob/master/try1.txt)

    * 访问来源主机TOP 100 IP和分别对应出现的总次数结果[link](https://github.com/chococolate/shells/blob/master/try2.txt)

    * 最频繁被访问的URL TOP 100结果[link](https://github.com/chococolate/shells/blob/master/try3.txt)

    * "不同响应状态码的出现次数和对应百分比结果”与“不同4XX状态码对应的TOP 10 URL和对应出现的总次数结果”（~~比较短，懒得传文件了~~）![](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x04/images/3.1.png)
   ![](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x04/images/3.1.png)

    * 给定URL（/whats-new.html）输出TOP 100访问来源主机结果[link](https://github.com/chococolate/shells/blob/master/try5.txt)

#### 实验总结
  * 所有bash代码都是自己敲的，在敲的过程中进一步加深了对语法的了解（发现了参考哥姐代码的小错误哈哈哈）
  
  * 刚开始还在想怎么把.sh文件传到本机再传到GitHub，后来看了视频才知道虚拟机可以远程git,又又又被自己蠢到 = =！
  
#### 参考资料
  https://github.com/CUCCS/linux-2019-QRiddle/tree/homework4/shell
  