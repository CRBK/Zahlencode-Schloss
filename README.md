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
* Das Programm wird seperat vorgestellt
