#include <Wire.h>
#include <Bluepad32.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_MPU6050.h>
#include <Adafruit_PWMServoDriver.h>


Adafruit_MPU6050 mpu;
Adafruit_PWMServoDriver armDrive = Adafruit_PWMServoDriver(0x40);
// تعريف بنات المواتير
#define motor1_in1 13
#define motor1_in2 12
#define motor2_in1 14
#define motor2_in2 27
#define motor3_in1 25
#define motor3_in2 26
#define motor4_in1 33
#define motor4_in2 32

#define pwm_1 23
#define pwm_2 15

#define SDA 21
#define SCL 22

#define SERVO_MIN  150 // أقل نبضة (صفر درجة)
#define SERVO_MAX  600 // أقصى نبضة (180 درجة)

bool ultrasonicMode = false;
unsigned long currentTime = 0;
unsigned long lastMeasureTime = 0;
const long measureInterval = 100;

int trigPinF = 19;
int echoPinF = 34;
int trigPinR = 18;
int echoPinR = 35;
int trigPinL = 5;
int echoPinL = 4;

// مصفوفة لتخزين الزوايا الحالية للـ 4 مواتير (تبدأ من منتصف المسافة 90)
int angles[4] = {90, 90, 90, 90}; 
int stepAmount = 1; // مقدار الزيادة أو النقصان في كل ضغطة

// مصفوفة لتخزين وحدات التحكم (يدعم حتى 4 مقابض)
ControllerPtr myControllers[BP32_MAX_CONTROLLERS];

// دالة تُستدعى عند اتصال يد التحكم
void onConnectedController(ControllerPtr ctl) {
    bool foundEmptySlot = false;
    for (int i = 0; i < BP32_MAX_CONTROLLERS; i++) {
        if (myControllers[i] == nullptr) {
            Serial.printf("CALLBACK: Controller is connected, index=%d\n", i);
            // طباعة بيانات يد التحكم المتصلة (النوع والعنوان)
            ControllerProperties properties = ctl->getProperties();
            Serial.printf("Controller model: %s\n", ctl->getModelName().c_str());            myControllers[i] = ctl;
            foundEmptySlot = true;
            break;
        }
    }
}

// دالة تُستدعى عند فصل يد التحكم
void onDisconnectedController(ControllerPtr ctl) {
    for (int i = 0; i < BP32_MAX_CONTROLLERS; i++) {
        if (myControllers[i] == ctl) {
            Serial.printf("CALLBACK: Controller is disconnected from index=%d\n", i);
            myControllers[i] = nullptr;
            break;
        }
    }
}

void forward()
{
  Serial.println("D-Pad UP");

  digitalWrite(motor1_in1, HIGH);
  digitalWrite(motor1_in2, LOW);
  digitalWrite(motor2_in1, HIGH);
  digitalWrite(motor2_in2, LOW);
  digitalWrite(motor3_in1, HIGH);
  digitalWrite(motor3_in2, LOW);
  digitalWrite(motor4_in1, HIGH);
  digitalWrite(motor4_in2, LOW);
}
void backward()
{
  Serial.println("D-Pad DOWN");

  digitalWrite(motor1_in1, 0);
  digitalWrite(motor1_in2, 1);
  digitalWrite(motor2_in1, 0);
  digitalWrite(motor2_in2, 1);
  digitalWrite(motor3_in1, 0);
  digitalWrite(motor3_in2, 1);
  digitalWrite(motor4_in1, 0);
  digitalWrite(motor4_in2, 1);
}
void left()
{
  Serial.println("D-Pad LEFT");

  digitalWrite(motor1_in1, HIGH);
  digitalWrite(motor1_in2, LOW);
  digitalWrite(motor2_in1, HIGH);
  digitalWrite(motor2_in2, LOW);
  digitalWrite(motor3_in1, 0);
  digitalWrite(motor3_in2, 1);
  digitalWrite(motor4_in1, 0);
  digitalWrite(motor4_in2, 1);
}
void right()
{
  Serial.println("D-Pad RIGHT");

  digitalWrite(motor1_in1, 0);
  digitalWrite(motor1_in2, 1);
  digitalWrite(motor2_in1, 0);
  digitalWrite(motor2_in2, 1);
  digitalWrite(motor3_in1, HIGH);
  digitalWrite(motor3_in2, LOW);
  digitalWrite(motor4_in1, HIGH);
  digitalWrite(motor4_in2, LOW);
}
void stop()
{
  Serial.println("stop");

  digitalWrite(motor1_in1, 0);
  digitalWrite(motor1_in2, 0);
  digitalWrite(motor2_in1, 0);
  digitalWrite(motor2_in2, 0);
  digitalWrite(motor3_in1, 0);
  digitalWrite(motor3_in2, 0);
  digitalWrite(motor4_in1, 0);
  digitalWrite(motor4_in2, 0);
}

