In this task, we will see how to achieve micro-stepping in a stepper motor using a PS2 controller and Arduino.
The first approach we take is to figure out how a stepper motor works. The working of a stepper motor is pretty basic. Once you understand how it works, the connections and the codes seem a lot simpler. You can find all the information you need about a stepper motor on the internet. 

STEPPER MOTOR WORKING AND PRINCIPLES:

Stepper motor is a brushless, DC electric motor that divides a full rotation into several equal steps. The motor's position can then be commanded to move and hold at one of these steps without any position sensor for feedback (an open-loop controller), as long as the motor is carefully sized to the application in respect to torque and speed. 
Stepper motors work on the principle of electromagnetism. There is a soft iron or magnetic rotor shaft surrounded by the electromagnetic stators. The rotor and stator have poles which may be teethed or not depending upon the type of stepper. When the stators are energized the rotor moves to align itself along with the stator (in case of a permanent magnet type stepper) or moves to have a minimum gap with the stator (in case of a variable reluctance stepper). This way the stators are energized in a sequence to rotate the stepper motor. Get more information about working of stepper motors through interesting images at the stepper motor Insight.



Next, we select a suitable motor driver for our stepper motor. The motor drivers act as an interface between the motors and the control circuits. Motors require high amount of current whereas the controller circuit works on low current signals. So, the function of motor drivers is to take a low-current control signal and then turn it into a higher-current signal that can drive a motor. Various motor drivers have different modes and mode-set-ups for micro-stepping. 

We will be using the A4988 motor driver for our NEMA 17 stepper motor. We chose this specific model for our task as required. This particular model of the motor driver supports five different step revolutions; namely full-step, half-step, quarter-step, eighth-step and sixteenth-step.

Then, we move on to coding. The code carries all the commands given to the Arduino to rotate the motor at a particular angle as per our requirements whilst being able to switch between the various modes. The sixteenth-step is set as default while we can switch back and forth between the other four modes with the press of a button. 

