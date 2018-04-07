# 授课内容 PA2.1

> CPU流水线执行指令的五个阶段？ 
>
> 取指 译码 执行 访存 写回 

## NEMU的主体代码框架
``` c
init_monitor();
reg_test();
load_img();
restart();
ui_mainloop();
```

在NEMU中，用函数`exec_wrapper()`代表那**五个阶段**，自己去看这个函数写了什么
指令格式照PPT讲即可。

## NEMU框架是如何执行指令的
### 取指
取eip当前指向地址的一个字节

### 译码
将取出的字节在`opcode_table`作为`索引`，找到对应的项目。
> 对应的项目是个什么？（指令）
>
> opcode_entry是什么变量类型？（结构体）
>
> 结构体的三个域分别是什么？（译码函数、执行函数、指令宽度）

写好一个指令，首先要让它能被译码，则需要填写`opcode_table`（建议写好执行函数再填，以免过不了编译搞的自己很紧张）
填写项目格式：`IDEXW(id, ex, w)` 这一个宏其实就够了，PPT上面给出其他的几个可以填到这里的宏无非也是层层嵌套最终还是这个，搞得懂意思可以用那几个，搞不懂则老老实实用这个

> 特殊情况1：0x0f两字节，简单，再查一下两字节表即可（即opcode_table的后一半）
>
> 特殊情况2：opcode和ModR/M字节组合查表（即8个group）。使用ModR/M字节中的reg/opcode字段对opcode进行扩充。这个也简单，即把组名填到opcode_table就行了，然后在组里的第几号就填到其应该在的组里即可（组的定义也在exec.c里面）

寻址方式的缩写见i386手册附录A。译码操作由填写的译码函数执行，具体每个指令用哪个译码函数见386手册对于每个指令的解释。
> 注意，**不可能译码出双操作数均为内存的指令**，为什么？5分。答案是为了节约指令长度，两个地址会导致一个指令很长），译码函数的宏make_DHelper(name)

译码完成后，我们需要的操作数已经被放进了`id_dest` `id_src` `id_src2`这三个“变量”中，这三个“变量”是什么，看框架代码。

### 执行
从指令表中取出该`项目`的`执行函数`
程序将跳转到执行函数开始执行执行函数，执行函数通过宏`make_Ehelper(name)`，例子参见NEMU框架中送给大家的已经写好的若干指令如何写的。
如何编写执行函数模拟指令，我们稍后谈，并现场写一个给大家举例。

### 访存
访问目标和源操作数

### 写回
将计算结果写入目标操作数，使用宏operand_write()，有些比较特殊的寄存器，如`eip`可能不会用到该宏

## 如何编写执行函数
以已经写好的adc带进位加法指令为例，经历以下步骤：
1. 查386手册`ADC页`，了解该指令的形式、描述和操作，发现操作即：

```
DEST ← DEST + SRC + CF;
```

2. 使用`RTL系列`函数获取`CF`值，并将CF和源操作数和目标操作数相加
3. 将**加完的结果通过operand_write写入id_dest**
4. 根据情况**对各个eflags的位进行影响**
5. 打印显示在屏幕上的汇编代码
6. **填写opcode_table**
	
## 需要做的事情
1. **写eflags寄存器**。寄存器结构和各个位的意义见386手册和讲义。
> 写在哪里？（5分，答案：PA1里面实现的那个CPU结构体中扩充）
>
> 如何写？使用位域。何为位域？（5分，答案：将一个uint32_t拆成若干个小分组来分别访问，同样要用到结构体和/或联合体）

注意：建议和一个uint32_t进行联合，以便可以直接给eflges赋值。

2. 实现一些基本的RTL指令，最基本的如更新eflags系列指令和rtl_push rtl_pop等，其操作已在rtl的注释中给出
> 提问：写在哪里？（5分，答案`include/cpu/rtl.h`）

注意：**使用已经写好的部分RTL指令来访存**。

3. 利用基本的RTL指令和自己写的部分RTL指令来写call push pop sub xor ret六个指令。
> 写在哪里？`src/cpu/exec/*.c`，每个C文件对应一个种类的指令，全写在里面即可

4. 运行dummy小程序并`hit good trap`。
> 注意：写的时候注意**灵活使用临时寄存器t1~t4和tzero**，帮助你设置eflags标志位。
	
### 实战：编写eflags
``` c
struct {
  /* 通过位域实现，自己想              *
   * 建议在结构体外留一个直接赋值的接口 */
} eflags;
```

