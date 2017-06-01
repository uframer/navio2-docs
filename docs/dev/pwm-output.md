## PWM输出

PWM 0-13 channels are available on 2.54mm header pins numbered 1-14 accordingly. Servos can be controlled by setting the correct frequency (50hz is a common frequency) and duty cycle corresponding to the length of a pulse (usually between 1 and 2 milliseconds).

To try to control servo connect the servo to the Navio’s output channel number 1 and run the provided example. Do not forget to supply power to the servo rail.

If you haven't already done that, download Navio2 drivers and examples code [here](navio-repository-cloning/).

### C++

Move to the folder with the source code, compile and run the example

```bash
cd C++/Examples/Servo
make
sudo ./Servo
```

### Python

Move to the folder with the code and run the example

```bash
cd Python
sudo python Servo.py
```

For further information see cource code. Note that ```set_period``` function sets the period for PWM_OUTPUT channel depending on frequency value,which passed as a second parameter to the function.  
To set the pulse range appropriate for your servo you can change the SERVO_MIN and SERVO_MAX values.
