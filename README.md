# Zahlencode-Schloss mit Tastenfeld

## Anforderung:

* Mit einem 4x4 Tastenfeld soll, durch die Eingabe eines 4 stelligen PINs, 
  eine LED aufleuchten und ein Servomotor eine bestimmte Position einnehmen und damit ein Schloss, einer Zimmertür betätigen.

* Sobald das Schloss „gesperrt“ ist, soll eine rote LED aufleuchten und der Servo das Schloss schließen. 
  Nach Eingabe des korrekten PINs, soll eine grüne LED leuchten und der Servo das Türschloss öffnen.
* Wenn der Pin 3 mal falsch eingegeben wurde, sperrt sich das Schloss. 
* Die mögliche Anzahl an Entsperrversuchen wird angezeigt. 

* Mit der Taste "*" oder "#", kann das Schloss wieder gesperrt werdern.

* Es gibt nur ein gültiges Passwort. 


## Komponenten 

* 1x Arduino UNO
* 1x Steckplatine
* 1x Tastenfeld 4X4
* 1x rote LED
* 1x grüne LED
* 2x 270 Ohm Widerstand
* 1x Servo Motor (SG90)

## Aufbau

![Aufbau](https://raw.githubusercontent.com/CRBK/Zahlencode-Schloss/master/Zahlencode-Schloss.png)


## Programm Code
//______Bibliotheken laden_____________________________________________________________________________________________
#include <Keypad.h>			// Tastenfeld
#include <Servo.h>			// Servomotor 
 
//______LEDs PIN festlegen_____________________________________________________________________________________________

const int Led_gruen = 13;	// Led Grün
const int Led_rot = 12;		// Led Rot

//______Servomotor konfigurieren_______________________________________________________________________________________

Servo tuerschloss;						// Servo wird jetzt mit "tuerschloss" angesprochen
const int servoPin = 11;
const int geschlossen = 360;
const int offen = 0;
const int openDelay = 3000;

//______Tastenfeld definieren_________________________________________________________________________________________

const byte COLS = 4;					// 4 Spalten
const byte ROWS = 4; 					// 4 Zeilen

char hexaKeys[ROWS][COLS]={				// Belegung definieren
{'1', '2', '3', 'A'},	
{'4', '5', '6', 'B'},
{'7', '8', '9', 'C'},
{'*', '0', '#', 'D'}

};

byte rowPins[ROWS] = { 9, 8, 7, 6 }; 	//Definition der Pins für die 4 Zeilen
byte colPins[COLS] = { 5, 4, 3, 2 };	//Definition der Pins für die 4 Spalten

Keypad tastenfeld = Keypad( makeKeymap(hexaKeys), rowPins, colPins, ROWS, COLS); // Keypad wird jetzt mit "tastenfeld" angesprochen 
 
//______Pincode und Länge festlegen____________________________________________________________________________________________

const byte PINLENGTH = 6;												                // Pin Länge	
char pinCode[PINLENGTH+1] = {'0','6','2','0','2','0'};					// Pincode
char keyBuffer[PINLENGTH+1] = {'-','-','-','-','-','-'};  			// Zwischenspeicher für Pin Eingabe

//______setup____________________________________________________________________________________________________________________

void setup(){
  Serial.begin(9600);
  pinMode(Led_gruen, OUTPUT);         											// Led Grün als Ausgang
  pinMode(Led_rot, OUTPUT);   															// Led rot als Ausgang
  digitalWrite(Led_gruen, LOW); 														// Led grün Standartmäßig Aus
  digitalWrite(Led_rot, LOW);     													// Led rot Standartmäßig Aus
  
  tuerschloss.attach(servoPin);              								// Servo auf Ausgangsposition
  tuerschloss.write(geschlossen);				
}

//______loop erstellen_________________________________________________________________________________________________________

void loop(){
  
  char customKey = tastenfeld.getKey();      						        // Gedrückte Taste abfragen
  if (customKey) {
    if ((int(customKey) >= 48) && (int(customKey) <= 57)){ 			// Check, ob ASCII Wert des Char einer Ziffer zwischen 0 und 9 entspricht
      addToKeyBuffer(customKey); 
    }
    else if (customKey == '#') { 									              // Code überprüfen mit # Taste
      checkKey(); 
    } 
  } 
} 
 
void addToKeyBuffer(char inkey) { 
  Serial.print(inkey); 
  Serial.print(" > ");
    
  for (int i=1; i<PINLENGTH; i++) {								// Von links nach rechts Zeichen um eins weiterkopieren = ältestes Zeichen vergessen
    keyBuffer[i-1] = keyBuffer[i];
  }
  
  keyBuffer[PINLENGTH-1] = inkey;								// in ganz rechter Stelle die letzte Ziffer speichern
  Serial.println(keyBuffer);
}

//______Pin Auswertung_____________________________________________________________________________________________________

void checkKey() {												// Eingabe mit festgelegtem Pincode vergleichen

   if (strcmp(keyBuffer, pinCode) == 0) {
    Serial.println("CORRECT");
    pinCorrect();												// Aktion für richtigen Pin 
  }
  
  else {
    Serial.println("WRONG");
    
    pinWrong();													     // Aktion für falschen Pin 
  }
  
  for (int i=0; i < PINLENGTH; i++) {
    keyBuffer[i] = '-'; 										// Nach Überprüfung Eingabe leeren
  }
}

void pinCorrect() {												  // Pin richtig
  tuerschloss.write(offen);
  digitalWrite(Led_gruen, HIGH);
  delay(openDelay);
  tuerschloss.write(geschlossen);
  digitalWrite(Led_gruen, LOW);
}
void pinWrong() {												    // Pin falsch
  for (int i=0; i<3; i++) {
    digitalWrite(Led_rot, HIGH);
    delay(1000);
    digitalWrite(Led_rot, LOW);  
    delay(1000);
    digitalWrite(Led_rot, HIGH);
    delay(500);
    digitalWrite(Led_rot, LOW);  
    delay(500);  
  } 
} 
