# ECE 528/L - Robotics and Embedded Systems with Lab
**CSU Northridge**

**Department of Electrical and Computer Engineering**

## Motor Control Lab
### Thomas Anderson / Abraham Santiago
---
<details><summary>The Motor Control lab interfaces with the following:</summary>

* User LEDs of the TI MSP432 LaunchPad
* Pololu Gearmotor with Encoder - [Product Link](https://www.pololu.com/product/3675)
* Left Bumper Switches for TI-RSLK MAX - [Product Link](https://www.pololu.com/product/3673)
* Right Bumper Switches for TI-RSLK MAX - [Product Link](https://www.pololu.com/product/3674)
* HS-485HB Servo-Stock Rotation - [Product Link](https://www.servocity.com/hs-485hb-servo/)
</details>

<details>
<summary><h2>Overview</h2></summary>

This lab serves as an introduction into using Pulse Width Modulation (<b>PWM</b>) and using it to control a motor.    
Additionally, it introduces timers using SysTick as a method to control duty cycle. While using what we learned from the GPIO lab to configure the GPIO pins.
</details>

<details>
<summary><h2>Components</h2></summary>
  
<b>Description</b> | <b>Quantity</b> | <b>Manufacture</b>
--- | --- | ---
MSP432 LaunchPad | 1 | Texas Instruments
USB-A to Micro-USB Cable | 1 | N/A
TI-RSLK MAX Chassis | 1 | Pololu
Bumper Switches | 2 | Pololu
HS-485HB Servo | 2 | ServoCity
Oscilloscope | 1 | N/A
Oscilloscope Probes | 2 | N/A
</details>


<details>
<summary><h2>Theory</h2></summary>

<b>Key Concepts</b>
---

<details>
<summary><b>SysTick and Timer_A</b></summary>

SysTick is a system which is integrated into the Cortex-M4 processor and counts down from a value defined by the program before reloading the set value and counting again down. The count is typically decremented every clock cycle and can send an interrupt when it transitions from 1 to 0, acting as a 24 bit timer.

Timer_A is a peripheral for the controller, providing a 16 bit timer similar to SysTick but a different structure and extra functionality including PWM outputs with its compare mode, the ability to count up or down, and selectable and configurable clock source, and track when in the timer's count some external signal changes.

</details>

<details>
<summary><b>Pulse Width Modulation</b></summary>

Pulse-width modulation (PWM) regulates average delivered power by switching a digital signal between on and off states. The output power is directly proportional to its duty cycle—the fraction of each period the signal remains high. The duty cycle can also be used to control the state of systems such as motors.

</details>

<details>
<summary><b>Switch Bouncing</b></summary>

In this lab the sensors for the impacts with the wall exhibit switch bouncing, which is a rapid toggling between high and low in the switch output due to the physical connection. This can cause the software triggered by the switch to be activated multiple times in quick succession. The problem is often fixed by including a small cooldown between triggers of the switch in the software.

</details>

</details>

<details>
<summary><h2>Analysis and Results</h2></summary>

<b>Pre-Task</b>
---
1. Copy repository using Git Bash
2. Connect the Oscilloscope wires to the MSP432 LaunchPad to be able to read the following signals for specific pins as shown in the table below. We were also given one servo which we connected to a breadboard then followed the second table to connect it to the MSP432 LaunchPad.
<details>
<summary><h2>Connections</h2></summary>

<b>Oscilloscope</b> | <b>MSP432 LaunchPad Pin</b> 
--- | ---  
CH 1 Probe | P5.6 (PM_TA2.1)
CH 2 Probe | P5.7 (PM_TA2.1)
GND |GND

<b>HS-485HB</b> | <b>MSP432 LaunchPad Pin</b> 
--- | --- 
Servo 1 PWM Input Signal (Yellow Wire) | P5.6(PM_TA2.1)
Servo 1 VCC (Red Wire) | 5V
Servo 1 GND (Black Wire) | GND
</details>

3. Then we uncommented some lines of code in the main.c file under the while loop, that will allow the servo we connected to rotate. Connected the MSP432 LaunchPad to the computer and build then flash the program
4. Verified that the current build works by observing the signals generated with varying duty cycles on the oscilloscope. We obtained 4 photos: a servo at 0 degrees, at 0 degrees with pulse width between 540-580us, servo at 180 degrees, and at 180 degrees with pulse width between 2320-2360us. The commented the lines we previously uncommented

<b>Tasks</b>
---
1. In the Bumper_Switches.c file we implemented the Bumper_Switches_Init function to configure pins P4.7, P4.6, P4.5, P4.3, P4.2, and P4.0 as GPIO pins then setting them as inputs, enabling them as pull-up resistors, having them to use falling edge triggers, clearing any previous interrupts then setting them as interrupts. The hexadecimal value for the pins was 0xED.

2.  After implementing the Bumper_Switches_Init function, we modified the Bumper_Switches_Hander in the main.c file which functions by setting P8.7 pin to high whenever any of the bumper switches are pressed. Which then prints out a collision detected text in the terminal which allows us to verify that the bumper that we pressed is the one being detected in the code. Then we took a screenshot of the terminal.

3. We connected the oscilloscope probes again to the MSP432 LaunchPad but on different pins as seen in the table below. The reason is because we are observing the bumper switch when it is pressed then how long it takes for P8.7 to toggle. To make sure it triggers correctly we set the trigger menu on the oscilloscope to be on CH1 with an Edge type trigger and Falling Edge slope and a Normal sweep. Then after setting it up we observed the oscilloscope. Ch 1 had many highs and lows when the bumper switch was pressed, this being due to the buttons not having a debounce filter. We also observed that Ch 2 took awhile  to toggle because it was printing on the terminal.

<details>
<summary><h2>Connections</h2></summary>

<b>Oscilloscope</b> | <b>MSP432 LaunchPad Pin</b> 
--- | --- 
CH 1 Probe | P4.0 (BMP0)
CH 2 Probe | P8.7 (Back Right Red LED)
GND | GND
</details>

4. In the Timer_A0_PWM.c file under the Timer_A0_PWM_Init we configured pins P2.6 and P2.7 to use peripheral function modes, then set them as output GPIO pins to drive the PWM signals. We set the Timer A0 CCR to a specified period constant, then cleared the Timer A0 EX0. And also set a new duty cycle for the PWM signal.

5. In Motor.c file under Motor_Init we configured pins P5.4 and P5.5 which were 0x30 in hexadecimal and set the pins as output GPIO pins then initializing the pins to zero. Then also configured pins P3.6, and P3.7 which were 0xC0 in hexadecimal and set the pins as output GPIO pins then initializing the pins to zero. Then moved on to implement the Motor_Left function. By using the OUT register P5 to set bit 4 and clearing bit 5, The opposite for Motor_Right we cleared bit 4 and set bit 5.

6. Now in the Main.c file under while we uncommented the Drive_Pattern1(); which allowed us to see if our code functioned as intended. It tested all the functions without any issues allowing it to move forward, move left, move right, and move backwards.

7. Next we implemented the Handle_Collision function. We implemented it by telling what the function should do when it detects a collision. It will first go backwards then move right then clear the collision detected global flag. We were able to observe this when building it and running the program on Launchpad. 

<b>These steps were demonstrated in lab.</b>
</details>

<details>
<summary><h2>Know Issues or Limitations</h2></summary>

We gained a thorough understanding of all the concepts in the lab and successfully demonstrated the entire lab. Known issues were the bumper switches because they did not have a debouncer filter but it did not seem to have any noticeable effects when handling the collision only seemed noticeable when viewing it on the oscilloscope.
<b>This lab was a complete success.</b>
</details>

<details>
<summary><h2>Author Contribution</h2></summary>

We both completed every component of the lab and worked on the report together.    
</details>

<details>
<summary><h2>References</h2></summary>

[MSP432P401R SimpleLink Microcontroller LaunchPad Development Kit User's Guide](https://docs.rs-online.com/3934/A700000006811369.pdf)

[MSP432P401R Datasheet](https://www.ti.com/lit/ds/slas826e/slas826e.pdf)

[Robot Systems Learning Kit (TI-RSLK) User Guide](https://www.ti.com/lit/pdf/sekp166)

[MSP432P4xx SimpleLink Microcontrollers Technical Reference Manual](https://web.archive.org/web/20200402132841/http:/www.ti.com/lit/ug/slau356i/slau356i.pdf)

[Left Bumper Switch for TI-RSLK MAX](https://www.pololu.com/product/3673)

[Right Bumper Switch for TI-RSLK MAX](https://www.pololu.com/product/3674)

[TI DRV8838 Motor Driver Datasheet](https://www.ti.com/lit/gpn/drv8838)

[HS-485HB Servo-Stock Rotation Datasheet](https://www.servocity.com/hs-485hb-servo/)
</details>