void setServoAngle(uint8_t num, int angle) {
    int pulse = map(angle, 0, 180, SERVO_MIN, SERVO_MAX);
    armDrive.setPWM(num, 0, pulse);
}

/*
// دالة موحدة للتحكم في المواتير (الاتجاه والسرعة معاً)
void moveMotors(int leftSpeed, int rightSpeed) {
    // تحديد اتجاه الجانب الأيسر
    if (leftSpeed > 0) {
        digitalWrite(motor1_in1, HIGH); digitalWrite(motor1_in2, LOW);
        digitalWrite(motor2_in1, HIGH); digitalWrite(motor2_in2, LOW);
    } else if (leftSpeed < 0) {
        digitalWrite(motor1_in1, LOW); digitalWrite(motor1_in2, HIGH);
        digitalWrite(motor2_in1, LOW); digitalWrite(motor2_in2, HIGH);
    } else {
        digitalWrite(motor1_in1, LOW); digitalWrite(motor1_in2, LOW);
        digitalWrite(motor2_in1, LOW); digitalWrite(motor2_in2, LOW);
    }
    
    // تحديد اتجاه الجانب الأيمن
    if (rightSpeed > 0) {
        digitalWrite(motor3_in1, HIGH); digitalWrite(motor3_in2, LOW);
        digitalWrite(motor4_in1, HIGH); digitalWrite(motor4_in2, LOW);
    } else if (rightSpeed < 0) {
        digitalWrite(motor3_in1, LOW); digitalWrite(motor3_in2, HIGH);
        digitalWrite(motor4_in1, LOW); digitalWrite(motor4_in2, HIGH);
    } else {
        digitalWrite(motor3_in1, LOW); digitalWrite(motor3_in2, LOW);
        digitalWrite(motor4_in1, LOW); digitalWrite(motor4_in2, LOW);
    }

    // إرسال السرعة (PWM) للمتحكمات
    analogWrite(pwm_1, abs(leftSpeed));
    analogWrite(pwm_2, abs(rightSpeed));
    Serial.printf("rightSpeed: %d ,leftSpeed: %d \n", rightSpeed, leftSpeed);
    
}
*/
float getDistance(int trig, int echo) {
  digitalWrite(trig, LOW);
  delayMicroseconds(2);
  digitalWrite(trig, HIGH);
  delayMicroseconds(10);
  digitalWrite(trig, LOW);
  long duration = pulseIn(echo, HIGH, 30000); 
  if (duration == 0) return 400.0;
  return duration * 0.034 / 2;
}

void turnDegree(int targetAngle) {
    stop();
    delay(100);
    float yaw = 0;
    unsigned long t_last = millis();
    
    // سرعة دوران هادئة لضمان الدقة
    analogWrite(pwm_1, 160); 
    analogWrite(pwm_2, 160);

    while (abs(yaw) < abs(targetAngle)) {
        sensors_event_t a, g, temp;
        mpu.getEvent(&a, &g, &temp);

        unsigned long t_now = millis();
        float dt = (t_now - t_last) / 1000.0;
        t_last = t_now;

        // تحويل السرعة الزاوية من راديان إلى درجات وإضافتها للياو
        float gyroZ = g.gyro.z * 57.2958; 
        if (abs(gyroZ) > 1.0) { // لتجاهل الضوضاء البسيطة
            yaw += gyroZ * dt;
        }

        if (targetAngle > 0) right(); // دوران لليمين
        else left();                // دوران لليسار
        
        delay(5); 
    }
    stop();
}

