#include  <TimerOne.h>        
#include <IRremote.h>
#define irPin 11
IRrecv irrecv(irPin);
decode_results results;

volatile int i=0;               // Variable to use as a counter volatile as it is in an interrupt
volatile boolean zero_cross=0;  // Boolean to store a "switch" to tell us if we have crossed zero
int fan_pin = 7;                // Output to Opto Triac
int light_pin = 6;
int dim = 0;                    // Dimming level (0-128)  0 = on, 128 = 0ff
int inc=64; 

int limit = 256;
// counting up or down, 1=up, -1=down
boolean dt = 1;
int light = 0;
int freqStep = 75;    // This is the delay-per-brightness step in microseconds.
                      // For 60 Hz it should be 65
// It is calculated based on the frequency of your voltage supply (50Hz or 60Hz)
// and the number of brightness steps you want. 
// 
// Realize that there are 2 zerocrossing per cycle. This means
// zero crossing happens at 120Hz for a 60Hz supply or 100Hz for a 50Hz supply. 

// To calculate freqStep divide the length of one full half-wave of the power
// cycle (in microseconds) by the number of brightness steps. 
//
// (120 Hz=8333uS) / 128 brightness steps = 65 uS / brightness step
// (100Hz=10000uS) / 128 steps = 75uS/step

void setup() {
  // Begin setup
   irrecv.enableIRIn();
   Serial.begin(9600);
  pinMode(fan_pin, OUTPUT);                          // Set the Triac pin as output 
    pinMode(light_pin, OUTPUT);                          // Set the Triac pin as output 
  attachInterrupt(1, zero_cross_detect, FALLING);    // Attach an Interupt to Pin 2 (interupt 0) for Zero Cross Detection
  Timer1.initialize(freqStep);                      // Initialize TimerOne library for the freq we need
  Timer1.attachInterrupt(dim_check, freqStep);      

}
void remote()
{
     if (irrecv.decode(&results)) {

      switch (results.value) {
         case 0x1FE807F:{
            light=!light;
            Serial.print("light");
            Serial.println(light);
            break;
         }
                  
         case 0x1FE48B7:{
            dt=!dt;
            Serial.print("dt");
            Serial.println(dt);
            break;
         }
         case 0x1FE58A7:{
         dim=dim+inc;
         if((dim>=256))
         {
          dim=256;}
          Serial.print("dim:");
          Serial.println(dim);
         }
            break;

         case 0x1FE7887:
         {dim=dim-inc  ;
         if((dim<0))
         {
          dim=0;}
          Serial.print("dim:");
          Serial.println(dim);
         }
         break;

            
      }
   irrecv.resume();
   }
}

void zero_cross_detect() {    
  zero_cross = true;               // set the boolean to true to tell our dimming function that a zero cross has occured
  i=0;
  digitalWrite(fan_pin, LOW);       // turn off TRIAC (and AC)
  digitalWrite(light_pin, light);       // turn off TRIAC (and AC)
}                                 

// Turn on the TRIAC at the appropriate time
void dim_check() {
if(dt){
  if(zero_cross == true) {              
    if(i>=dim) {                     
      digitalWrite(fan_pin, HIGH); // turn on light       
      i=0;  // reset time step counter                         
      zero_cross = false; //reset zero cross detection
    } 
    else {
      i++; // increment time step counter                     
   }                                
  }      
 }  
}                                   


void loop()
{                        
  remote();
}