### 实战：举个例子，实现RTL的push指令
原本框架给的样子：
``` c
static inline void rtl_push(const rtlreg_t* src1) {
  // esp <- esp - 4
  // M[esp] <- src1
  TODO();
}
```
> TODO是什么？5分，答案是：宏定义`panic("please implement me")`。panic是什么，其实就是无条件停止程序，提示你你这里需要实现。

完成后：
``` c
static inline void rtl_push(const rtlreg_t* src1) {
  // esp <- esp - 4
  // M[esp] <- src1
  /* 通过已经实现的RTL指令给esp减去4 */
  /* 通过已经实现的RTL指令把src1写入esp指向的内存，宽度为4个字节 */
  // TODO(); 删去即可
}
```

### 实战：举个例子，通过刚刚我们实现的rtl_push来写PUSH的执行函数
1. 查询386手册的`PUSH页`（PDF第367页），得知PUSH的操作
说白了，就是先给ESP减去2或4，然后将要压栈的值写入减后的ESP指向的地址（黑板上画图表示）
2. 获取需要的值（对于PUSH，需要压栈的值已在`id_dest`中）
3. 访存与写回，由于我们的`rtl_push`已经做完了访存操作，这里于是调用一下就OK：
``` c
rtl_push(&id_dest->val);
```
4. 使用打印模板打印显示在屏幕上的汇编代码
写完以后的样子：
``` c
make_EHelper(push) {
  /* 调用写好的rtl_push()函数即可 */
  /* 由于push指令只有1个操作数，调用print_asm_template1宏即可打印 */
}
```
5. 填写`opcode_table`。由于这里是push的`r32`形式，故指令码`0x50+/rd`
> 提问：+/rd是什么意思？5分，答案在手册246页。

注意这个`+/rd`，在手册第246页，其他的缩写的意义都在这个地方，哪个不清楚的自己查就好了。由此可知我们在`opcode_table`需填写8个格子为`push指令`。顺便类比着看POP（当然，rtl_pop自己写）：

``` c
make_EHelper(pop) {
  /* 调用rtl_pop()取出一个值到临时寄存器 */
  /* 使用operand_write()将取出的临时值写入目标寄存器 */
  /* 由于push指令只有1个操作数，调用print_asm_template1宏即可打印 */
}
```

### 下面我们来展开几个宏看看吧
``` c
  make_EHelper(name) -> void concat(exec_, name) (vaddr_t *eip)
  make_EHelper(mov) -> void exec_mov(vaddr_t *eip)
  make_rtl_arith_logic(add)
 -> 
  static inline void rtl_add (rtlreg_t* dest, const rtlreg_t* src1, const rtlreg_t* src2) {
    *dest = concat(c_, name) (*src1, *src2); 
  }
 ->
  static inline void rtl_add (rtlreg_t* dest, const rtlreg_t* src1, const rtlreg_t* src2) {
    *dest = c_add(*src1, *src2); 
  }
 ->
  static inline void rtl_add (rtlreg_t* dest, const rtlreg_t* src1, const rtlreg_t* src2) {
	*dest = ((*src1) + (*src2))
  }
```

### 需要多熟悉宏展开的文件：
> include/cpu/rtl.h
>
> include/cpu/exec.h
>
> include/cpu/decode.h

### 对客户程序进行反汇编：
1. 客户程序均在`nexus-am`这个子工程中，PA2.1只用到`dummy`，2.2会用到剩下的
2. 客户程序在`~/ics2017/nexus-am/tests/cputest`中，源文件在`src/`中，交叉编译后的客户程序二进制文件在`build/`中
3. 进入`build/`中，会看到`dummy-x86-nemu.txt`，这里存的就是反汇编的汇编代码，绿的是可执行文件，也可以`objdump`来反汇编：
``` bash
objdump -d dummy-x86-nemu > dummy.txt
cat dummy.txt
```

### 最后来看看运行dummy要写的6个指令
以此为例，PA2.2的时候不再每个指令举例说明
> CALL：讲义上说的很清楚
PUSH POP：刚刚演示过了
SUB：参考框架中写好的make_EHelper(sbb)，但是我们的SUB不需要减CF就行了
XOR：调用rtl_xor进行异或运算，运算结果写操作数，更新ZF SF CF OF，然后调用模板打印汇编代码
RET：调用rtl_li读一个立即数，然后用rtl_push压栈，然后设置跳转：
``` c
decoding.is_jmp = 1;
```

	
	
	