void setup() {
    Serial.begin(115200);
    Serial.println("Bluepad32 is starting...");

    pinMode(motor1_in1, OUTPUT);pinMode(motor1_in2, OUTPUT);
    pinMode(motor2_in1, OUTPUT);pinMode(motor2_in2, OUTPUT);
    pinMode(motor3_in1, OUTPUT);pinMode(motor3_in2, OUTPUT);
    pinMode(motor4_in1, OUTPUT);pinMode(motor4_in2, OUTPUT);

    pinMode(pwm_1, OUTPUT);pinMode(pwm_2, OUTPUT);

    pinMode(trigPinF, OUTPUT); pinMode(echoPinF, INPUT);
    pinMode(trigPinR, OUTPUT); pinMode(echoPinR, INPUT);
    pinMode(trigPinL, OUTPUT); pinMode(echoPinL, INPUT);

    // إعداد قنوات PWM
    pinMode(pwm_1, OUTPUT); 
    pinMode(pwm_2, OUTPUT);

    // إعداد المكتبة وتحديد دوال الـ Callbacks
    BP32.setup(&onConnectedController, &onDisconnectedController);

    // ملاحظة: إذا أردت مسح الأجهزة المقترنة سابقاً لعمل اقتران جديد:
    // BP32.forgetBluetoothKeys();

    // دالة الmpu
    if (!mpu.begin()) {
    Serial.println("MPU6050 connection failed!");
    }
    mpu.setGyroRange(MPU6050_RANGE_250_DEG);

    armDrive.begin();
    armDrive.setPWMFreq(60);  // تردد السيرفو العادي هو 60 هرتز

    for(int i=0; i<4; i++) {
        setServoAngle(i, angles[i]);
    }
}   

