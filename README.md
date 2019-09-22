# Singlechip
## 在KEIL中创建一个工程
   - project <br/> 
   --> New uVision Project <br/> 
   --> 设置保存路径和工程名 <br/> 
   --> 选择单片机的类型（51：Atmel->AT89C51）<br/> 
   --> 确定后会弹出一个窗口询问是否将8051的启动代码复制到项目文件夹并将文件添加到项目，选否 <br/> 
   --> 创建一个C文件 <br/> 
   --> 并将这个C文件添加到源组中（右击源组，选中“Add files to Group'Source Group'”） <br/> 
   --> 运行C程序，生成 .hex 文件（点击上方图表"target options" -> output -> 勾选"Greate HEX"）<br/>
## reg52.h头文件的作用
   - 在代码中引用头文件，其实际意义是将头文件中的所用内容都放到引用头文件的地方
   - 下面是reg52.h头文件的内容：
   ```
   /*--------------------------------------------------------------------------
REG52.H

Header file for generic 80C52 and 80C32 microcontroller.
Copyright (c) 1988-2002 Keil Elektronik GmbH and Keil Software, Inc.
All rights reserved.
--------------------------------------------------------------------------*/

#ifndef __REG52_H__
#define __REG52_H__

/*  BYTE Registers  */
sfr P0    = 0x80;
sfr P1    = 0x90;
sfr P2    = 0xA0;
sfr P3    = 0xB0;
sfr PSW   = 0xD0;
sfr ACC   = 0xE0;
sfr B     = 0xF0;
sfr SP    = 0x81;
sfr DPL   = 0x82;
sfr DPH   = 0x83;
sfr PCON  = 0x87;
sfr TCON  = 0x88;
sfr TMOD  = 0x89;
sfr TL0   = 0x8A;
sfr TL1   = 0x8B;
sfr TH0   = 0x8C;
sfr TH1   = 0x8D;
sfr IE    = 0xA8;
sfr IP    = 0xB8;
sfr SCON  = 0x98;
sfr SBUF  = 0x99;

/*  8052 Extensions  */
sfr T2CON  = 0xC8;
sfr RCAP2L = 0xCA;
sfr RCAP2H = 0xCB;
sfr TL2    = 0xCC;
sfr TH2    = 0xCD;


/*  BIT Registers  */
/*  PSW  */
sbit CY    = PSW^7;
sbit AC    = PSW^6;
sbit F0    = PSW^5;
sbit RS1   = PSW^4;
sbit RS0   = PSW^3;
sbit OV    = PSW^2;
sbit P     = PSW^0; //8052 only

/*  TCON  */
sbit TF1   = TCON^7;
sbit TR1   = TCON^6;
sbit TF0   = TCON^5;
sbit TR0   = TCON^4;
sbit IE1   = TCON^3;
sbit IT1   = TCON^2;
sbit IE0   = TCON^1;
sbit IT0   = TCON^0;

/*  IE  */
sbit EA    = IE^7;
sbit ET2   = IE^5; //8052 only
sbit ES    = IE^4;
sbit ET1   = IE^3;
sbit EX1   = IE^2;
sbit ET0   = IE^1;
sbit EX0   = IE^0;

/*  IP  */
sbit PT2   = IP^5;
sbit PS    = IP^4;
sbit PT1   = IP^3;
sbit PX1   = IP^2;
sbit PT0   = IP^1;
sbit PX0   = IP^0;

/*  P3  */
sbit RD    = P3^7;
sbit WR    = P3^6;
sbit T1    = P3^5;
sbit T0    = P3^4;
sbit INT1  = P3^3;
sbit INT0  = P3^2;
sbit TXD   = P3^1;
sbit RXD   = P3^0;

/*  SCON  */
sbit SM0   = SCON^7;
sbit SM1   = SCON^6;
sbit SM2   = SCON^5;
sbit REN   = SCON^4;
sbit TB8   = SCON^3;
sbit RB8   = SCON^2;
sbit TI    = SCON^1;
sbit RI    = SCON^0;

/*  P1  */
sbit T2EX  = P1^1; // 8052 only
sbit T2    = P1^0; // 8052 only
             
/*  T2CON  */
sbit TF2    = T2CON^7;
sbit EXF2   = T2CON^6;
sbit RCLK   = T2CON^5;
sbit TCLK   = T2CON^4;
sbit EXEN2  = T2CON^3;
sbit TR2    = T2CON^2;
sbit C_T2   = T2CON^1;
sbit CP_RL2 = T2CON^0;

#endif
   ```
