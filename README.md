# ECE528_HW2
Repository for ECE 528 Homework Assignment 2

# Question 1:
Hardware timers are timer peripherals built onto a microcontroller such as the MSP432. They can perform precise timing events, generate periodic interrupts and create PWM signals. For example, we can have a timer that starts from 0 to a time set by the user, or vice versa, even a timer that will count both up and then down in one instance. These are more accurate then a software timer which isn't always consistent.

# Question 2:
The clock signal can be thought of as the signal that synchronizes everything together to perform efficient and effective operation of the system. It synchronizes processors, peripherals, and other parts of the microcontroller. If the clock is not stable, you can expect all operation to cease or heavily flawed.

The MSP432P401R has the Auxiliary Clock (ACLK) with max freq. of 128 kHz, Master Clock (MCLK) which is used by the CPU and peripheral module interfaces, the Subsystem Master Clock (HSMCLK) is a clock used when the CPU and peripherals need to run at a faster speed, Low-Speed Subsystem Master Clock (SMCLK) uses the same resources as the HSMCLK but as in its name is for low speed operations as it's half the rated freq. of the HMSCLK, and lastly the Low-Speed Backup Domain Clock (BCLK) is used for low power and non-volatile operations like backup RAM, real-time clocks, etc.

# Question 3:
0x0000 to TAxCCR0, the value that is set when the period must be different than 0xFFFF which for a 16 bit timer would be the ultimate maximum. Assuming the maximum is 0xFFFF then we have 2^16 = 65536 - 1 = 65535.

# Question 4:
24 bit: 0x000000; freq. 10 MHz = 10,000,000 Hz; 
Overflow Period: 2^24 = 16,777,216 / 10,000,000 = Overflow = 1.68 seconds

# Question 5:
The SysTick timer is a 24 bit timer on the MSP microcontroller, it always counts down from a specified value by us the user to zero. When this happens it will "reload" while also generating an interrupt each time it does. One clear example is a system that needs to run scheduled tasks, but another could be activating a sensor like a DHT22 which measures humidity and temperature for reading. 

The clock sources used by it seems to be the core clock which is also described as the free running clock FCLK. You select it by setting bit 2 labeled CLKSOURCE in the STCSR Register.

# Question 6:
It has 4 registers. STCSR for control and status of the systick timer, STRVR which is the reload value register that we set, STCVR which is the current value register which we can read from or you can write to it which then clears the register to 0, and lastly the STCR register that enables software to scale to any required speed with dividing and multiplying.

# Question 7:
Timer_A is a 16 bit timer in which in its name a timer but its advantage is having different operating modes that count in different ways. The 4 main operating modes are (00) STOP, timer is halted, (01) UP, timer repeatedly counts from zero to TAxCCR0, (10) Continuous, timer repeatedly counts from zero to 0xFFFF, and (11) UP/DOWN, timer repeatedly counts from zero up to the value of TAxCCR0 then back down to zero.

# Question 8:
1) TAxCTL: Control register for clock, input divider, mode control, etc. 
2) TAxCCTLn: Capture/Compare control registers for selecting capture/compare and operating with them.
3) TAxR: Counter Register, the count of Timer_A
4) TAxCCRn: Capture/compare register that holds the data for comparison to TAxR or while in capture mode, TAxR is copied into TAxCCRn.
5) TaxIV: Interrupt Vector register that displays interrupt sources from any of the TAxCCRn registers
6) TAxEX0: Expansion register that can further divide the timer beyond what has already been divided.

# Question 9: 
PWM or Pulse Width Modulation is based off a duty cycle that dictates how much a digital signal is ON and OFF in a cycle. For example a 50% duty cycle will have an equal ON and OFF sequence in one cycle. 

One example we've already been using it for is DC motor speed. Another would be the servo position which is described by the width of a control pulse. Lastly, a light source or LED source, if you would want to make it dimmable with different settings. 

# Question 10: 
There are 4 modules TA0, TA1, TA2, and TA3 with each providing 7 capture/compare registers CCR0 to CCR7. Each timer can implement 4 outputs however and since there are 4 timers then 16 outputs in total.

# Question 11: 
P2SEL0 |= 0x10;
P2SEL1 &= ~0x10;
P2DIR  |= 0x10;
TIMER_A0->CCR[0] = 60000; // (0.020 * 6,000,000)/2
TIMER_A0->CCR[1] = 30000; //50% duty cycle
TIMER_A0->EX0 = 0;
TIMER_A0->CTL |= 0x0270; //SMCLK, Div by 2, UP/DOWN mode
TIMER_A0->CCTL[1] |= 0x0040;

# Question 12:
TIMER_A0->CTL &= ~0x0030;
TIMER_A0->CTL |= 0x0200;
TIMER_A0->EX0 = 0x0000;
TIMER_A0->CCR[0] = 23999;
TIMER_A0->CCTL[0] = 0x0010;
NVIC->IP[2] = (NVIC->IP[2] & 0xFFFFF00F) | 0x00000060;
NVIC->ISER[0] |= 0x00000100;
TIMER_A0->CTL |= 0x0014;