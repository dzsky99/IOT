# IOT

아두이노 1주차 초음파 센서 및 led 점등
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
