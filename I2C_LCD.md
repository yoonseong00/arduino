# arduino

#include <Wire.h>
#include <LiquidCrystal_I2C.h> 

LiquidCrystal_I2C lcd(0x27,16,2);  // LCD I2C 주소를 설정한다. 16칸 2줄 LCD 사용
                                    // 0x27 대신 스캐닝된 주소를 넣는다.
const int potentiometerPin = A3;  // 가변저항의 출력값을 A3에서 읽어들임

// 특수문자 만들기
byte heart[8] ={
  0b00000,
  0b01010,
  0b11111,
  0b11111,
  0b11111,
  0b01110,
  0b00100,
  0b00000
};

byte smiley[8] ={
  0b00000,
  0b00000,
  0b01010,
  0b00000,
  0b00000,
  0b10001,
  0b01110,
  0b00000
};

byte frownie[8] ={
  0b00000,
  0b00000,
  0b01010,
  0b00000,
  0b00000,
  0b00000,
  0b01110,
  0b10001
};

byte armsDown[8] ={
  0b00100,
  0b01010,
  0b00100,
  0b00100,
  0b01110,
  0b10101,
  0b00100,
  0b01010
};

byte armsUp[8] ={
  0b00100,
  0b01010,
  0b00100,
  0b10101,
  0b01110,
  0b00100,
  0b00100,
  0b01010
};

void setup(){
  lcd.init();
  lcd.backlight();                // 백라이트를 켠다.
  
  // 새로운 문자 정의
  lcd.createChar(0, heart);                   
  lcd.createChar(1, smiley); 
  lcd.createChar(2, frownie);
  lcd.createChar(3, armsDown);
  lcd.createChar(4, armsUp);

  // 상단 텍스트 및 아이콘을 초기화
  lcd.setCursor(0, 0);                 // 첫 번째 줄 첫째 칸
  lcd.print("I ");
  lcd.write(byte(0));                           // 0바이트에 저장된 heart를 쓴다.
  lcd.print(" Array Kit! ");
  lcd.write((byte)1);                           // 1바이트에 저장된 smiley를 쓴다.
}

void loop(){
  int sensorReading = analogRead(potentiometerPin);       // A3의 가변저항값을 읽는다.
  int delayTime = map(sensorReading, 0, 1023, 200, 1000);  // 저항값을 200 – 1000으로 맵핑

  int width = 16;  // LCD의 너비
  int pos = width; // 문자 시작 위치를 LCD 화면의 오른쪽 밖으로 설정

  while (true) {
    // 하단의 내용을 지우기
    lcd.setCursor(0, 1);  // 두 번째 줄의 첫 칸으로 설정
    lcd.print("                ");  // 하단의 기존 내용을 지우기 위해 공백을 출력

    // 현재 위치에 문자 표시
    lcd.setCursor(pos, 1);                       // 커서를 하단의 현재 위치로 설정
    lcd.write(4);                               // 팔이 위로 든 사람을 그린다.
    delay(delayTime);                           // 가변저항 값만큼 지연

    // 다음 위치로 이동
    pos--;
    if (pos < -1) {  // 문자 위치가 화면을 벗어난 경우
      pos = width;  // 문자를 화면 오른쪽 밖으로 다시 설정
    }
  }
}
