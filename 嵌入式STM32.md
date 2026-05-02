# 第一节

## 接线

STLINK    开发板
SWCLK->SWCLK

SWDIO->SWO

GND->GND

3.3v->3.3v

---



## 导入标准库

首先新建工程，选择你的开发板

然后添加启动文件，在你的标准库的STM32F10x_StdPeriph_Lib_V3.5.0\Libraries\CMSIS\CM3\DeviceSupport\ST\STM32F10x\startup\arm目录下

将全部文件复制下来

![嵌入式开发1](嵌入式开发1.png)

放到工程的start文件夹(新建)

返回上一级的上一级，也就是STM32F10x，将.h和.c文件也放入start文件夹，这些是寄存器和时钟配置文件

然后去STM32F10x_StdPeriph_Lib_V3.5.0\Libraries\CMSIS\CM3\CoreSupport

这里面是内核的寄存器描述文件，也放到start文件夹内

回到keil5修改文件夹名，然后添加已经存在的文件

![嵌入式开发2](嵌入式开发2.png)

在这里要添加的启动文件是startup_stm32f10x_md.s，然后其他的非.s文件全部添加进来

点魔术棒，选择C/C++IncludePaths新建路径，添加start路径

接下来可以新建文件测试，建议放入User文件夹

然后在工程新建Libary存放库函数

内核库文件STM32F10x_StdPeriph_Lib_V3.5.0\Libraries\STM32F10x_StdPeriph_Driver\src

STM32F10x_StdPeriph_Lib_V3.5.0\Libraries\STM32F10x_StdPeriph_Driver\inc

将所有的放到keil5的对应文件夹内

接着是配置文件

STM32F10x_StdPeriph_Lib_V3.5.0\Project\STM32F10x_StdPeriph_Template

下的
_conf.h

_it.c

_it.h

放到User

然后F12打开stm32f10x.h

在8296行的ifdef添加到魔术棒的C/C++的define
当然记得将User和Libary添加到include paths

然后点三个箱子按钮将Libary向上动一下

---



## 编写简单电灯

首先，我们灯的位置为P13端口，对应的GPIO就是GPIOC(13对应D，但是由于GPIO是0开头的所以对应的是C)

然后写

```c
#include "stm32f10x.h"                  // Device header
GPIO_InitTypeDef GPIO_InitStructure;
int main(void){
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC,ENABLE);
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_13;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOC,&GPIO_InitStructure);
	//GPIO_SetBits(GPIOC,GPIO_Pin_13);//关灯
	GPIO_ResetBits(GPIOC,GPIO_Pin_13);//开灯
	while(1){
	
	}
}
```



RCC_APB2PeriphClockCmd外设时钟控制	

按f12进入后可以看到@arg中的参数，其中RCC_APB2Periph_GPIOC就是我们要的

GPIO_Init，初始化GPIO，也是F12进入后看到参数

GPIO_InitTypeDef是GPIO的结构体定义

​	其中GPIO_Mode按下F12打开后需要ctrl+f寻找，可以看到对应的枚举

​	选择GPIO_Mode_Out_PP（推免输出）

​	然后是GPIO_Pin，F12后底部窗口(表示定义有很多，选择member)

---



## 补充

为什么选md.s文件

![嵌入式开发3](嵌入式开发3.png)



架构图

![嵌入式开发4](嵌入式开发4.png)



---
# GPIO
## GPIO是什么
通用输入输出口（类似编程语言的通用IO库？
- 可配置8种输入输出模式
- 引脚电压：0V~3.3V，部分引脚可容忍5V
![[嵌入式开发5.png]]
## 长什么样
![[嵌入式开发6.png]]
ps:具体的还在课里，没看懂，明天再看看

---
# 点个灯吧

先上程序
根据课里说的，很多库都可以F12查看实现来(英语不好的有福了 :（  )
``` c
#include "stm32f10x.h"                  // Device header

int main(void){
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);
	GPIO_Init(GPIOA,&GPIO_InitStructure);
	//GPIO_ResetBits(GPIOA,GPIO_Pin_0);
	GPIO_SetBits(GPIOA,GPIO_Pin_0);
	while(1){
		
	}
}

```
再上接线图
当然
这个LED灯有点问题
这个灯 长的是正极，短的是负极，我第一次安装就安装错了
![[嵌入式开发7.png]]
接下来需要查看函数在stm32f10x_gpio.h有gpio的定义，在... _ rcc.h中有rcc的定义
```c
void RCC_AHBPeriphClockCmd(uint32_t RCC_AHBPeriph, FunctionalState NewState);
void RCC_APB2PeriphClockCmd(uint32_t RCC_APB2Periph, FunctionalState NewState);
void RCC_APB1PeriphClockCmd(uint32_t RCC_APB1Periph, FunctionalState NewState);
这三个是常用的rcc

GPIO_InitTypeDef是GPIO定义的结构体
```
接着是LED闪烁，我使用的江科大的那个大佬自己做的，他课程里没讲什么，先插个眼
然后研究了开漏输出和推挽输出的区别
当使用推挽输出
将LED灯位置切换为长脚(正极)在p0，短脚(负极)在负极的情况，这个时候是高电平驱动
`GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;`依然可以使用
这说明委婉输出在高低电平都有效
开漏输出时高电平却没有输出能力，因为开漏输出是高阻态，高阻态的高电平是没有驱动能力的
注意IO口不能并联
高级点的灯哈哈哈
```c
#include "stm32f10x.h"                  // Device header
#include "Delay.h"

int main(void){
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_All;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);
	GPIO_Init(GPIOA,&GPIO_InitStructure);
	//GPIO_ResetBits(GPIOA,GPIO_Pin_0);//开启
	//GPIO_SetBits(GPIOA,GPIO_Pin_0);//关闭
	//GPIO_WriteBit(GPIOA,GPIO_Pin_0,Bit_SET);//通过第三个参数决定关闭还是打开
	int tmp = 0x0001;
	while(1){
		GPIO_Write(GPIOA,~tmp);
		Delay_ms(500);
		tmp=tmp<<1;
		if (tmp>=0x0080){
			tmp = 0x0001;
		}

	}
}

```
在这里，0x001就是p0口
上面的GPIO_Pin_All，是所有端口推挽输出，和江科大的程序有点出入，因为我用了按位左移
## 蜂鸣器
```c
#include "stm32f10x.h"                  // Device header
#include "Delay.h"

int main(void){
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);
	
	GPIO_InitTypeDef GPIO_InitStructure;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Pin = GPIO_Pin_12;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(GPIOB,&GPIO_InitStructure);
	while(1){
		GPIO_ResetBits(GPIOB,GPIO_Pin_12);
		Delay_ms(500);
		GPIO_SetBits(GPIOB,GPIO_Pin_12);
		Delay_ms(500);
	}
}

```