# Thesis

#include <ShiftLCD.h>
#include <stdlib.h>
#include <math.h>
////////////////////////////////////////////////Keypad//////////////////////////////////////////////////////////
int keypressed = 0;
int key;
int keyboardPin = 1;    // Analog input pin that the keypad is attached to
int keyboardValue = 0;   // value read from the keyboard
//////////////////////////////////////////////////COUNTERSWITCH/////////////////////////////////////////////////
int switchPin = 3; // choose the input pin (sensor)
int val ; // variable for reading the pin status
int counter;
int currentState;
int previousState;

int motorSpeed=0;
int playstatus=0;
/////////////////////////////////////////////////input///////////////////////////////////////////////////
int i;
long turns = 0;
long grams = 0;
int gauge = 0;// int ni befor
//////////////////////////////////////////////////////MotorSETUP////////////////////////////////////////////////
int RPWM=10;
int LPWM=9;
// timer 0
int L_EN=11;
int R_EN=12;
/////////////////////////////////////////////////GuideSetup//////////////////
int sw1;
int sw2;
int ge=7;
int gd=8;
const int switchR=13;
const int switchL=2;
ShiftLCD lcd(4, 6, 5);

void setup() {
 lcd.begin(16, 2);
  lcd.clear();
  lcd.setCursor(5,0);
  lcd.print("Welcome");
  lcd.setCursor(7,1);
  lcd.print("to");
  delay(2000);
  lcd.clear();
  lcd.setCursor(1,0);
  lcd.print("Wire Repacking");
  lcd.setCursor(5,1);
  lcd.print("Machine");
  delay(2000);
  lcd.clear();
  lcd.setCursor(4,1);
  lcd.print("Loading...");
  delay(2000);
  lcd.clear();
  pinMode(switchPin,INPUT); ///////////////////IR sensor
//////
pinMode(LPWM,OUTPUT);
pinMode(RPWM,OUTPUT);
pinMode(L_EN,OUTPUT);
pinMode(R_EN,OUTPUT);
/////////////////////////////////////////////////GuideSetup//////////////////
pinMode (switchR,INPUT);
pinMode (switchL,INPUT);
pinMode (ge,OUTPUT);
pinMode (gd,OUTPUT);
}




void loop(){
  
    key=0;
    gauge=0;
    turns=0;
    grams=0;
    lcd.clear();

    enterGauge();
   
     switch(key) 
    {
    case 2:
    key=0;
    if(gauge<20){
      lcd.clear();
      lcd.setCursor(0,0);
      lcd.print("Out of Range");
     delay(1000);
    }
    else{
      enterGrams();  
      
      
    }
        
    break;
    }

  
}


void enterGrams(){
  
       lcd.clear();
       lcd.setCursor(0,0);
       lcd.print("Enter Grams:");
       while(key==0){
         readkeyboard();
         if(key>0){
            break;
          } 
        lcd.setCursor(0,1);
        grams = grams * 10 + (keypressed);
         if ((grams > 32767) || (grams<0))
          {
            lcd.print("Out of Range");
            gauge=0;
            delay(1000); 
            lcd.clear();
          }
          else{
              lcd.print(grams);
            }
      }
       if(key==1){
            lcd.clear();
            lcd.setCursor(0,0);
            lcd.print("Canceled");
            delay(1000);
          }
          else{
              if(gauge<20){//grams limit
                lcd.clear();
                lcd.setCursor(0,0);
                lcd.print("Out of Range");
                delay(1000);
                          }
              else{
                summary();
                
                  }
            }
}
void summary(){
 
              turns=0;
              lcd.clear();
              lcd.setCursor(0,0);
              lcd.print(gauge);
              lcd.print("wg ");
              lcd.print(grams);
              lcd.print("g");
              lcd.setCursor(0,1);
              lcd.print(" #-go,");
              lcd.print("*-cancel");
              lcd.setCursor(14,1);//* to cancel, # to proceed 
              key=0;
              readkeyboard();
                  if(key==2){
                  lcd.clear();
                  lcd.setCursor(0,0);
                  lcd.print("Turns * grams ");
                  lcd.setCursor(0,1);
                  int gaugeForCount=gauge;
                  turns=gramsTurns(grams,gauge);
                  
                  lcd.print(turns);
                  count(gaugeForCount);
                  }  
              
}
void enterGauge(){
    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print("Enter Gauge:");
   
    while(key==0){
     
      readkeyboard();
     
        if(key>0){
          break;
        }
      lcd.setCursor(0,1);
      gauge=gauge *10 + (keypressed);
       if ((gauge > 40) || (gauge<0)){
          lcd.print("Out of Range");
          gauge=0;
          delay(1000); 
          lcd.clear();
          break;
        }
        else{
          lcd.print(gauge);
          }}}
