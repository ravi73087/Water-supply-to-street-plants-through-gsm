# Water-supply-to-street-plants-through-gsm

#include <SoftwareSerial.h>
SoftwareSerial mySerial(9,10);// rx,tx
char inchar; //Will hold the incoming character from the Serial Port.
int relay1=13;
int value;
int value1=7;
int sen = A0;  
int sensorValue = 0;  
int percent = 0;

void setup()

{
 pinMode(sen,INPUT);
 mySerial.begin(9600);   // Setting the baud rate of GSM Module 
 pinMode(relay1,OUTPUT);
 pinMode(value1,OUTPUT);
 Serial.begin(9600);    // Setting the baud rate of Serial Monitor (Arduino)
 digitalWrite(relay1,LOW);
 digitalWrite(value1,LOW);
 delay(100);

}
int convertToPercent(int value)
{
  int percentValue = 0;
  percentValue = map(value, 1023,0, 0, 100);
  return percentValue;
}

void printValuesToSerial()
{
  /*Serial.print("\n\nAnalog Value: ");
  Serial.print(sensorValue);
  */
  mySerial.print("\nPercent: ");
  mySerial.print(percent);
  mySerial.print("%");
}

void SendMessage()
{
  mySerial.println("AT+CMGF=1");    //Sets the GSM Module in Text Mode
 // Serial.println("AT+CMGF=1");
  delay(1000);  // Delay of 1000 milli seconds or 1 second
  mySerial.println("AT+CMGS=\"+918290769244\"\r"); // Replace x with mobile number
  //Serial.println("AT+CMGS=\"+918290769244\"\r");
  delay(1000);
  mySerial.println("low moisture detected.");// The SMS text you want to send
 // Serial.println("low moisture detected.");// The SMS text you want to send
  delay(100);
  mySerial.println((char)26);// ASCII code of CTRL+Z
  delay(1000);
}
void RecieveMessage()
{
  mySerial.println("AT+CNMI=2,2,0,0,0"); // AT Command to recieve a live SMS
  delay(1000);
}


void loop()

{
  sensorValue = analogRead(sen);
  percent = convertToPercent(sensorValue);
   
    
 
  
  delay(100);
   if (percent>=70&& value1==HIGH)
   {
      digitalWrite(relay1,LOW);
   }
   value1=digitalRead(value1);
   if ( percent<=35 && value1==LOW)
   {
      printValuesToSerial(); 
      SendMessage();
      digitalWrite(value1,HIGH);
      delay(1000);
   }
   if(mySerial.available() >0)
   {
    inchar=mySerial.read();
       if (inchar=='M')
        {
            delay(10);
            inchar=mySerial.read(); 
            //first led
            if (inchar=='O')//closed
             {
               delay(10);
               inchar=mySerial.read();
               if (inchar=='N')//closed
                   {
                    digitalWrite(relay1, HIGH);
                    delay(1000);
                    Serial.print("motor on\n");
                    delay(500);
                    mySerial.println("AT+CMGF=1");
                    delay(500);
                    mySerial.println("AT+CMGS=\"+918290769244\"\r");//Change the receiver phone number
                    delay(500);
                    mySerial.println("motor on");//the message you want to send
                    delay(500);
                    mySerial.println((char)26);
                    delay(500);
                    }
                       
               else if (inchar=='F')
                     {
                      digitalWrite(relay1, LOW);
                      delay(1000);
                      //digitalWrite(relay1, LOW);
                      mySerial.print("motor off\n");
                      delay(500);
                      mySerial.println("AT+CMGF=1");
                      delay(500);
                      mySerial.println("AT+CMGS=\"+918290769244\"\r");//Change the receiver phone number
                      delay(500);
                      Serial.println("motor off");    //the message you want to send
                      delay(500);
                      mySerial.println((char)26);
                      delay(500);
                      Serial.write(mySerial.read());
                      } 
                } else
                     {
                      mySerial.println("AT+CMGF=1");
                      delay(500);
                      mySerial.println("AT+CMGS=\"+918290769244\"\r");//Change the receiver phone number
                      delay(500);
                      Serial.println("INVALID CODE");    //the message you want to send
                      delay(500);
                      mySerial.println((char)26);
                      delay(500);
                      Serial.write(mySerial.read());
                      } 
               } else
                     {
                      mySerial.println("AT+CMGF=1");
                      delay(500);
                      mySerial.println("AT+CMGS=\"+918290769244\"\r");//Change the receiver phone number
                      delay(500);
                      Serial.println("INVALID CODE");    //the message you want to send
                      delay(500);
                      mySerial.println((char)26);
                      delay(500);
                      Serial.write(mySerial.read());
                      } 
              }
             }
