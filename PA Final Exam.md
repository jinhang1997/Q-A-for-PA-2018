# NUAA PA 2018 期末答辩

# 答辩安排

## 答辩时间
`2018/06/14 13:30~17:30`，请在该时段内到达，由于排队时间可能较长，请不要来的太晚，以免到结束时间还未轮到你。

## 答辩地点
`教室：6202`，请不要走错教室影响成绩。

## 答辩性质
相当于PA期末考试，占课程总成绩的`20%`；

## 答辩形式和要求
1. 按个人为单位进行答辩；
2. 两个专业**同时同地**进行，分专业排队，每个专业两条队伍，每个专业两条队伍（`软件工程专业由金航和应亦周负责、信息安全专业由陈浩和邓理负责`）。
3. 自带电脑，保证项目能立刻运行；
4. 按上述要求排队；
5. 轮到后每人5分钟左右，**5个问题**，由助教提问，按要求操作或回答问题；

## 答辩内容
1. **PA0~PA3所有要求做过的内容**，评分重点见文末；
2. 问的问题包括所有评分项，对于每一个问题都需要当场操作，查功能运行、查熟悉程度、查实现原理；
3. 每个人的问题抽签决定。

### 抽签规则
1. 总共有三个盒子，每个问题被分别写在纸条上放入对应章节的盒子里。
2. 问题分布：`PA0`抽`1`个问题；`PA1+PA2`抽`3`个问题；`PA3`抽`1`个；每个问题均为`20`分，总分`100`分；
3. 自己从盒子里抽问题；
4. 公平起见，抽到的题不管自己有没有做到，都需要回答。

## 注意事项
1. 不在规定时间范围内到场答辩的，一律视作未到，答辩部分`0`分；
2. 重修同学请注意：请排在**你选课的老师的专业**的答辩队伍里，否则登不上成绩。选陈丹老师课的在`信息安全`队伍，选李博涵老师课的在`软件工程`队伍。

## 题目样例

### （PA0/Linux的使用）请你在`~`路径下创建一个名为`ab.c`的空文件
使用命令`touch ab.c`即可

### （PA1）请你加载任意程序演示`info r`的功能，并找到该命令的处理函数在哪里，然后解释为什么这样写
处理函数在`nemu\src\monitor\debug\ui.c`中（你需要自带电脑打开该代码文件），实现方法为写一个循环，遍历`cpu.gpr`数组即可

### （PA2）请你执行AM下的CPU测试用例`if-else`并说明其中用到的`cmp`和`jcc`指令是如何配合工作实现分支的
`cmp`指令对给出的两个操作数进行相减操作，并对相关标志位进行更新，随后，通过合适的`jcc`指令的具体形式进行跳转（或者不跳转），跳转到对应分支条件的第一条语句处

### （PA3）请你在`Nanos-lite`机制下运行`/bin/hello`程序，并找到`sys_close`系统调用以及`fs_close`的代码位置，然后分别解释其实现
系统调用在`nanos-lite\src\syscall.c`中，函数在`nanos-lite\src\fs.c`中，由于我们的简易文件系统不涉及文件打开状态的维护，因此`fs_close`中只需要直接返回`0`表示关闭文件成功即可

# 附录：各小节评分项

## PA0
1. Installing a GNU/Linux VM
2. First Exploration with GNU/Linux
3. Installing Tools
4. Configuring vim
5. The power of vim
6. More Exploration
7. 编写运行Hello World 小程序
8. Transferring Files between host and container
9. Transfer Files – Have a try
10. Acquiring Source Codes for Pas

## PA1
### PA1.1
1. CPU_state
2. cmd_info
3. cmd_x
4. cmd_si

### PA1.2
1. 算术运算
2. 逻辑运算
3. 关系运算
4. 十六进制
5. 寄存器
6. 单目运算
7. 指针解引用
8. 负数
9. 监视点结构体
10. 新建监视点
11. 删除监视点
12. 打印监视点
13. cmd_w()
14. cmd_d()
15. cmd_info()

## PA2
### PA2.1
1. eflags寄存器
2. 更新eflags
3. rtl_push和rtl_pop
4. call指令
5. push指令
6. pop指令
7. sub指令
8. xor指令
9. ret指令
10. dummy程序 HIT GOOD TRAP

### PA2.2
1. 完成指令
2. runall.sh一键测试
3. diff-test的实现并能正常使用
4. PA2.2部分思考题
5. PA2.2部分的git log
6. 串口in/out指令的实现并成功运行helloworld
7. 时钟的实现并成功运行timetest
8. 成功运行三个跑分项目
9. 键盘的实现并成功运行keytest
10. VGA内存映射I/O的实现
11. 完成画矩形函数并成功运行videotest
12. 成功运行打字小游戏

## PA3
### PA3.1
1. loader()函数
2. IDTR寄存器
3. LIDT指令
4. INT指令与raise_intr函数
5. PUSHA指令
6. _RegSet结构体
7. do_event函数识别系统调用事件
8. SYSCALL_ARGx()宏
9. do_syscall函数识别并实现none和exit系统调用并设置返回值
10. POPA指令
11. IRET指令
12. 成功运行dummy

### PA3.2
1. 简易标准输出的write调用
2. 运行hello
2. 实现sbrk函数和系统调用
3. 运行在堆区管理机制上的hello
3. fs_open()
4. fs_read()
5. fs_close()
6. fs_filesz()
7. 实现Loader
8. 使用Loader加载并运行hello
8. fs_write()
9. fs_lseek()
10. 成功运行程序/bin/text
11. init_fs()
12. fb_write()
13. init_device()
14. dispinfo_read()
15. 增加对fb和dispinfo的支持
16. 成功运行bmptest
17. events_read()
18. 增加对events的支持
19. 实现movs/movsb/movsw/movsd指令
20. 运行仙剑奇侠传并开启新的故事，能看到李逍遥和李大娘对话的画面








