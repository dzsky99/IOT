# IOT

아두이노 2주차 초음파 센서 및 led 점등
```C
// C++ code
//
#define TRIG 13
#define ECHO 12 // 기호상수

int led_red = 7;
int led_green = 8;

void setup()
{
  Serial.begin(9600); // 통신 속도 설정
  pinMode(7, OUTPUT); // input output 설정, 아두이노 외부 output
  pinMode(8, OUTPUT);
  pinMode(TRIG, OUTPUT);
  pinMode(ECHO, INPUT);
}

void loop()
{
  long duration, distance;
  
  digitalWrite(TRIG, LOW); // 디지털 신호 쓰기
  delayMicroseconds(2);
  digitalWrite(TRIG, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG, LOW);
  
  duration = pulseIn(ECHO, HIGH);
  distance = duration / 58.2; // 17 / 1000

  if(distance >= 100) { // 거리가 100 이상이면 레드, 이외에는 그린
    digitalWrite(led_red, HIGH);
    digitalWrite(led_green, LOW);
  }
  else {
    digitalWrite(led_green, HIGH);
    digitalWrite(led_red, LOW);
  }
    
  Serial.println(duration ); // 시리얼 모니터에 출력
  Serial.print("\nDistance :");
  Serial.print(distance);
  Serial.println(" Cm");
  delay(1000);
}
  
  /* 
  digtalWrite(led_red, HIGH);
  delay(1000); // Wait for 1000 millisecond(s) // 1초에 한번 씩 번갈아서 점등
  digitalWrite(led_red, LOW);
  delay(1000); // Wait for 1000 millisecond(s)
  digitalWrite(led_green, HIGH);
  delay(1000); // Wait for 1000 millisecond(s)
  digitalWrite(led_green, LOW);
  delay(1000); // Wait for 1000 millisecond(s)
  */
```

3주차 미세먼지 센서 influxDB 사용 python 아두이노

```python
import serial
from influxdb_client import InfluxDBClient
import time

serial_port = 'COM9'
baud_rate = 9600
timeout = 2

#influxDB v2 설정
influxdb_url = "http://localhost:8086"
influxdb_token = "5-mxazPWIaZ4_VNdTUpTGIjUNimJQ5ySTIghAQpJAWAnfIiPapDlWy9uU6P7kEP6wX3Jf-QhLCiYbEX-QT4Zsg=="
influxdb_org = "test" #조직
influxdb_bucket = "dust" # 데이터가 저장될 bucket 이름
# InfluxDB 클라이언트 초기화
client = InfluxDBClient(url=influxdb_url, token=influxdb_token, org=influxdb_org)
write_api = client.write_api()

# 시리얼 포트 열기 
try:
    ser = serial.Serial(serial_port, baud_rate, timeout = timeout)
    print(f"Connected to {serial_port} at {baud_rate} baud")
except Exception as e:
    print("Failed to connect to serial prot")
    print(e)
    exit()
                        

try:
    while True:
        if ser.in_waiting > 0:
            # 아두이노로부터 시리얼 데이터를 읽음
            line = ser.readline().decode('utf-8').strip()
            
            # 데이터가 유효한 경우 influxDB에 기록
            if "=" in line:
                key, value = line.split("=")
                try:
                    value = float(value)
                    data=f"sensor_data,device=arduino {key}={value}"
                    write_api.write(bucket=influxdb_bucket, record=data)
                    print(f"Data wriiten to influxDB: {key}={value}")
                except ValueError:
                        print("Invalid data format")
        time.sleep(1)

except KeyboardInterrupt:
    print("프로그램이 종료되었습니다.")

finally:
    ser.close()
```
```C
int Vo = A0;
int V_led = 12;

float Vo_value = 0;
float Voltage = 0;
float dustDensity = 0;

void setup() {
  Serial.begin(9600);
  pinMode(V_led, OUTPUT);
  pinMode(Vo, INPUT);
}

void loop() {
  digitalWrite(V_led, LOW);
  delayMicroseconds(280);
  Vo_value = analogRead(Vo);
  delayMicroseconds(40);
  digitalWrite(V_led, HIGH);
  delayMicroseconds(9680);

  Voltage = Vo_value*5.0 / 1023.0;
  dustDensity = (Voltage - 0.5)/0.005;

  Serial.print("dust=");
  Serial.println(dustDensity);

  delay(1000);
}
```
![image](https://github.com/user-attachments/assets/04ce1202-cd81-480b-b34f-4c22bfcc3a7f)
![image](https://github.com/user-attachments/assets/7f9fe4aa-5874-453d-ac3b-99b7cca90ae7)

4주차 i2c lcd 통신

```c
#include <Wire.h> // i2c 통신을 위한 기본 라이브러리
#include <LiquidCrystal_I2C.h> // i2c led 기본 라이브러리

// 초음파 센서 lcd에 띄우기

#define TRIG 13
#define ECHO 12

LiquidCrystal_I2C lcd(0x27, 16, 2);

void setup()
{
  Serial.begin(9600); 
  pinMode(TRIG, OUTPUT);
  pinMode(ECHO, INPUT);

  lcd.init();  // i2c lcd 초기화
  lcd.backlight(); // 백라이트켜기
  lcd.print("LCD init");
  delay(2000);
  lcd.clear();
}

void loop()
{
  long duration, distance;
  
  digitalWrite(TRIG, LOW); 
  delayMicroseconds(2);
  digitalWrite(TRIG, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG, LOW);
  
  duration = pulseIn(ECHO, HIGH);
  distance = duration / 58.2;

  lcd.setCursor(0, 0);
  lcd.print(distance);
  lcd.print("cm");
  delay(1000);
  lcd.clear();

  //lcd.print("Hello, World!");
  
  // for (int position = 0; position < 16; position++) {
  //  lcd.scrollDisplayLeft();
  //  delay(150);
  // }
}
```
