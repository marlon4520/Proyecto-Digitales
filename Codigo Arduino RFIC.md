#include <SPI.h>    
#include <MFRC522.h>  

#define greenLed 7      
#define redLed   5      
#define yellowLed 6     

byte myCards[] = {0x03,0x48,0x32,0xFA,   //Tarjeta blanca
                  0xC2,0x0A,0x35,0x54};  //Llavero azul
                  
int successRead;      

byte dummy = 0x00;

byte readCard[4];      

#define SS_PIN 53
#define RST_PIN 9
MFRC522 mfrc522(SS_PIN, RST_PIN);	




void setup() {
 
  pinMode(greenLed,OUTPUT);  
  pinMode(redLed,OUTPUT);
  pinMode(yellowLed,OUTPUT);
 
  //Initializing everingthing
  
  Serial.begin(9600);	                          
  SPI.begin();                                  //Iniciamos protocolo SPI
  mfrc522.PCD_Init();                             
  mfrc522.PCD_SetAntennaGain(mfrc522.RxGain_max); 
   Serial.println("Visor de Eventos");
}


void loop () {
  do{
   
    digitalWrite(yellowLed,HIGH);
    delay(500);
    digitalWrite(yellowLed,LOW);
    delay(500);
    successRead = getID();
    
  } 
 
  
  while (!successRead);    //Esperando que haya una comunicación con la tarjeta
  
  if (readCard[0] == myCards[4] && readCard[1] == myCards[5] 
  && readCard[2] == myCards[6] && readCard[3] == myCards[7])              //checking for blue card
  {   
   
    Serial.println("Felicitaciones, avanzo al siguiente nivel");
    
   Success();
   
   for(int i = 0; i<4; i++) dummy = readCard[i];   // removing previous stored value from the readCard variable
   
   successRead = 0;
  }else if(readCard[0] == myCards[0] && readCard[1] == myCards[1] 
  && readCard[2] == myCards[2] && readCard[3] == myCards[3])            //checking for white card
  {
    Serial.println("Felicitaciones, avanzo al siguiente nivel");
    
    Success();     
    
    for(int i = 0; i<4; i++) dummy = readCard[i];  
  }
  else {
  
    Error();      //calling the error function
  }
}

int getID() {
  // Getting ready for Reading PICCs
  if ( ! mfrc522.PICC_IsNewCardPresent()) {
    return 0;
  }
  if ( ! mfrc522.PICC_ReadCardSerial()) {
    return 0;
  }

  Serial.println("");
  for (int i = 0; i < 4; i++) {  // 
    readCard[i] = mfrc522.uid.uidByte[i];
    Serial.print(readCard[i], HEX);
  }
  
  Serial.println("");
  mfrc522.PICC_HaltA(); 
  return 1;
}

void Success(){
  digitalWrite(greenLed,HIGH);
  delay(5000);
  digitalWrite(greenLed,LOW);
  delay(1000);
}

void Error(){
  Serial.println("Game Over"); 
  
    digitalWrite(redLed,HIGH);
    delay(10000);
    digitalWrite(redLed,LOW);
    delay(10000);
  
  
}