void readkeyboard(){
keyboardValue = 0; // read the keyboard value (0 - 1023)
 while (keyboardValue < 49){
   //do nothing until a key is pressed
 keyboardValue = analogRead(keyboardPin); 
 
}//end of do nothing till a key is pressed                           
//read the keyboard routine
   while (keyboardValue > 48) {
   keyboardValue = analogRead(keyboardPin); // read the value (0-1023)
   delay(250);
  if (keyboardValue <49){keyboardValue=0;}
  if ((keyboardValue >50) && (keyboardValue < 58)){keypressed = 1;}
  if ((keyboardValue >90) && (keyboardValue < 100)){keypressed = 2;}
  if ((keyboardValue >140) && (keyboardValue < 150)){keypressed = 3;}
  if ((keyboardValue >120) && (keyboardValue < 130)){keypressed = 4;}
  if ((keyboardValue >200) && (keyboardValue < 212)){keypressed = 5;}
  if ((keyboardValue >286) && (keyboardValue < 294)){keypressed = 6;}
  if ((keyboardValue >300) && (keyboardValue < 308)){keypressed = 7;}
  if ((keyboardValue >444) && (keyboardValue < 450)){keypressed = 8;}
  if ((keyboardValue >552) && (keyboardValue < 560)){keypressed = 9;}
  if ((keyboardValue >930) && (keyboardValue < 960)){keypressed = 0;}
  if ((keyboardValue >820) && (keyboardValue < 904)){key = 1;}//*cancel
  if ((keyboardValue >970) && (keyboardValue < 982)){key = 2;} //#ok
  if (keyboardValue >982){keyboardValue=0;}
  //NOTE: the values used above are all halfway between the value obtained with each keypress in previous test sketch 
   }//wait until key no longer being pressed before continuing
   
  }
 int gramsTurns(long gramsValue, long gaugeValue){
 int result=0;
    if(gaugeValue==20){
        result=gramsValue * 1;}
    else if(gaugeValue==21){
        result=gramsValue * 2;}
    else if(gaugeValue==22){
        result=gramsValue * 3;}
    else if(gaugeValue==23){
        result=gramsValue * 4;}
    else if(gaugeValue==24){
        result=gramsValue * 5;}
    else if(gaugeValue==25){
        result=gramsValue * 6;}
    else if(gaugeValue==26){
        result=gramsValue * 7;}
    else if(gaugeValue==27){
        result=gramsValue * 8;}
    else if(gaugeValue==28){
        result=gramsValue * 9;}
    else if(gaugeValue==29){
        result=gramsValue * 10;}
    else if(gaugeValue==30){
        result=gramsValue * 1;}
    else if(gaugeValue==31){
        result=gramsValue * 2;}
    else if(gaugeValue==32){
        result=gramsValue * 3;}
    else if(gaugeValue==33){
        result=gramsValue * 4;}
    else if(gaugeValue==34){
        result=gramsValue * 5;}
    else if(gaugeValue==35){
        result=gramsValue * 6;}
    else if(gaugeValue==36){
        result=gramsValue * 7;}
    else if(gaugeValue==37){
        result=gramsValue * 8;}
    else if(gaugeValue==38){
        result=gramsValue * 9;}
    else if(gaugeValue==39){
        result=gramsValue * 10;}
    else if(gaugeValue==40){
        result=gramsValue * 1;}
      return result;}

  class Thesis{ //Class named Thesis
  public: //Object under public can be access.
  void finishing(){ //Object named finishing
    while(1){
          sw1=digitalRead(switchR);// guide switching
          sw2=digitalRead(switchL);
                  if (sw1==LOW){
                  digitalWrite(ge,HIGH);}
                  else if (sw2==LOW){
                  digitalWrite(gd,LOW);
                  break;}}}
   void notify(){//Object named notify
        lcd.clear();
        lcd.setCursor(0,0);
        lcd.print("Finishing...");
        delay(2000);}
        
  void slowTofast(int gaugeForCount){
        if(((gaugeForCount>=33) && (gaugeForCount<=35)) && (motorSpeed<=40) ){
                motorSpeed++;
             delay(100);}
             else if((gaugeForCount >= 20) && (gaugeForCount<=34)){
             if(motorSpeed<=55){
             motorSpeed++;
             delay(100);}}
             keyboardValue = analogRead(keyboardPin);
            if ((keyboardValue >970) && (keyboardValue < 982)){
         motorSpeed=20;
         playstatus=1;
         digitalWrite(R_EN,HIGH);
         digitalWrite(L_EN,HIGH);
         analogWrite(RPWM,motorSpeed); //Motor turns on
         digitalWrite(gd,LOW);// turn on
          if (sw1==LOW){
          digitalWrite(ge,HIGH);
          }
          else if (sw2==LOW){
          digitalWrite(ge,LOW);
          //TEST
          }
          delay(1000);
         keyboardValue=0;
         delay(150);
          while(!(keyboardValue >970) && (keyboardValue < 982)){
            
            keyboardValue = analogRead(keyboardPin);
            delay(150);
            if((keyboardValue >970) && (keyboardValue < 982)){
              break;}}} //#pause
           playstatus=0;
           digitalWrite(R_EN,HIGH);
           digitalWrite(L_EN,HIGH);
           analogWrite(RPWM,motorSpeed); //Motor turns on
           }
  void pauseCounter(){
  }};
  void pauseplay(int pause){

      while(pause==1){
      }
          digitalWrite(gd,HIGH);// turn on
         
          if (sw1==LOW){
          digitalWrite(ge,HIGH);
          }
          else if (sw2==LOW){
          digitalWrite(ge,LOW);
          //TEST
          }
          delay(10);
}
void count(int gaugeForCount){
  Thesis myProcedure; //an intance of Object Thesis
  motorSpeed=5;
  counter=0;
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Processing...");
  delay(1000);
      while (counter<turns){
          sw1=digitalRead(switchR);// guide switching
          sw2=digitalRead(switchL);
          val = digitalRead(switchPin); // read input value
              if (val == HIGH) { // check if the input is HIGH (button released)
              if (currentState==0){
              currentState=1;
              counter=counter+1;
              lcd.clear();
                lcd.begin(16, 2);
                lcd.setCursor(0,0);
                lcd.print("Counter:");
                lcd.setCursor(0,1);
                lcd.print(counter);
              }}
              else currentState=0;
          if(counter != turns){
          myProcedure.slowTofast(gaugeForCount);
          pauseplay(playstatus);}
          else {
          digitalWrite(R_EN,LOW);
          digitalWrite(L_EN,LOW);
          analogWrite(RPWM,0);}   //Motor turns off
    }
       //Multitasking Print "Finishing while motor aligning process"
       myProcedure.notify(); //access the object notify and do its procedure
       myProcedure.finishing(); // access the object finishing and to its procedure
          lcd.setCursor(0,1);
          lcd.print("Done");//WHILE LOOP
          delay(2000);
}
              


