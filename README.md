# Control your computer with hand gestures
![Hand Gesture workshop](https://github.com/ayeshaf9/hands_gesture/blob/master/ControlYourComputerWithHandGestures-5.jpg)
Control a laptop with hand gestures using python's PyAutoGUI library and ultrasonic sensors.

## Dependencies
1. Download the [Anaconda](https://www.anaconda.com/distribution/#download-section) SDK.
2. Download the [Arduino IDE](https://www.arduino.cc/en/main/software).
3. Download [VLC Player](https://www.videolan.org/vlc/) to demonstrate how hand gestures can be used for video controls.
4. 2 ultrasonic sensors
5. Arduino UNO
6. [PyAutoGUI](https://pyautogui.readthedocs.io/en/latest/keyboard.html) library for python

## What is PyAutoGUI?
PyAutoGUI is a cross-platform GUI automation Python module for human beings. Used to programmatically control the mouse & keyboard.

## Setup

Connect the ultrasonic sensors to the Arduino board as shown.

![Hardware Setup](https://github.com/ayeshaf9/hands_gesture/blob/master/hardwaresetup.png)

## Steps

1. Launch the [Arduino IDE](https://www.arduino.cc/en/main/software).

2. Create a new file.

3. Declare the pins of the ultrasonic sensors:

```C++
const int trigger1 = 2; //Trigger pin of 1st Sensor
const int echo1 = 3; //Echo pin of 1st Sensor
const int trigger2 = 4; //Trigger pin of 2nd Sensor
const int echo2 = 5;//Echo pin of 2nd Sensor
```

4. Initialize the pins:

```C++
void setup() {
Serial.begin(9600); 
  
pinMode(trigger1, OUTPUT);  
pinMode(echo1, INPUT); 
pinMode(trigger2, OUTPUT); 
pinMode(echo2, INPUT);           }
```

5. Add the function to calculate distance between the sensors and hands:

```C++
void calculate_distance(int trigger, int echo)
{
digitalWrite(trigger, LOW);
delayMicroseconds(2);
digitalWrite(trigger, HIGH);
delayMicroseconds(10);
digitalWrite(trigger, LOW);

time_taken = pulseIn(echo, HIGH);
dist= time_taken*40.03/2;
if (dist>50)
dist = 50;
}
```

6. Main loop for sensing hand gestures:

```C++
void loop() { calculate_distance(trigger1,echo1);
distL =dist; //get distance of left sensor

calculate_distance(trigger2,echo2);
distR =dist; //get distance of right sensor

delay(200);
}
```

7. Define the distances between sensors and hands to assign functionality:

```C++
//Pause Modes -Hold
if ((distL >40 && distR>40) && (distL <50 && distR<50)) //Detect both hands
{Serial.println("Play/Pause"); delay (500);}

if ((distL >0 && distR>0) && (distL <5 && distR<5)) //Detect both hands
{Serial.println ("MUTE"); delay ( 500);}

calculate_distance(trigger1,echo1);
distL =dist;

calculate_distance(trigger2,echo2);
distR =dist;
```

8. Detect a single hand **(left)** within a specific distance range:

```C++
//Control Modes
//Lock Left - Control Mode
if (distL>=13 && distL<=17)
{
  delay(100); //Hand Hold Time
  calculate_distance(trigger1,echo1);
  distL =dist;
  if (distL>=13 && distL<=17)
  {
    Serial.println("Left Locked");
    while(distL<=40)
    {
      calculate_distance(trigger1,echo1);
      distL =dist;
      if (distL<10) //Hand pushed in 
      {Serial.println ("Vdown"); delay (500);}
      if (distL>20) //Hand pulled out
      {Serial.println ("Vup"); delay (500);}
    }
  }
}
```

9. Detect a single hand **(right)** within a specific distance range:

```C++
//Lock Right - Control Mode
if (distR>=13 && distR<=17)
{
  delay(100); //Hand Hold Time
  calculate_distance(trigger2,echo2);               
  distR =dist; 
  if (distR>=13 && distR<=17)
  {
    Serial.println("Right Locked");  
    while(distR<=40)
    {
      calculate_distance(trigger2,echo2);
      distR =dist;
      if (distR<10) //Right hand pushed in
      {Serial.println ("Rewind"); delay (300);}
      if (distR>20) //Right hand pulled out
      {Serial.println ("Forward"); delay (300);}
  }
}
}
```

10. Install the **PyAutoGUI** module:
 
**For Windows**:
Open Anaconda command prompt and enter `python -m pip install pyautogui`

**For Mac**:
Launch Anoconda and execute the following code in the terminal:
- `pip install --upgrade pip`
- `conda install -c conda-forge pyautoguipip`
- `install pyserial`

To find the port addresses on **Mac**, use this command in terminal:
`python -m serial.tools.list_ports`

11. Download pyserial library in Anaconda.

12. Launch Spyder (Python 3.7).

13. Create a new **.py** file.

14. Import the following libraries:
- `import serial`
- `import time`
- `import pyautogui`

15. Create a Serial port object for Arduino connection: 
`ArduinoSerial = serial.Serial (‘USB PORT’,9600)`

16. Main loop for reading and writing instructions to and from the Arduino and your computer using “pyautogui”:

```python
while 1:
    incoming = str (ArduinoSerial.readline()) 
    print (incoming)
    
    if 'Play/Pause' in incoming:
        pyautogui.typewrite(['space'],  0.2)

    if 'Vup' in incoming: 
        pyautogui.hotkey('ctrl', 'up')
 
    if 'Vdown' in incoming: 
        pyautogui.hotkey('ctrl',' down ' )
        
incoming   = "";
```

17. Run the python file.

18. Play a video using the **VLC Player**.

19. Test hand gesture control.

## Credits

Aswinth Raj on [circuitdigest](https://circuitdigest.com/microcontroller-projects/control-your-computer-with-hand-gestures).