void loop() {
    // تحديث حالة المكتبة واستقبال البيانات من البلوتوث
    BP32.update();

    for (int i = 0; i < BP32_MAX_CONTROLLERS; i++) {

        ControllerPtr myController = myControllers[i];

        if (myController && myController->isConnected()) {
            
            // فحص الأزرار الرئيسية (A, B, X, Y) أو (Cross, Circle, Square, Triangle)


            // فحص أزرار الأكتاف (L1, R1, L2, R2)
            if (myController->l1()) Serial.println("L1 pressed");
            if (myController->r1()) Serial.println("R1 pressed");
            if (myController->l2()) Serial.printf("L2 trigger: %d\n", myController->throttle()); // قيمة الضغط
            if (myController->r2()) {/*Serial.printf("R2 trigger(stop): %d\n", myController->brake()); stop();*/}    // قيمة الضغط

            // فحص أزرار الاتجاهات (D-pad)
            /*
            uint8_t dpad = myController->dpad();
            if (dpad & DPAD_UP) forward();
            if (dpad & DPAD_DOWN) backward();
            if (dpad & DPAD_LEFT) left();
            if (dpad & DPAD_RIGHT) right();
            */
            // فحص أزرار الـ Menu والـ Options
            if (myController->miscSystem()) Serial.println("System/PS Button pressed");
            if (myController->miscSelect()) {Serial.println("Select/Share Button pressed"); stop(); ultrasonicMode = !ultrasonicMode; printf("ul mode is :%d\n",ultrasonicMode);delay(250);}
            if (myController->miscStart()) Serial.println("Start/Options Button pressed");
            // --- قراءة الأنالوج (Joysticks) ---
            // الذراع الأيسر (Left Stick)
            int32_t lx = myController->axisX();
            int32_t ly = myController->axisY();
            //printf("ly:%d, lx:%d", ly, lx);
            if(!ultrasonicMode)
            {
                myController->setColorLED(0, 0, 255);
                if(lx > 200 && ly < -200)
                {
                    forward();
                    analogWrite(pwm_1, 0);
                    analogWrite(pwm_2, 255);
                }else if(lx < -200 && ly < -200)
                {
                    forward();
                    analogWrite(pwm_1, 255);
                    analogWrite(pwm_2, 0);
                }
                else if(ly < -200)
                {
                    forward();
                    analogWrite(pwm_1, 255);
                    analogWrite(pwm_2, 255);
                }else if(ly > 200)
                {
                    backward();
                    analogWrite(pwm_1, 255);
                    analogWrite(pwm_2, 255);
                }else if(lx > 200)
                {
                    right();
                    analogWrite(pwm_1, 255);
                    analogWrite(pwm_2, 255); 
                }else if(lx < -200)
                {
                    left();
                    analogWrite(pwm_1, 255);
                    analogWrite(pwm_2, 255);
                }else{
                    stop();
                }
            }
            /*
            //========== كود الحركة بالانالوج الشمال ==========
            int throttle = map(ly, -512, 511, -255, 255);
            int steering = map(lx, -512, 511, -255, 255);
            // حساب سرعة كل جانب (Differential Drive)
            int leftMotorSpeed = -throttle + steering;
            int rightMotorSpeed = -throttle - steering;
            // التأكد أن القيم في حدود PWM (0-255)
            leftMotorSpeed = constrain(leftMotorSpeed, -255, 255);
            rightMotorSpeed = constrain(rightMotorSpeed, -255, 255);
            // "Deadzone" لمنع حركة المحركات في وضع السكون
            if (abs(throttle) < 30 && abs(steering) < 30) {
                moveMotors(0, 0);
            } else {
                moveMotors(leftMotorSpeed, rightMotorSpeed);
            }*/
            //===========================================

// ========================== التحكم في الأرم ==========================
            // الذراع الأيمن (Right Stick)
            int32_t rx = myController->axisRX();
            int32_t ry = myController->axisRY();

            // تحديث الزوايا بناءً على الإدخال
            if(ry < -100) angles[0] += stepAmount;
            else if(ry > 100) angles[0] -= stepAmount;

            if(rx > 100) angles[1] += stepAmount;
            else if(rx < -100) angles[1] -= stepAmount;

            if (myController->a()) angles[2] += stepAmount;
            else if (myController->b()) angles[2] -= stepAmount;

            if (myController->x()) angles[3] += stepAmount;
            else if (myController->y()) angles[3] -= stepAmount;

            // التأكد إن الزوايا متتخطاش الحدود (0 إلى 180) وإرسالها للسيرفو
            for(int j=0; j<4; j++) {
                angles[j] = constrain(angles[j], 0, 180);
                setServoAngle(j, angles[j]);
            }

// ========================== ultrasonic code ==========================
            if(ultrasonicMode)
            {
                
                myController->setColorLED(0, 255, 0);
                Serial.println("ultrasonic mode");
                int distF = getDistance(trigPinF, echoPinF);
                int distR = getDistance(trigPinR, echoPinR);
                int distL = getDistance(trigPinL, echoPinL);
                /*
                if (distF > 15)
                { // لسا المسافة 25 ممكن تدعدل حسب التست
                forward();
                } 
                else
                { // فيه عائق قدامك
                    stop();
                    delay(200);
                    if (distR > distL && distR > 15) {
                    right();
                    delay(750); // وقت للدوران لسا هيتعدل علي حسب سرعة دوران العربية
                    } else if (distL > 15) {
                    left();
                    delay(750);
                    } else {
                    backward();
                    delay(750);
                    
                }*/
                if (distF > 15) {
                    forward();
                    analogWrite(pwm_1, 180);
                    analogWrite(pwm_2, 180);
                } else {
                    stop();
                    delay(200);
                    if (distR > distL && distR > 15) {
                        turnDegree(90);  // دوران يمين 90 درجة دقيق
                    } else if (distL > 15) {
                        turnDegree(-90); // دوران يسار 90 درجة دقيق
                    } else {
                        backward();
                        delay(500);
                        turnDegree(180); // دوران للخلف
                    }
                }
            }
        }
    }
    
    delay(10);
}
// =====================================================================
            /*
            // طباعة القيم فقط إذا كانت الحركة أكبر من 20 (لتجنب التشويش البسيط)
            if (abs(lx) > 100 || abs(ly) > 100) {
                Serial.printf("Left Stick: x=%-4d, y=%-4d | ", lx, ly);
            }
            
            if (abs(rx) > 100 || abs(ry) > 100) {
                Serial.printf("Right Stick: x=%-4d, y=%-4d", rx, ry);
            }

            // سطر جديد لو فيه حركة
            if (abs(lx) > 100 || abs(ly) > 100 || abs(rx) > 100 || abs(ry) > 100) {
                Serial.println();
            }
            */
