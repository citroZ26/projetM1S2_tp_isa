# TP ISA
Par Chaigne Hyacinthe, Delaby Léo, Monvoisin Anthony et Roussel Victorien

## Objectif : 
L'objectif de ce TP est d'initiatier au protocole de communication LoRaWAN et de montrer les possibilités qu'elle présente.

## Matériel :
### x1 Seeeduino LoRaWAN
<img src="https://user-images.githubusercontent.com/47259100/112843746-36e55400-90a3-11eb-9538-a5d5b874448c.png" width="200"/>

### x1 Capteur d'humidité et de température - Grove 
<img src="https://user-images.githubusercontent.com/47259100/112844018-8b88cf00-90a3-11eb-9da6-63b35a8d2a74.png" width=100/>

### x1 Câble USB - MicroUSB 
<img src="https://user-images.githubusercontent.com/47259100/112844170-ba06aa00-90a3-11eb-810c-76f5558a4347.png" width=100/>

## Etape 1 - Branchement
## Etape 2 - Téléchargement
Téléchargement d'Arduino IDE:
https://www.arduino.cc/en/software

Fichier -> Préférence -> URL de gestionnaire de cartes supplémentaires:
https://files.seeedstudio.com/arduino/package_seeeduino_boards_index.json

Bibliothèque DHT à inclure dans le dossier : 
* <a href="https://github.com/citroZ26/projetM1S2_tp_isa/blob/main/DHT.cpp">dht.h</a>
* <a href="">dht.cpp</a>

## Etape 3 - Code à copier dans l'Arduino IDE
```
#include <LoRaWan.h>
#include "DHT.h"

DHT dht;
unsigned char data[4] = {'x',1,'x',4};
char buffer[256];
 
void setup(void)
{     
    dht.setup(2);
 
    SerialUSB.begin(115200); 
    
    lora.init();
 
    memset(buffer, 0, 256);
    lora.getVersion(buffer, 256, 1);
    SerialUSB.print(buffer); 
 
    memset(buffer, 0, 256);
    lora.getId(buffer, 256, 1);
    SerialUSB.print(buffer);
 
    lora.setKey("2B7E151628AED2A6ABF7158809CF4F3C", "2B7E151628AED2A6ABF7158809CF4F3C", "2B7E151628AED2A6ABF7158809CF4F3C");
 
    lora.setDeciveMode(LWABP);
    lora.setDataRate(DR0, EU868);
 
    lora.setChannel(0, 867.7);
    lora.setChannel(1, 867.9);
    lora.setChannel(2, 868.8);
 
    lora.setReceiceWindowFirst(0, 867.7);
    lora.setReceiceWindowSecond(869.5, DR3);
 
    lora.setDutyCycle(false);
    lora.setJoinDutyCycle(false);
 
    lora.setPower(14);    
}
 
void loop(void)
{   
    bool result = false;
 
    delay(2000);
    int h = dht.getHumidity();
    int t = dht.getTemperature();
 
    data[0] = 20; //humidity
    data[1] = 80; //temperature
    result = lora.transferPacket(data, 2, 10);
 
    if(result)
    {
        short length;
        short rssi;
 
        memset(buffer, 0, 256);
        length = lora.receivePacket(buffer, 256, &rssi);
 
        if(length)
        {
            SerialUSB.print("Length is: ");
            SerialUSB.println(length);
            SerialUSB.print("RSSI is: ");
            SerialUSB.println(rssi);
            SerialUSB.print("Data is: ");
            for(unsigned char i = 0; i < length; i ++)
            {
                SerialUSB.print("0x");
                SerialUSB.print(buffer[i], HEX);
                SerialUSB.print(" ");
            }
            SerialUSB.println();
        }
    }
}
```
## Etape 4 - Televersement
Outil -> Type de carte -> Seeed SAMD (...) boards -> Seeduino LoRaWAN

Puis téléverser :

<img src="https://user-images.githubusercontent.com/47259100/112849612-87f84680-90a9-11eb-946d-8e21ca1c5eab.png" width="200"/>
