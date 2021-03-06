# Lab11 Functional Debugging
### student number：14353223
### name: 罗海福

* **实验环境配置**：
 
		1.	解压安装文件
	![](http://p1.bqimg.com/567571/e79ad896b42c5e83.png)

	
		2.	安装MDK474
	![](http://i1.piimg.com/567571/e6b39835240e0ec2.png)

	![](http://i1.piimg.com/567571/60f0c7a4a772d2a5.png) 


		3.	安装EE319K_InstallSpring2016
	![](http://i1.piimg.com/567571/a04d51bf4ef9c199.png)

* **实验目的和要求**
	*	熟悉keli调试器的使用
	*	熟悉汇编语句，能够看懂简单的汇编代码，并能够找出错误
	*	运行代码后，观察Register和Memory的值的变化，
	*	找出Cnt变量的最终值和HappyBuf的最终值

* **实验结果及分析**
	* 第一行的前五个即前40位数字即HappyBuf的值，Cnt的值是第二行中绿色的	00000014。
	![](http://p1.bpimg.com/567571/0de86d2e2022b079.png)
	*	实验代码分析
	![](http://p1.bpimg.com/567571/27a166a37fc6a0ec.png)
	*	我们可以看到定义了再内存中的分配空间，ALIGN=2，SIZE的值是20，所以HappyBuf，SadBuf的存储位数是40，Cnt，M是8位，happy和sad是2位。所以我们可以看到这样的实验结果即分配方式。
	![](http://p1.bpimg.com/567571/c92f20bc228dfeda.png)
	*	这里执行一个循环，跳转到循环函数，然后将得到的随机数的低8位存储起来，调用save函数。
	![](http://p1.bpimg.com/567571/0e217a7887a00197.png)
	*	观察次结果可以看到，先将得到的随机数值存储在happy与sad中，然后执行save函数，再进行存储在HappyBuffer与SadBuffer中。
	![](http://i1.piimg.com/567571/b1e4ca113486ab44.png)
	*	首先得到当前的Cnt值即数组偏移量，判断当前数组是否存储满了，如果存储满了直接跳出函数，如果没有满，将Happy与sad的值存储在buffer中。
	*	通过观察下图结果可以发现在Buffer满了之后Buffer中的值没有再进行变化，只有计算随机数的M值还有Happy与sad的值在变化。
	![](http://i1.piimg.com/567571/c8a8217dea31e70f.png)
	![](http://i1.piimg.com/567571/52c1244c35cc8620.png)
	*	我们得到的结果是8位中从低位开始存储，每次存储两个字节，最后得到的cnt值是14，因为是十六进制，转化为十进制是20，也验证了我们的答案是40位。
* **实验感想和反思**
	*	实验开始的时候其实并不是很理解题目的意思，就好像是程序会进入一个死循环，我们需要找到错误，一开始就想到一条一条的指令逐条执行，虽然比较麻烦，但是会直接的将错误显示出来，发现程序在cnt大于SIZE值之后会进入到一个死循环里面，所以我们需要给进入loop循环前加入一个条件。
	*	采用单步追踪运行，基本上我们如果要得到一个值都是要先得到值的存储地址，再把对应地址里面的值写到别的寄存器，观察到的寄存器的值如果是跟预期一样，取值每次取得对应的地址都一样。Cnt最终取值为0x00000014也就是20，根SIZE的值一样，因为转存时会先判断cnt是否越界，若否，则将变量转存再将Cnt加1。