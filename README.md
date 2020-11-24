#include <Adafruit_CircuitPlayground.h>
#include "pitches.h"  

#define SLOUCH_ANGLE  8.0
#define SLOUCH_TIME   5000
#define GRAVITY       9.80665
#define RAD2DEG       57.29579

float currentAngle;
float targetAngle;
unsigned long slouchStartTime; 
bool slouching;

int melody[] = {                            // specific notes in the melody
NOTE_E7, NOTE_E7, 0, NOTE_E7,
  0, NOTE_C7, NOTE_E7, 0,
  NOTE_G7, 0, 0,  0,
  NOTE_G6, 0, 0, 0,
 
  NOTE_C7, 0, 0, NOTE_G6,
  0, 0, NOTE_E6, 0,
  0, NOTE_A6, 0, NOTE_B6,
  0, NOTE_AS6, NOTE_A6, 0,
 
  NOTE_G6, NOTE_E7, NOTE_G7,
  NOTE_A7, 0, NOTE_F7, NOTE_G7,
  0, NOTE_E7, 0, NOTE_C7,
  NOTE_D7, NOTE_B6, 0, 0,
 
//  NOTE_C7, 0, 0, NOTE_G6,
//  0, 0, NOTE_E6, 0,
//  0, NOTE_A6, 0, NOTE_B6,
//  0, NOTE_AS6, NOTE_A6, 0,
// 
//  NOTE_G6, NOTE_E7, NOTE_G7,
//  NOTE_A7, 0, NOTE_F7, NOTE_G7,
//  0, NOTE_E7, 0, NOTE_C7,
//  NOTE_D7, NOTE_B6, 0, 0  
 };
int numNotes; // Number of notes in the melody

int noteDurations[] = {     // note durations
  12, 12, 12, 12,
  12, 12, 12, 12,
  12, 12, 12, 12,
  12, 12, 12, 12,
 
  12, 12, 12, 12,
  12, 12, 12, 12,
  12, 12, 12, 12,
  12, 12, 12, 12,
 
  9, 9, 9,
  12, 12, 12, 12,
  12, 12, 12, 12,
  12, 12, 12, 12,
 
  12, 12, 12, 12,
  12, 12, 12, 12,
  12, 12, 12, 12,
  12, 12, 12, 12,
 
  9, 9, 9,
  12, 12, 12, 12,
  12, 12, 12, 12,
  12, 12, 12, 12,
};


void setup(){
  Serial.begin(9600);
  CircuitPlayground.begin(); 
  targetAngle = 0;
  numNotes = sizeof(melody)/sizeof(int);  // number of notes we are playing
  }

  void loop(){

     currentAngle = RAD2DEG* asin(-CircuitPlayground.motionZ()/9.81);

     Serial.println(currentAngle);

    if ((CircuitPlayground.leftButton()) || (CircuitPlayground.rightButton())){
    targetAngle = currentAngle; 
    
     
  
  CircuitPlayground.playTone(900, 100);
   delay(50);
   CircuitPlayground.playTone(900, 100);
   delay(50);
//File myFile = CircuitPlayground.open("test.wav");
    }
//check for slouching 
    if(currentAngle - targetAngle > SLOUCH_ANGLE){
     if(!slouching) slouchStartTime = millis();
      slouching = true;
      } else {
       slouching = false;
      }
  
         
    if(slouching){
      //check how long weve been slouching
      if (millis() -  slouchStartTime > SLOUCH_TIME){
        //play sound
        for (int thisNote = 0; thisNote < numNotes; thisNote++  ) {
           // Lighting up all leds in red
      for (int i = 0; i < 10; i++) {
        CircuitPlayground.setPixelColor(i,255,0,0);
      }
          // play notes of the melody
      // to calculate the note duration, take one second divided by the note type.
            int noteDuration = 1000 / noteDurations[thisNote];
            CircuitPlayground.playTone(melody[thisNote], noteDuration);
        }

     }
    } else {
      // yellow leds when not slouching
      delay(200);
      for (int i = 0; i < 10; i++) {
      CircuitPlayground.setPixelColor(i,15,15,0);
      }
    }
  }
  
  
