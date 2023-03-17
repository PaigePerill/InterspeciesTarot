# InterspeciesTarot


**Boxset Making Instructions**


This documentation describes the making of a box for holding a deck of cards and a booklet. 
When the box opens, a Voice will describe the context of the object and why it needs to exist. 

The deck of cards and booklet are tied to my Project with Carolina called the Interpecies Tarot. It is a tarot deck that illustrates and poses the crucial questions we need to ask ourselves when working with other species to act more in alignment. By using the tarot format, we can go into detail about each concept card in the booklet, while the cards remain evocative images. This works well for the project because we can expand on complex subjects in written form including a mix of scientific concepts, poetic storytelling and references, yet stay engaging and playful.

Because the content is not entirely ready yet, the voice recording part of this project  (which was Daphne's idea) is a good addition, since it can be a placeholder until the content is ready (cards+booklet), yet still intrigue and inspire the audience. 

The inspiration for the booklet is the Dali Tarot Boxset: 

![](https://i.imgur.com/bCycgJm.jpg)



- [ ] Step 1: 

Define how the box will be made. 

I had orginally thought of laser cutting various pieces of plywood with the lasercutter and assembling them together, however Eduardo suggested I instead CNC the slots shapes into a large block of wood. We went to see the available wood in the scrap pile and found a thick piece of white oak wood that fit the brief perfectly. 



- [ ] Step 2: 
Sketch the file and get feedback. 
I sketched heavily on Eduardo's initial sugggestion drawing including a thin slot for the booklet, a deeper slot for the cards in the middle of the booklet slot and a round slot for the speaker in the top left. 

![](https://i.imgur.com/OlH0zeq.jpg)


- [ ] Step 3: 

Model the file. This is not not my skillset, so I asked Ahmed's help. We used Rhino to create a file. Operations included scaling, extruding and booolean differences. The end result had a different dimensionality that the sketches but made more sense.

![](https://i.imgur.com/97bLTpJ.jpg)


See file tarotbox.3dm



- [ ] Step 4: 
Transform the Rhino file into 2D for readability by the CNC Machine. Here Adai helped me to think through the logic of the machine and how to change the settings so the file would succesfully engrave when sent to the machine. 

![](https://i.imgur.com/X0UYdcm.jpg)




- [ ] Step 5: 
Cut out a piece of white oak from the larger piece. This we did with the circular saw. It was important to measure right angles properly so we could slot it in the machine well afterwards. 


- [ ] Step 6: 
Eduardo double checked the files and for the last part of the operation which is cutting out the final piece of wood from the larger block, he suggested we use a longer drill bit for the last operation (cuting out the rectangle our of the larger piece of wood) since drilled bits cannot go down more than half their lenght. We then transformed the files to G-Code format and sent them to the printer.

- [ ] Step 7: fix the Oak Piece on the CNC machine in between slots of plywood. Define origin for X Y and Z axes. 
![](https://i.imgur.com/7NbipgO.jpg)


- [ ] Step 8 : Launch job. There were 3 seperate jobs. 
The first one was the back side with the slot for electronics. The second one was the front side with the slots for the booklet, the cars and the microphone. The third one was cutting out the right size out of the larger block - and this is the one we has to change the drill bit. 

![](https://i.imgur.com/Ujp0hGR.jpg)



- [ ] Step 9: 
Sanding the edges and bridges and then thinking about the aesthetics. I had orginally thought I would create a book cover that woul be covered in bacteria-died silk, but in the end the colors did not match quite right so I decided to keep the box open and focus on the aesthetics of the booklet. 


- [ ] Step 10: Coding and Electronics 

Since I am working with Carolina on the Interspecies Tarot, and that her microchallenge group serendipitously had a similar code and electronics concept as mine, I drew on their work and got the help of Miguel, Daphne and Adai to wire the circuit and amend the code. 

The idea is this. When people remove the booklet from the boxset, a voice starts playing. This means we needed to conect a capacitive sensor to the ESP32 Feather as well as an SD card reader. 

The Wiring then look like this: 

(INSERT IMAGE)

And the code is the following: 


```
#include <DFRobotDFPlayerMini.h>

#include "Arduino.h"
//#include "SoftwareSerial.h"

const int Touch = 14; /*Touch pin defined*/
const int LED = LED_BUILTIN;  /*led output pin*/
const int threshold = 25;  /*threshold value set*/
int TouchVal;  /*store input value*/



#define RXD2 16
#define TXD2 17

HardwareSerial mySoftwareSerial(1); // RX, TX
//mySoftwareSerial(10, 11); // RX, TX
int touchState = 0;  // variable for reading the capacitive touch foil status

DFRobotDFPlayerMini myDFPlayer;
void printDetail(uint8_t type, int value);


void setup(){
  mySoftwareSerial.begin(9600, SERIAL_8N1, RXD2, TXD2);
  //Serial2.begin(9600, SERIAL_8N1, RXD2, TXD2);

  //Serial1.begin(9600, SERIAL_8N1, RXD2, TXD2);
  Serial.begin(115200);

  pinMode (Touch, INPUT);

  Serial.println();
  Serial.println(F("DFRobot DFPlayer Mini Demo"));
  Serial.println(F("Initializing DFPlayer ... (May take 3~5 seconds)"));



  if (!myDFPlayer.begin(mySoftwareSerial)) {  //Use softwareSerial to communicate with mp3.
    Serial.println(F("Unable to begin:"));
    Serial.println(F("1.Please recheck the connection!"));
    Serial.println(F("2.Please insert the SD card!"));
    while(true);
  }
  Serial.println(F("DFPlayer Mini online."));

  myDFPlayer.volume(30);  //Set volume value. From 0 to 30
  //myDFPlayer.playMp3Folder(1);
  //myDFPlayer.play(1);  //Play the first mp3
}


void loop(){
  TouchVal = touchRead(Touch); /*read touch pin value*/
  Serial.print(TouchVal);
  


  if(TouchVal > threshold){  /*if touch value is less than threshold LED ON*/
    digitalWrite(LED, HIGH);
    
    Serial.println(" - LED on / sound on");
  }
  else if (TouchVal < threshold){
    digitalWrite(LED, LOW);  /*else LED will remains OFF*/
    myDFPlayer.playMp3Folder(1);
    Serial.println(" - LED off / sound off");
  }
  if (myDFPlayer.available()) {
    printDetail(myDFPlayer.readType(), myDFPlayer.read()); //Print the detail message from DFPlayer to handle different errors and states.
  }



  delay(500);

}


/*
void loop()
{

  if (millis() - timer > 3000) {
    timer = millis();
    myDFPlayer.next();  //Play next mp3 every 3 second.
  }

  if (myDFPlayer.available()) {
    printDetail(myDFPlayer.readType(), myDFPlayer.read()); //Print the detail message from DFPlayer to handle different errors and states.
  }
}
*/


void printDetail(uint8_t type, int value){
  switch (type) {
    case TimeOut:
      Serial.println(F("Time Out!"));
      break;
    case WrongStack:
      Serial.println(F("Stack Wrong!"));
      break;
    case DFPlayerCardInserted:
      Serial.println(F("Card Inserted!"));
      break;
    case DFPlayerCardRemoved:
      Serial.println(F("Card Removed!"));
      break;
    case DFPlayerCardOnline:
      Serial.println(F("Card Online!"));
      break;
    case DFPlayerPlayFinished:
      Serial.print(F("Number:"));
      Serial.print(value);
      Serial.println(F(" Play Finished!"));
      break;
    case DFPlayerError:
      Serial.print(F("DFPlayerError:"));
      switch (value) {
        case Busy:
          Serial.println(F("Card not found"));
          break;
        case Sleeping:
          Serial.println(F("Sleeping"));
          break;
        case SerialWrongStack:
          Serial.println(F("Get Wrong Stack"));
          break;
        case CheckSumNotMatch:
          Serial.println(F("Check Sum Not Match"));
          break;
        case FileIndexOut:
          Serial.println(F("File Index Out of Bound"));
          break;
        case FileMismatch:
          Serial.println(F("Cannot Find File"));
          break;
        case Advertise:
          Serial.println(F("In Advertise"));
          break;
        default:
          break;
      }
      break;
    default:
      break;
  }
}
```







`
```
