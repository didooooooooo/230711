# 230711

프로젝트 아이디어 
(근데 나는 SW이용해서 지금 진행되는 코드의 종류 자체를 바꾸게 하는거.. 그걸 어케할지 모르겠음 그게 하고싶음 나는!! 할튼 일단 렛츠고 )

- **이름 물어보기(네글자) >> HI __ 이렇게 나옴
스위치 이용해서 이름 이니셜이 바뀜
스위치로 이름 바꿀 때 마다 LED 들어옴
이름 다 바꿧으면 SW3 버튼 누르기
환영인사까지 해줌** >> 여기까지만 구현되었어용
- 오늘의 퀴즈를 시작함
정답이면 딩동~ with LED 초록색
틀리면 삑~ with LED 빨간색
- 오늘은 습할까 안습할까? 
습하면 sw3 누름 안습하면 그냥 3초 기다림
- 지금은 비가 올까 안올까?
비가 오면 sw3 누름, 비가 안오면 그냥 기다림
- 지금은 여름철 실내 적정온도일까 적정온도가 아닐까?
맞으면 sw3 누름, 안오면 기다림
- 지금 혹시 너무 어두운가요? 어두우면 불 켜드릴까요?
sw3 누르면 불 켜줌(relay 이용 한 다.)


#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <Adafruit_Sensor.h>
#include<DHT.h>

#define DHTPIN 2
#define DHTTYPE DHT11

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define PIN_CDS 26

DHT dht(DHTPIN, DHTTYPE);

const int SW1 = 3;
const int SW2 = 4;

const int buzzer = 6;
const int relay = 22;

const int RED_LED = 7;
const int GREEN_LED = 10;
const int BLUE_LED = 11;

bool nameSet = false;

char alph[] = {'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M','N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z'};
char name[5] = {'A', 'A', 'A', 'A', '\0'};
int name_index = 0;
int sec = 0;

Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

void setup() {
  pinMode(PIN_CDS, INPUT);

  pinMode(RED_LED, OUTPUT);
  pinMode(GREEN_LED, OUTPUT);
  pinMode(BLUE_LED, OUTPUT);
  pinMode(relay, OUTPUT);
  pinMode(SW1, INPUT_PULLUP);
  pinMode(SW2, INPUT_PULLUP);

  Wire.setSDA(12);
  Wire.setSCL(13);
  Wire.begin();
  dht.begin();
  Serial.begin(115200);

  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)){
    Serial.println(F("SSD1306 allocation failed "));
    for(;;);
  }

  delay(2000);
  display.clearDisplay();
  display.setTextSize(1);

  display.setTextColor(WHITE);
  display.setCursor(0,10); //cursor 위치 정해주는 코 드 임 

  display.println("Hello, world!");
  display.display();
  delay(3000);
  
}

void loop(){

  display.clearDisplay();
  display.setTextColor(WHITE);
  display.setCursor(0,10);

  // If SW1 is pressed, move to the next character
  if (digitalRead(SW1) == LOW) {
    name[name_index] = alph[(name[name_index] - 'A' + 1) % 26];
    digitalWrite(RED_LED, LOW);
    digitalWrite(GREEN_LED, LOW);
    digitalWrite(BLUE_LED, LOW);
    tone(buzzer, 659);
    delay(100);
    noTone(buzzer);
    delay(100);
    delay(200); // debouncing
  }

  // If SW2 is pressed, move to the next position
  if (digitalRead(SW2) == LOW) {
    name_index = (name_index + 1) % 4;
    digitalWrite(RED_LED, HIGH);
    digitalWrite(GREEN_LED, HIGH);
    digitalWrite(BLUE_LED, HIGH); 
    tone(buzzer, 784);
    delay(100);
    noTone(buzzer);
    delay(100);// assuming names are 4 letters
    delay(200); // debouncing
  }

  // If all names are set, set the nameSet variable to true
  if(name_index == 3 && digitalRead(SW2) == LOW) {
    nameSet = true;
    
  }

  // If the name has been set, display welcome message
  if(nameSet) {
    tone(buzzer, 1318);
    delay(100);
    noTone(buzzer);
    delay(100);

    tone(buzzer, 1318);
    delay(100);
    noTone(buzzer);
    delay(100);

    tone(buzzer, 1318);
    delay(200);
    noTone(buzzer);
    delay(100);

    tone(buzzer, 1046.5);
    delay(100);
    noTone(buzzer);
    delay(100);

    tone(buzzer, 1318);
    delay(50);
    noTone(buzzer);
    delay(100);

    tone(buzzer, 1568);
    delay(50);
    noTone(buzzer);
    delay(300);

    tone(buzzer, 784);
    delay(1000);
    noTone(buzzer);
    delay(100);

    display.print("Hi, ");
    display.print(name);
    display.println("!");
    display.println("Press Switch3!");
  }
  else {
    display.print("Current Name: ");
    display.println(name);
  }

  display.display();
  delay(100);
}
