/* @file CustomKeypad.pde
|| @version 1.0
|| @author Alexander Brevig
|| @contact alexanderbrevig@gmail.com
||
|| @description
|| | Demonstrates changing the keypad size and key values.
|| #
*/
#include <Keypad.h>
#include <LiquidCrystal.h>
#include <MD_Parola.h>
#include <MD_MAX72xx.h>
#include <SPI.h>
#define HARDWARE_TYPE MD_MAX72XX::PAROLA_HW
#define MAX_DEVICES 2

#define CLK_PIN 35
#define DATA_PIN 32
#define CS_PIN 33

MD_Parola P = MD_Parola(HARDWARE_TYPE, DATA_PIN, CLK_PIN, CS_PIN, MAX_DEVICES);

int kondisi;
LiquidCrystal lcd(46, 47, 50, 51, 48, 49);  // Inisialisasi objek LCD
const String correctPassword = "1234";      //

const byte ROWS = 4;  //four rows
const byte COLS = 4;  //four columns
//define the cymbols on the buttons of the keypads
char hexaKeys[ROWS][COLS] = {
  { '1', '2', '3', 'A' },
  { '4', '5', '6', 'B' },
  { '7', '8', '9', 'C' },
  { '*', '0', '#', 'D' }
};
byte rowPins[ROWS] = { 38, 39, 40, 41 };  //connect to the row pinouts of the keypad
byte colPins[COLS] = { 42, 43, 44, 45 };  //connect to the column pinouts of the keypad

//initialize an instance of class NewKeypad
Keypad customKeypad = Keypad(makeKeymap(hexaKeys), rowPins, colPins, ROWS, COLS);

String enteredPassword = "";  // String untuk menyimpan password yang dimasukkan
bool doorLocked = true;

byte smiley[8] = {
  0b00000,
  0b00000,
  0b01010,
  0b00000,
  0b00000,
  0b10001,
  0b01110,
  0b00000
};

void setup() {
  pinMode(53, OUTPUT);
  digitalWrite(53, HIGH);
  lcd.begin(16, 2);  // Inisialisasi LCD
  lcd.clear();
  delay(2000);
  lcd.print("Enter Password:");
  Serial.begin(9600);
  P.begin();
  P.setZoneEffect(0, true, PA_FLIP_LR);
}

void loop() {
  char customKey = customKeypad.getKey();
  if (customKey) {
    if (customKey == '#') {
       checkPassword();
    } else {
      enteredPassword += customKey;
      lcd.setCursor(enteredPassword.length() - 1, 1);
      lcd.print('*');
    }
  }
  while (kondisi == 1) {
    lcd.setCursor(, 0);
    lcd.print("PLEASE TRY");
  }
}
}

void checkPassword() {

  if (enteredPassword == correctPassword) {
    lcd.clear();
    lcd.print("Access Granted");
    delay(1000);
    lcd.clear();
    lcd.clear();
    delay(2000);
    lcd.setCursor(1, 0);

    lcd.print("WELCOME BACK !");
    delay(2000);
    lcd.setCursor(4, 1);
    lcd.print("AZAZEUS");
    delay(2000);
    lcd.clear();

    lcd.setCursor(5, 0);
    lcd.print("HAVE A ");
    delay(2000);
    lcd.setCursor(3, 1);
    lcd.print("NICE DAY");
    lcd.createChar(1, smiley);
    lcd.setCursor(12, 1);
    lcd.write(byte(1));
    delay(2000);
    lcd.clear();
    delay(1000);
    kondisi = 1;
    (P.displayAnimate())
      P.displayText("ISOH CUK ! ", PA_CENTER, 100, 1, PA_SCROLL_RIGHT, PA_SCROLL_RIGHT);
    delay(1000);
    break;

    delay(2000);
    lcd.clear();
    lcd.print("Enter Password:");
  } else {
    lcd.clear();
    lcd.print("Access Denied");
    delay(2000);
    lcd.clear();
    lcd.print("Enter Password:");
  }

  enteredPassword = "";
}
