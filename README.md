# IOT

# 13주차

## 라즈베리파이 초기 설정
```
sudo apt update
sudo apt updgrade // 인터넷 연결 확인
```

 - 한글깨짐 & 사용
```
sudo apt-get install fonts-unfonts-core -y
sudo apt-get install ibus ibus-hangul -y
sudo reboot
```
## InfluxDB 설치 (리눅스)
  - InfluxDB download key using wget
```
wget -q https://repos.influxdata.com/influxdata-archive_compat.key
echo '393e8779c89ac8d958f81f942f9ad7fb82a25e133faddaf92e15b16e6ac9ce4c influxdata-archive_compat.key' | sha256sum -c && cat influxdata-archive_compat.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/influxdata-archive_compat.gpg > /dev/null
echo 'deb [signed-by=/etc/apt/trusted.gpg.d/influxdata-archive_compat.gpg] https://repos.influxdata.com/debian stable main' | sudo tee /etc/apt/sources.list.d/influxdata.list
```
  - Packages are up to date && install Influxdb
```
 sudo apt-get update && sudo apt-get install influxdb -y

```
  - InfluxDB as a background service on startup
```
sudo service influxdb start
```
  - InfluxDB is status (service)
```
sudo service influxdb status
```

### influxdb import with python
```
pip install influxdb
```
  - hint: See PEP 668 for the detailed specification.
```
  sudo rm /usr/lib/python3.11/EXTERNALLY-MANAGED
```
### InfluxDB 데이터베이스 만들기
```
$ influx
create database 데이터베이스이름
확인하려면 show databases
```
## Grafana Installation (데이터 시각화 및 대시보드 구성 도구)

### 1. Install the prerequisite packages
```
sudo apt-get install -y apt-transport-https software-properties-common wget
```

### 2. Import the GPG key:
```
sudo mkdir -p /etc/apt/keyrings/
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
```

### 3. To add a repository for stable releases, run the following command:
```
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```

### 4. Run the following command to update the list of available packages:
```
sudo apt-get update && sudo apt-get install grafana -y

```
### 5. Run the following command to server start
```
sudo systemctl start grafana-server
```

## TelegramBot
```
  pip install python-telegram-bot --upgrade
  git clone https://github.com/python-telegram-bot/python-telegram-bot
  pip install python-telegram-bot[job-queue] --pre
```
### TelegramBot Docs
  - https://docs.python-telegram-bot.org/en/stable/telegram.ext.jobqueue.html

## PI 카메라 연결
  - Legacy Camera disable
```
  libcamera-hello -t 0
```
  - Python Lib 설치
```
  pip install picamera2
```
  - Error
```
libEGL warning : DRI2: failed to authenticate
Made X/EGL preview window
[1773] INFO Camera camera_manager.cpp:297 libcamera v0.0.5+83-bde9b04f
ERROR: *** no cameras available ***
```
  - 참고
```
  https://github.com/raspberrypi/picamera2/blob/main/examples/capture_png.py
```


# 중간고사 전 내용

수정 체크리스트
- ~~레이아웃 수정~~
- 장치, 주차 별 설명 추가

## 아두이노 2주차 초음파 센서 및 led 점등
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

## 3주차 미세먼지 센서 influxDB 사용 python 아두이노

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

## 4주차 i2c lcd 통신

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
```c
#include <Wire.h> // i2c 통신을 위한 기본 라이브러리
#include <LiquidCrystal_I2C.h> // i2c led 기본 라이브러리
//미세먼지 센서 i2c lcd에 띄우기
int Vo = A0;
int V_led = 12;

float Vo_value = 0;
float Voltage = 0;
float dustDensity = 0;

LiquidCrystal_I2C lcd(0x27, 16, 2);

void setup()
{
  Serial.begin(9600); 
  pinMode(V_led, OUTPUT);
  pinMode(Vo, INPUT);

  lcd.init();  // i2c lcd 초기화
  lcd.backlight(); // 백라이트켜기
  lcd.print("LCD init");
  delay(2000);
  lcd.clear();
}

void loop()
{
  long duration, distance;
  
  digitalWrite(V_led, LOW);
  delayMicroseconds(280);
  Vo_value = analogRead(Vo);
  delayMicroseconds(40);
  digitalWrite(V_led, HIGH);
  delayMicroseconds(9680);

  Voltage = Vo_value*5.0 / 1023.0;
  dustDensity = (Voltage - 0.5)/0.005;

  lcd.setCursor(0, 0);
  lcd.print(dustDensity);
  lcd.print("um");
  delay(1000);
  lcd.clear();
}
```
