## vimtutor实验报告
### 实验步骤
#### Step 1.asciinema注册账号并安装配置asciinema
* 用putty连接虚拟机，在虚拟机上安装asciinema。
输入：sudo pip3 install asciinema .

(实验过程中当然不可能那么简单而顺利啦！因为虚拟机没有装pip,所以会报错，需要执行sudo apt install python-pip 但是这样也是会报错滴哦因为python要在3.0版本以上哦~装了两次python-pip我太南啦！)
#### Step 2.确认asciinema auth,且asciinema以关联本地账号与在线账号
* 在putty命令行输入 asciinema auth ,返回有网址说明关联成功。
#### Step 3.进入vimtutor,开始“机器”学习
* [Lesson 1-3](https://asciinema.org/a/pfqZSinXU7U6kzUC7S7qTT4W1)
* [Lesson 4-7](https://asciinema.org/a/xtZe1gH9CfvfQ6BjIygBCxmzK)

(上次试验还在吐槽vim语法不友好，这回就真香了！不过弱弱说一句：vim为什么没有记忆= =？分段录制的时候总是要从Lesson 1往下拉回到现在的课程，或许有相应的语法我不知$_$)
### Feedback自查清单
* 你了解vim有哪几种工作模式？
    * 一般模式【Esc】
    * 编辑模式【此时左下方一般会有insert/replace/etc】
    * 指令模式【：、？】
    * 可视模式【v进入】（课程学习中体会不深= =）】


* Normal模式下，从当前行开始，一次向下移动光标10行的操作方法？如何快速移动到文件开始行和结束行？如何快速跳转到文件中的第N行？
    + 向下移动10行：10j
    + 文件头：gg 文件为：G
    + 跳转N行：NG（貌似可以弥补没有记忆的槽点）


* Normal模式下，如何删除单个字符、单个单词、从当前光标位置一直删除到行尾、单行、当前行开始向下数N行？
    + 删除单字符 x
    + 删除单个单词 dw/de
    + 当前到行尾：d$
    + 删除单行：dd
    + 删除以下N行：Ndd


* 如何在vim中快速插入N个空行？如何在vim中快速输入80个-?
    * 插入N个空行：NO（前插），No(后插)
    + 输入80个-：80i-(Lesson里貌似无，xdlxdl!)


* 如何撤销最近一次编辑操作？如何重做最近一次被撤销的操作？
    + 撤销：u
    + 重做撤销： ctrl+r


* vim中如何实现剪切粘贴单个字符？单个单词？单行？如何实现相似的复制粘贴操作呢？
    + 复制单个字符：yl
    + 复制单个单词：yw
    + 复制单行:yy
    + 复制以后N行：Nyy
    + 粘贴一律： p


* 为了编辑一段文本你能想到哪几种操作方式（按键序列）？
    + i,a,A,u
    * c motion ,d motion
    * O ,o;


* 查看当前正在编辑的文件名的方法？查看当前光标所在行的行号的方法？
    * ctrl+g


* 在文件中最近编辑过的位置来回快速跳转的方法？
    + ctrl+o【后跳】/ctrl+i【前跳】


* 如何把光标定位到各种括号的匹配项？例如：找到(, [, or {对应匹配的),], or }
    + 光标停留在想要查询括号上，按%键


* 在不退出vim的情况下执行一个外部程序的方法？
    + ！programname(xdlxdl!那可以在vim里启动asciinema的叭？自己实践貌似不可)


* 如何使用vim的内置帮助系统来查询一个内置默认快捷键的使用方法？如何在两个不同的分屏窗口中移动光标？
    * :help 快捷键名称
    * 移动光标： CTRL+w +<h/j/k/l>