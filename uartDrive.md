## nanoMecanumDrive.ino


```c++

#include <Microduino_MicroRobot.h>
#include <math.h>

MicroRobot microRobot;

const static uint8_t forwardLeft=2;
const static uint8_t backLeft=3;
const static uint8_t backRight=4;
const static uint8_t forwardRight=1;

float minRange=-85;
float MaxRange=85;

String getData;

void setup()
{
  Serial.begin(115200);
  microRobot.begin();
  for (int a = 1; a <= 4; a++) {
    microRobot.motorInit(a);
  }
  for (int a = 1; a <= 4; a++) {
    microRobot.setMode(a, MODE_OPEN);
    microRobot.setSpeed(a, 0);

    // microRobot.setMode(a, MODE_SPEED);
    // microRobot.setRatio(a, 60);
    // microRobot.setResolution(a, 12);
    // microRobot.setSpeed(a, 0);
  }
}


void StopCar(bool isFree) {
  for (int a = 1; a <= 4; a++) {
    if(isFree) {
      microRobot.setSpeed(a, FREE);
    } else {
      microRobot.setSpeed(a, BRAKE);
    }
  }
}


void controlCar(float joyStickForBack, float joyStickLeftRight, float joyStickRot) {

  float forwBackVel=minRange+joyStickForBack*0.001*(MaxRange-minRange);
  float leftRightVel=minRange+joyStickLeftRight*0.001*(MaxRange-minRange);
  float rotVel=minRange+joyStickRot*0.001*(MaxRange-minRange);

  float forwardLeftSpeed=(-forwBackVel-leftRightVel-rotVel);
  float backLeftSpeed=(-forwBackVel+leftRightVel-rotVel);
  float backRightSpeed=(-forwBackVel-leftRightVel+rotVel);
  float forwardRightSpeed=(-forwBackVel+leftRightVel+rotVel);

  forwardLeftSpeed=-forwardLeftSpeed;
  backLeftSpeed=-backLeftSpeed;
  backRightSpeed=backRightSpeed;
  forwardRightSpeed=forwardRightSpeed;

  microRobot.setSpeed(forwardLeft, forwardLeftSpeed);
  microRobot.setSpeed(backLeft, backLeftSpeed);
  microRobot.setSpeed(backRight, backRightSpeed);
  microRobot.setSpeed(forwardRight, forwardRightSpeed);

  // Serial.print("fL:\t");
  // Serial.print(forwardLeftSpeed);
  // Serial.print(" ");
  // Serial.print("bL:\t");
  // Serial.print(backLeftSpeed);
  // Serial.print(" ");
  // Serial.print("bR:\t");
  // Serial.print(backRightSpeed);
  // Serial.print(" ");
  // Serial.print("fR:\t");
  // Serial.println(forwardRightSpeed);

}


void loop()
{

  if(Serial.available() > 0) {
    getData = Serial.readStringUntil('\n');
    getData.trim();
    if(getData.startsWith("D:")) {
      uint8_t dataLen=getData.length();
      // Serial.println(getData);
      // Serial.println(dataLen);
      uint8_t index=getData.indexOf(",");
      uint8_t index2=getData.indexOf(",", index+1);
      float joyStickRot=getData.substring(2,index).toInt();
      float joyStickForBack=getData.substring(index+1,index2).toInt();
      float joyStickLeftRight=getData.substring(index2+1,dataLen).toInt();

      controlCar(joyStickForBack, joyStickLeftRight, joyStickRot);
    }
  }
}
```


## bigFootDrive.ino

```c++
#include <Microduino_MicroRobot.h>

MicroRobot microRobot;

// bool connctOK=false;
// bool portOK=false;
// bool serverOK=false;

static const uint8_t servoIndex=0;
static const uint8_t motorIndex=4;

// bigFootESC
static const uint8_t servoMiddle=90;
static const uint8_t servoRightDegree=40;
static const uint8_t servoLeftDegree=140;

uint16_t lastSpeedVal=1500;

// #define portStatePin D4
// #define serverStatePin D5

String getData;

// the setup routine runs once when you press reset:
void setup() {

  //light state init
  // pinMode(portStatePin,OUTPUT);
  // digitalWrite(portStatePin,LOW);
  // pinMode(serverStatePin,OUTPUT);
  // digitalWrite(serverStatePin,LOW);

  // draw("Initial...");
  microRobot.begin();
  Serial.begin(115200);
  microRobot.servoWriteRaw(motorIndex, 1500);
  delay(1000);

  //servo init
  microRobot.digitalWrite(servoIndex, LOW);
  microRobot.servoWrite(servoIndex, servoMiddle);

}

// 获取速度，角度数据并控制电机舵机
void loop() {

  if(Serial.available() > 0) {
    getData =Serial.readStringUntil('\n');
    getData.trim();
    if(getData.startsWith("D:")) {
      uint8_t dataLen=getData.length();
      // Serial.println(getData);
      // Serial.println(dataLen);

      uint8_t index=getData.indexOf(",");
      uint8_t index2=getData.indexOf(",", index+1);
      float joyStickRot=getData.substring(2,index).toInt();
      float joyStickForBack=getData.substring(index+1,index2).toInt();
      float joyStickLeftRight=getData.substring(index2+1,dataLen).toInt();


      uint8_t servoVal=map(joyStickRot,0,1000,servoLeftDegree,servoRightDegree);
      int16_t speedVal=map(joyStickForBack,0,1000,-500,500);
      // int16_t speedVal=getData.substring(index+1,dataLen).toInt();

      if(speedVal>0) {
        speedVal=map(speedVal,0,500,1500,1700);
      } else {
        speedVal=map(speedVal,0,-500,1500,1000);
        if(speedVal<1005) {
          speedVal=1000;
        }
      }


      microRobot.servoWriteRaw(motorIndex, speedVal);

      // lastSpeedVal=speedVal;

      microRobot.servoWrite(servoIndex, servoVal);
    }
  }

}

```