CODE:

        #include <PS2X_lib.h>  //for v1.6
        //defining the diffent ps2 pins and the pins numbers of the mega board which are going to be connected respectively
        #define PS2_DAT        53
        #define PS2_CMD        51
        #define PS2_SEL        49
        #define PS2_CLK        47
        #define pressures   false
        #define rumble      false

        PS2X ps2x;                                                //name of the controller we are using
        int error = 0;
        byte type = 0;
        byte vibrate = 0;
        const int Step = 12;                                      //defining the step pin of the stepper motor
        const int dir = 10;                                       //defining the direction pin of the stepper motor

        // ms1,ms2,ms3 are the variables that changes the modes of the micro-stepping
        const int ms1 = 8;
        const int ms2 = 7;
        const int ms3 = 2;

        int  a[16] = {707, 757, 736, 718, 706, 701, 703, 714, 728, 746, 769, 787, 728, 803, 798, 784};                   //array of 16 elements consisting of step counts in the sixteeth-step mode

        int b[16], c[16], e[16], f[16];                                                                                  //defining the new arrays after the change in modes

        //defining other flags and variables used in the code below
        int x = 0;
        int p = 0, q = 0, r = 0, s = 0, t = 0;
        int d = 0, g = 0, o = 0;
        int i = 0, j = 0, k = 0, l = 0;

        int cstep = 0;                                           //defining the current step at which the execution is carried
        int pstep = 0;                                           //defining the previous step from which the motor needs to rotate
        int diff = 0;                                            //difference between the current step and the previous step



        void setup()
        {
          //Setting the modes of the pins that are connected to the Arduino
          pinMode(dir, OUTPUT);
          pinMode(Step, OUTPUT);
          pinMode(ms1, OUTPUT);
          pinMode(ms2, OUTPUT);
          pinMode(ms3, OUTPUT);

          Serial.begin(9600);
          Serial.print(i);
          error = ps2x.config_gamepad(47, 51, 49, 53, pressures, rumble);
        }

        void loop()
        {
          if (error == 0)
          {
            ps2x.read_gamepad(false, vibrate);

            /*----------------------------------------------------------------------------------------*/

            d += (ps2x.ButtonPressed(PSB_CROSS));                                         //the code to be executed when the cross button is pressed on the PS2

            if (d % 5 == 1)                                                               //the code to be executed when the cross button is pressed once
            {
              //the logics of the pins for the mode to be set on full-step
              digitalWrite(ms1, LOW);
              digitalWrite(ms2, LOW);
              digitalWrite(ms3, LOW);

              int  a[16] = {707, 757, 736, 718, 706, 701, 703, 714, 728, 746, 769, 787, 728, 803, 798, 784};

              //changing the elements of the array from the sixteeth-step mode to full-step mode for the same angles

              if (p == 0)
              {
                Serial.println("mode full step");
                for (o = 0; o < 16; o++)
                {
                  f[o] = (int)a[o] / 16;                           //creating a new array with the steps needed for the full-step mode
                  Serial.println(f[o]);

                }


                p = 1;
              }
            }
            if (d % 5 == 2)
              p = 0;

            /*----------------------------------------------------------------------------------------*/

            if (d % 5 == 2 )                                                               //the code to be executed when the cross button is pressed twice
            {
              int  a[16] = {707, 757, 736, 718, 706, 701, 703, 714, 728, 746, 769, 787, 728, 803, 798, 784};

              //the logics of the pins for the mode to be set on half-step
              digitalWrite(ms1, HIGH);
              digitalWrite(ms2, LOW);
              digitalWrite(ms3, LOW);

              //changing the elements of the array from the sixteeth-step mode to half-step mode for the same angles

              if (q == 0)
              {
                Serial.println("mode half step");
                for (k = 0; k < 16; k++)
                {
                  b[k] = a[k] / 8;                           //creating a new array with the steps needed for the half-step mode
                  Serial.println(b[k]);
                }


                q = 1;
              }
            }
            if (d % 5 == 3)
              q = 0;


            /*----------------------------------------------------------------------------------------*/
            if (d % 5 == 3)                                                               //the code to be executed when the cross button is pressed thrice

            {
              int  a[16] = {707, 757, 736, 718, 706, 701, 703, 714, 728, 746, 769, 787, 728, 803, 798, 784};

              //the logics of the pins for the mode to be set on quarter-step
              digitalWrite(ms1, LOW);
              digitalWrite(ms2, HIGH);
              digitalWrite(ms3, LOW);

              //changing the elements of the array from the sixteeth-step mode to quarter-step mode for the same angles

              if (r == 0)
              {
                Serial.println("mode quarter step");
                for (l = 0; l < 16; l++)
                {
                  c[l] = (a[l] / 4);                           //creating a new array with the steps needed for the quarter-step mode
                  Serial.println(c[l]);
                }


                r = 1;
              }
            }
            if (d % 5 == 4)
              r = 0;
            /*----------------------------------------------------------------------------------------*/

            if (d % 5 == 4)                                                               //the code to be executed when the cross button is pressed four times
            {
              int  a[16] = {707, 757, 736, 718, 706, 701, 703, 714, 728, 746, 769, 787, 728, 803, 798, 784};

              //the logics of the pins for the mode to be set on eighth-setp
              digitalWrite(ms1, HIGH);
              digitalWrite(ms2, HIGH);
              digitalWrite(ms3, LOW);

              //changing the elements of the array from the sixteeth-step mode to eighth-step mode for the same angles

              if (s == 0)
              {
                Serial.println("mode eighth step");
                for (j = 0; j < 16; j++)
                {
                  e[j] = (a[j] / 2);                           //creating a new array with the steps needed for the eighth-step mode
                  Serial.println(e[j]);
                }


                s = 1;
              }
            }
            if (d % 5 == 0)
              s = 0;

            /*----------------------------------------------------------------------------------------*/
            if (d % 5 == 0)                                                               //the code to be executed when the cross button is pressed five times

            {
              int  a[16] = {707, 757, 736, 718, 706, 701, 703, 714, 728, 746, 769, 787, 728, 803, 798, 784};

              //the logics of the pins for the mode to be set on sixteeth-step
              digitalWrite(ms1, HIGH);
              digitalWrite(ms2, HIGH);
              digitalWrite(ms3, HIGH);

              //changing the elements of the array from the sixteeth-step mode to sixteenth-step mode for the same angles

              if (t == 0)
              {
                Serial.println("mode sixteenth step");
                for ( g = 0; g < 16; g++)
                {
                  a[g] = a[g];                           //keeping the same array for the sixteenth-step mode
                  Serial.println(a[g]);
                }


                t = 1;
              }
            }
            if (d % 5 == 1)
              t = 0;

            /*----------------------------------------------------------------------------------------*/
            if (ps2x.ButtonPressed(PSB_CIRCLE))                                              //the code to be executed when the circle button of the PS2 is pressed
            {
              //to increment the value of the index of the array when the button is pressed

              Serial.println(i);

              if (i > 14)                          //to get the first index after the fifteenth index is incremented
              {
                i = -1;
              }
              i++;

              Serial.println("inc    ");
              Serial.println(i);

            }

            if (ps2x.ButtonPressed(PSB_SQUARE))                                              //the code to be executed when the square button of the PS2 is pressed
            {
              //to decrement the value of the index of the array when the button is pressed
              Serial.println(i);

              if (i < 1)                           //to get the last index after the first index is decremented
              {
                i = 16;
              }
              i--;

              Serial.println("dec    ");
              Serial.println(i);
            }

            /*----------------------------------------------------------------------------------------*/

            if (ps2x.ButtonPressed(PSB_TRIANGLE))                                             //the code to be executed when the triangle button of the PS2 is pressed
            {
              if (d % 5 == 0)                             //the array to be chosen while executing the sixteeth-step mode
                cstep = a[i];

              if (d % 5 == 1)
                cstep = f[i];                             //the array to be chosen while executing the full-step mode

              if (d % 5 == 2)
                cstep = b[i];                             //the array to be chosen while executing the half-step mode

              if (d % 5 == 3)
                cstep = c[i];                             //the array to be chosen while executing the quarter-step mode

              if (d % 5 == 4)
                cstep = e[i];                             //the array to be chosen while executing the eighth-step mode

              diff = cstep - pstep;                       //the difference between the current step and the previous step gives the number of steps the motor has to rotate through
              Serial.println(diff);


              if (diff > 0)                               //depending on the difference, the motor has to be rotated in the clockwise and counter clockwise direction
              {
                digitalWrite(dir, LOW);                   //to rotate the motor in coounter clockwise direction, the direction pin is set as LOW
                for (int x = 0 ; x <= diff ; x++ )
                {
                  //setting the step pin as HIGH and then LOW gives you a pulse
                  digitalWrite(Step, HIGH);
                  delay(1);
                  digitalWrite(Step, LOW);
                  delay(1);   // STEP LOWs
                }
              }
              else if (diff <= 0)
              {
                digitalWrite(dir, HIGH);                   //to rotate the motor in clockwise direction, the direction pin is set as HIGH
                for (int x = 0 ; x <= -diff ; x++ )
                {
                  //setting the step pin as HIGH and then LOW gives you a pulse
                  digitalWrite(Step, HIGH);
                  delay(1);
                  digitalWrite(Step, LOW);
                  delay(1);
                }
              }
            }
            pstep = cstep;                                 //the previous step now becomes the current step and the code is ready to run again!!
          }
        }


Next, we start connecting all of our components (the stepper motor, the motor driver, the Arduino board and the PS2 controller) with the help of wires and boards. 

HARDWARE CONNECTIONS - The a4988 driver has 16 pins.

![A4988-Stepper-Motor-Driver-Pinout](https://user-images.githubusercontent.com/79084266/107937252-b0b8f680-6fa9-11eb-9e66-a0867b6ecb8b.png)

· VMOT AND GND are the power pins for external power supply.

· 1A, 1B, 2A and 2B are the pins connected to the stepper motor. 1A and 1B pins are connected to the pair of coils which are shorted internally in the stepper motor.

· VDD and GND pins are connected to the Arduino’s VCC 5V pin and GND pin.

· Enable key is connected to PWM pin.

· MS1 MS2 MS3 are micro-stepping pins and set low or high based on the stepping mode.

· RESET AND SLEEP pins are externally shorted with jumper wire.

· Step pin is connected to Arduino PWM third pin.

· DIR pin is connected to Arduino PWM fourth pin. It controls the direction of the stepper motor. if low forward direction and if high backward direction.
