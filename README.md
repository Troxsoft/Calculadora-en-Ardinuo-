# Calculadora-en-Ardinuo-
una simple calculadora :) dejo las fotos y el codigo
Este tutorial servira SOLO CON EL ttp29 QUE ES UN TIPO DE TECLADO Y EL CODIGO SOLO FUNCIONA CON EL 
4x4 teclas
### este tutoria solo vera el apartado de la programacion de una calculadora con el i2c modulo para lcd 16x2 y el teclado ttp29 :(
## NO se dejan fotos :(
EL pin SCL de este tutorial es: 7 
EL pin SDO es en este tutorial es: 8

# estos pines se pueden cambiar.

### Las librerias utilizadas en este tutorial son:
- LiquidCrystal_I2C.h
- stdio.h
- stdlib.h

```c++
/* Define the digital pins used for the clock and data */
#include <LiquidCrystal_I2C.h>
#include <stdio.h>
// para que funcione el teclado conecta un jumper en 2 al 2 del 2
#include <stdlib.h>
#define SCL_PIN 7
#define SDO_PIN 8
LiquidCrystal_I2C lcd(0x27, 16, 2);
String numero1("");
String numero2("");
bool numero1_activado = true;
bool numero2_activado = false;
String operador("");  // + - * /
bool operador_ya = false;
/* Used to store the key state */
byte Key;

void setup() {
  lcd.init();
  lcd.backlight();
  /* Initialize the serial interface */
  Serial.begin(9600);
  /* Configure the clock and data pins */
  pinMode(SCL_PIN, OUTPUT);
  pinMode(SDO_PIN, INPUT);
  lcd.clear();
}

/* Main program */
void loop() {
  /* Read the current state of the keypad */
  Key = Read_Keypad();
  /* If a key has been pressed output it to the serial port */
  if (Key) {

    if (numero1_activado) {
      if (!es_operador(Key)) {
        if (Key != 10) {

          numero1.concat(Key);
          lcd.clear();
          lcd.print(numero1);
        } else {
          numero1.concat(0);
          lcd.clear();
          lcd.print(numero1);
        }
      } else {
        if (!operador_ya) {
          if (Key == 11)  // suma
          {
            operador_ya = true;
            operador = "+";
            lcd.print("+");
            numero2_activado = true;
            numero1_activado = false;

          } else if (Key == 12)  //resta
          {
            operador_ya = true;
            operador = "-";
            lcd.print("-");
            numero2_activado = true;
            numero1_activado = false;
          } else if (Key == 13)  //mul
          {
            operador_ya = true;
            operador = "*";
            lcd.print("*");
            numero2_activado = true;
            numero1_activado = false;
          } else if (Key == 14)  //div
          {
            operador_ya = true;
            operador = "/";
            lcd.print("/");
            numero2_activado = true;
            numero1_activado = false;
          }
        }
      }
    } else {
      if (!es_operador(Key)) {
        if (Key != 10) {

          numero2.concat(Key);
          lcd.print(numero2);
          lcd.clear();
          lcd.print(numero1);
          lcd.print(operador);
          lcd.print(numero2);
        } else {
          numero2.concat(0);
          lcd.print(numero2);
          lcd.clear();
          lcd.print(numero1);
          lcd.print(operador);
          lcd.print(numero2);
        }
      } else if ((es_operador(Key)) && (Key != 15) && (Key != 16)) {
        Serial.println(Key);
        Serial.println(operador);
        if (Key != 10) {

          numero2.concat(Key);
          lcd.print(numero2);
          lcd.clear();
          lcd.print(numero1);
          lcd.print(operador);
          lcd.print(numero2);
        } else {
          numero2.concat(0);
          lcd.print(numero2);
          lcd.clear();
          lcd.print(numero1);
          lcd.print(operador);
          lcd.print(numero2);
        }
      } else {

        if (Key == 15) {
          if (operador == "+") {
            lcd.clear();
            lcd.flush();
            lcd.print(numero1.toFloat() + numero2.toFloat());


          } else if (operador == "-") {
            lcd.clear();
            lcd.flush();
            lcd.print(numero1.toFloat() - numero2.toFloat());


          } else if (operador == "*") {
            lcd.clear();
            lcd.flush();
            lcd.print(numero1.toFloat() * numero2.toFloat());


          } else if (operador == "/") {
            lcd.clear();
            lcd.flush();
            if (numero2 == "0") {
              lcd.print("no se puede div...");
            } else {
              lcd.print(numero1.toFloat() / numero2.toFloat());
            }
          }
        }
      }
    }
    if (numero1_activado) {
      if (Key == 16) {
        if (numero1 != "") {
          numero1.remove(numero1.length() - 1);
          lcd.clear();
          lcd.print(numero1);
        }
      }
    } else {
      if (Key == 16) {
        if (numero2 != "") {
          numero2.remove(numero2.length() - 1);
          lcd.clear();
          lcd.print(numero1);
          lcd.print(operador);
          lcd.print(numero2);
        }
      }
    }
    Serial.println(Key);
  }

  delay(200);
}


/* Wait a little before reading again
  so not to flood the serial port*/

/* Read the state of the keypad */
byte Read_Keypad(void) {
  byte Count;
  byte Key_State = 0;
  /* Pulse the clock pin 16 times (one for each key of the keypad)
  and read the state of the data pin on each pulse */
  for (Count = 1; Count < 17; Count++) {
    digitalWrite(SCL_PIN, LOW);

    /* If the data pin is low (active low mode) then store the
    current key number */
    if (!digitalRead(SDO_PIN))
      Key_State = Count;
    digitalWrite(SCL_PIN, HIGH);
  }
  return Key_State;
}
bool es_operador(byte Key) {
  if (Key > 10) {
    return true;
  } else {
    return false;
  }
}
```
