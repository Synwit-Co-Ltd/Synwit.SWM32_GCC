# Synwit.SWM32_GCC
GCC support pack for Synwit MCU.

## GCC编译使用说明（以SWM320为例）：
1. 将SWM320目录下的Makefile和swm320.ld拷贝到SWM320_StdPeriph_Driver目录下
2. 添加printf需要的_write函数实现
3. 执行make clean
4. 执行make

_write 实现示例：
``` c
int _write(int fd, char *ptr, int len)
{
	int i;
	for(i = 0; i < len; i++)
	{
		UART_WriteByte(UART0, *ptr++);
	
		while(UART_IsTXBusy(UART0));
	}
 	
	return len;
}
```

## 如何编译其他工程（以ADC\SimplADC0为例）？
1. 将ADC\SimplADC0工程下的main.c拷贝到SWM320_StdPeriph_Driver\APP目录下
2. 执行make clean
3. 执行make
