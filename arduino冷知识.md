# Arduino冷知识1
使用伺服马达时，最好避开D9以及D10的PWM功能，analogWrite()，如果使用的是Mega板，在超过12个Servo的状况下，则需避开D11与D12的PWM
否则，PWM可能不会正常执行，这是由于Servo.h与D9,D10的PWM使用同一个Timer，如果同时使用这两个指令会互相干扰，造成PWM不能正常执行。
![alt text](<Arduino冷知識 (1).jpg>)

# Arduino冷知识2
使用tone()指令时，也不能使用D3与D11的PWM功能，ATmega328内部有三个计数计数器，timer0控制5，6的PWM讯号，以及delay(),millis(),micros()等指令。timer1控制9，10的PWM讯号，以及servo.h，timer2控制11，3的pwm信号，以及tone()指令。
使用servo跟tone时，已经占用两个计时器，所以同时使用喇叭跟伺服马达时，就只剩2颗呼吸灯使用了，另外MEGA板有多大6个计数器，分担许多PWM讯号
![alt text](<Arduino冷知識 (2).jpg>)

# Arduino冷知识3
Arduino不能使用tone()指令，让多个喇叭发出不同频率的声音。且不能够设定31hz以下的低频率，但可以设定高达65535Hz的高频率，而人耳约可达到20~20000hz左右的频率。
![alt text](<Arduino冷知識 (3).jpg>)

# Arduino冷知识4
想要暂停比delay()还短的时间，可以使用delayMicroseconds().
delay()的单位是毫秒，delayMicroseconds()的单位是微秒。
但使用时要注意以下两点
1。delayMicroseconds()的最大值不能超过16383，也就是延迟16.383毫秒，所以要延迟1000微秒，就直接使用delay()

2，并且在低于3时会有较大的误差，delayMicroseconds(1),实际会延迟1.5微妙
                              delayMicroseconds(2),实际会延迟2.125微妙

![alt text](<Arduino冷知識 (4).jpg>)

# Arduino冷知識5
在支持键盘指令的arduino，使用Keyboard.h头文件时，插上usb线，电脑会自动把Arduino当作USB键盘(或者鼠标)
所以可以写好键盘的脚本，朋友的电脑，然后就。。。。
或者可以自己制作游戏硬件外挂，因为电脑会判断你用真正的usb键盘鼠标，所以游戏服务器也不会阻挡
但官方也特别强调，在使用keyboard.h的指令时一定要特别小心，可能会让你的arduinio疯狂乱按一些按钮，甚至无法重新上传Arduino的代码。
![alt text](<Arduino冷知識 (5).jpg>)

# Arduino冷知识6
原厂的Arduino板排针母座印有编号，可以让你不再有插错引脚的困扰
![alt text](<Arduino冷知識 (6).jpg>)


# Arduino冷知识7
I2C是个超级好用的传输界面，许多模块也会使用I2C作为模块与Arduino沟通的桥梁，这种传输方式仅需要SDA SCL两个脚位，就可以让Arduino与多个模块沟通，在购买I2C模组时，可能部分模组不会附上I2C地址，此时可以通过Arduino的I2C扫描功能，来获取I2C地址。
![alt text](<Arduino冷知識 (7).jpg>)

# Arduino冷知识8
早期的Arduino只能控制两个伺服马达，现在最多可以控制12个伺服马达，由于Servo的信号需要约0.5~2.4毫秒的信号脉冲，Arduino要输出这种信号需要使用IC内部的计时计时器，所以早期的Arduino输出Servo指令是使用PWM讯号的脚位(只能使用D9,D10)，之后，才能像现在控制12颗伺服马达，但也因此，一片Arduino Uno就算高达20个I/0，但最多只能送12组Servo的脉冲信号，而Mega板则可最多控制48组伺服马达
![alt text](<Arduino冷知識 (8)_工作區域 1 複本 8.jpg>)

# Arduino冷知識9
Arduino Pro Mini是最小，最轻，最便宜的Arduino开发板，但是官网已经不卖了，Pro Mini减少了许多不必要的组件，也没有烧录芯片与USB接口，需要利用FTDI或是其他Arduino板才能烧录程序，没有排针母座，一切链接电路都需要焊接，但也因此，这款Pro Mini是功能最齐全且最轻，最小，最便宜的开发板
![alt text](<Arduino冷知識 (9)_工作區域 1 複本 9.jpg>)

# Arduino冷知識10
在Arduino 1.0.1之前的版本没有PinMode(pin,INPUT_PULLUP)，必须设定pinMode为INPUT，再将digitalWrite设定为HIGH来达成内部上拉电阻。
INPUT_PULLUP功能是使用AVR芯片内部的20k~150k上拉电阻，来达成侦测开关讯号的功能，这样就可以直接将开关链接Dpin与GND，不需在外部配置额外的电路，大大降低电路的复杂程序。
在早期的Arduino版本中，没有INPUT_PULLUP功能，必须这样写
~~~bash
    pinMode(pin, INPUT);
    digitalWrite(pin, HIGH);
~~~
不过Arduino在未设定的预设状态，会将所有的pinMode设定为INPUT，也就是说可以直接省去PinMode(pin,INPUT)这行指令。
![alt text](<Arduino冷知識_工作區域 1 複本 10.jpg>)

# Arduino冷知识11
一片Arduino最多只能控制1颗RGB LED灯，如果控制两颗，另一颗可能会有诡异的闪烁。
在Arduino UNO里面有3个timer，一个timer分别控制2个pin脚的PWM讯号，但其中的timer0因为同时处理missis()与delay()，所以输出的频率会跟其他的两个timer不同，也就是说，6个PWM当中有4个频率是490hz，2个是976hz
如果用PWM控制单颗LED还好，但控制RGB LED混色时，3种颜色会使用到不同频率的PWM，颜色会互相叠加，产生诡异的闪烁频率。由于UNO只有四个相同频率的PWM，所以做多只能控制1颗RGB LED灯了
![alt text](<Arduino冷知識_工作區域 1 複本 11.jpg>)

# Arduino冷知识12
UNO消息的A6,A7在哪里?副厂的UNO板可以找到
使用过MIN或是MICRO板，应该会发现命名ATmega328芯片，但是UNO的A6,A7却没有，这时由于芯片从TQFP32封装改为DIP28封装，芯片脚位少了4个，A6,A7被封印在里面了
自己设计PCB时，可以手动将这两个脚位焊接出来使用。
![alt text](<Arduino冷知識_工作區域 1 複本 12.jpg>)

# Arduino冷知识13
Arduino UNO控制LED灯时，不会让超过10颗同时发亮
如果使用的LED是同盟高亮度的LED，通常会消耗约20mA，一个Dpin可以提供最多40mA，所以链接高亮度当然没问题，但UNO板上所有引脚位加起来不能超过200mA，也就是说如果10颗LED同时发亮，可能将Arduino烧坏。
但可以用一些技巧的方式操控更多的LED，比如说轮流闪烁，或是超快速切换闪烁，让LED看起来都像是点亮一样
![alt text](<Arduino冷知識_工作區域 1 複本 4.jpg>)