## 点亮一个LED
```
#include<reg52.h>
//声明管脚
sbit led=P0^0;
void main(){
	while(1){
		led=1;
	}	
}
```
## 延时函数
```
	#define uint unsigned int
	void delayms(uint xms){
		uint i, j;
		for(i=xms;i>0;i--)		//延时xms毫秒（ms）
			for(j=110;j>0;j--);
	}
```
## 
## LED流水灯
   - C语言常用预处理命令 **宏定义#define使用：#define 新名称  原内容（不加分号）**//对同一个内容，宏定义只能定义一次
   - 循环左移和右移函数,包含在instrins.h库函数里边：
      - _crol_(a, b); 循环左移函数，a是要左移的值，b是左移的位数
      - _cror_(a, b); 循环右移函数，a是要右移的值，b是右移的位数
   - 实现代码
      - 方法1：
      ```
	#include<reg52.h>
	#define uint unsigned int
	#define A P0

	void delay(uint i){
		while(i--);
	}

	void main(){
		uint i;
		A=0x01; //是最低位的发光二极管亮
		delay(50000);
		while(1){
			for(i=0;i<8;i++){
				A=(0x01<<i); //0x01:0000 0001 左移一位得到 0000 0010
				delay(50000);
			}
		}
	}
	```
      - 方法2：
```
#include<reg52.h>
#define uint unsigned int //宏定义
sbit led1=P0^0;
sbit led2=P0^1;
sbit led3=P0^2;
sbit led4=P0^3;
sbit led5=P0^4;
sbit led6=P0^5;
sbit led7=P0^6;
sbit led8=P0^7;
//声明延时函数
void delayms(uint);
void main()
{
	while(1)
	{
		led1=1;
		delayms(500);
		led1=0;
		led2=1;
		delayms(500);
		led2=0;
		led3=1;
		delayms(500);
		led3=0;
		led4=1;
		delayms(500);
		led4=0;
		led5=1;
		delayms(500);
		led5=0;
		led6=1;
		delayms(500);
		led6=0;
		led7=1;
		delayms(500);
		led7=0;
		led8=1;
		delayms(500);
		led8=0;
	}
	}
	//延时函数
	void delayms(uint xms)
	{
		uint i,j;
		for(i=xms;i>0;i--)
			for(j=110;j>0;j--);
	}
   ```
## 蜂鸣器
   - 在单片机上大都是使用蜂鸣器来做提示或报警；
   - 改变单片机引脚输出波形的频率，就可以调整控制蜂鸣器音调，产生不同的音色和音调
   - 改变输出电平的高低电平占空比，则可控制蜂鸣器声音的大小
   ```
   	#include<reg52.h>
	#define uint unsigned int

	sbit beep=P1^5;
	//延时函数
	void delay(uint i){
		while(i--);
	}
	void main(){
		while(1){
			beep=1;
			delay(1000);  //延时时间越短输出的音频信号频率越高，音调越高
			beep=0;
			delay(1000);
		}	
	}
   ```

## 继电器（relay）
   - 继电器（英文名称：relay）是一种电控制器件，是当输入量（激励量）的变化达到规定要求时，在电气输出电路中使被控量发生预定的阶跃变化的一种电器。它**具有控制系统（又称输入回路）和被控制系统（又称输出回路）之间的互动关系**。通常应用于自动化的控制电路中，它**实际上是用小电流去控制大电流运作的一种“自动开关”**。故在电路中起着自动调节、安全保护、转换电路等作用。
   - 继电器内部含有线圈和触电，当线圈的电就会使触电吸合，从而使得开关连通
  ```
     #include<reg52.h>
     #define uint unsigned int
     sbit relay=P1^4;
     void main(){
	  relay=0;  //
	  while(1)
	  {	
	  }
     }
  ```

## 数码管
   - 一个数码管的引脚是10个，显示8字需要7小段，另外还有一个小数点，最后还有一个公共端，生产商为了封装统一，单位数码管都封装10个引脚，其中第3脚和第8脚是连接在一起的
   ![原理图](http://image.baidu.com/search/detail?ct=503316480&z=undefined&tn=baiduimagedetail&ipn=d&word=%E6%95%B0%E7%A0%81%E7%AE%A1&step_word=&ie=utf-8&in=&cl=2&lm=-1&st=undefined&hd=undefined&latest=undefined&copyright=undefined&cs=904804419,1892714089&os=220630412,2915171698&simid=3397265267,176940090&pn=17&rn=1&di=117590&ln=1327&fr=&fmq=1569128244820_R&fm=&ic=undefined&s=undefined&se=&sme=&tab=0&width=undefined&height=undefined&face=undefined&is=0,0&istype=0&ist=&jit=&bdtype=0&spn=0&pi=0&gsm=0&objurl=http%3A%2F%2Fgss0.baidu.com%2F-4o3dSag_xI4khGko9WTAnF6hhy%2Fzhidao%2Fwh%253D450%252C600%2Fsign%3D7f6d0b47b1119313c716f7b4500820e1%2Fb999a9014c086e0631bbface01087bf40ad1cb20.jpg&rpstart=0&rpnum=0&adpicid=0&force=undefined)
