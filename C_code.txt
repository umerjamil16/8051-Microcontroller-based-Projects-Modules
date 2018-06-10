//qaz umer jamil
#include<reg51.h>

//Function declarations
void cct_init(void);
void InitINT0(void);

//Function declarations
//void cct_init(void);
void delay(int);
void lcdinit_hello(void);
void writecmd(int);
void writedata(char);
void ReturnHome(void);

void delay(int);/Users/umer/Downloads/Chrome Downloads/NERC-1/CheckHbridge/CheckHbridge.ino
void lcdinit_clock(void);
void WriteCommandToLCD(int);
void WriteDataToLCD(char);
void ClearLCDScreen(void);
void InitTimer0(void);
void UpdateTimeCounters(void);
void DisplayTimeToLCD(unsigned int,unsigned int,unsigned int);
void mode0();
void mode1();
void mode3();
void mode4();
void display_highs_lcd( unsigned int);
void second( unsigned int  );   // Displays time in HH:MM:SS format
void displaymode4(  unsigned int  );


void UpdateTimeCountersformode4(void);
// Define Clock variables
unsigned int usecCounter = 0;
unsigned int msCounter   = 0;
unsigned int secCounter  = 0;
unsigned int minCounter  = 0;
unsigned int hrCounter   = 0;

unsigned int LED  = 0;
//*******************
//Pin description
/*
P2 is data bus
P1.0 is RS
P1.1 is E
*/
//********************

// Defines Pins
sbit RS = P1^2;
sbit E  = P1^3;
sbit P17 = P1^7;
//definng the inputs of conditional switches
sbit Ap = P1^1;
sbit Bp = P1^0;

//DEFINING BCDs pins

sbit A1 = P2^0;
sbit B1 = P2^1;
sbit C1 = P2^2;
sbit D1 = P2^3;

sbit A2 = P2^4;
sbit B2 = P2^5;
sbit C2 = P2^6;
sbit D2 = P2^7;
//BCD output functions - generics
void bcd1(int);
void bcd2(int);
//LAPS counter and reset pin
sbit reset = P3^2;

unsigned int a =0;
unsigned int b=0;
unsigned int c=0;
unsigned int d=0;
unsigned int e=0;unsigned int f=0;
unsigned int highs=0;
unsigned int lcd_dis=0;
unsigned int h=0;
// ***********************************************************
// Main program
//
void main(void)
{
	while(1){
	if(Ap==0 & Bp==0 ){mode0();}
	else if(Ap==0 & Bp==1 ){mode1();}
	else if(Ap==1 & Bp==0 ){mode3();}
	else if(Ap==1 & Bp==1 ){mode4();}
}


}

void mode4(){
  cct_init();                                     //Make all ports zero
  a = P3;
  P2=a;
     lcdinit_clock();              // Initilize LCD
      ClearLCDScreen();      // Move cursor to zero location and clear screen
//here
		if(Ap==0 & Bp==0 ){mode0();}
	else if(Ap==0 & Bp==1 ){mode1();}
	else if(Ap==1 & Bp==0 ){mode3();}
  // Display Hour

if (a==255) //means max
{
  /* code */
  //LCD: Alaram ON immediately
   lcdinit_hello(); 
   writedata('A');                                 //write
   writedata('l');                                 //write
   writedata('a');                                 //write
   writedata('r');                                 //write
   writedata('a');                                 //write
   writedata('m');                                 //write
   writedata(' ');                                 //write
   writedata('O');                                 //write
   writedata('n');                                 //write
  ReturnHome();   

}
else if (a>127)//below mid pint
{
  /* code */
  //LCD Alaram Off
  //SSD OFF
	 lcdinit_hello(); 
    writedata('A');                                 //write
   writedata('l');                                 //write
   writedata('a');                                 //write
   writedata('r');                                 //write
   writedata('a');                                 //write
   writedata('m');                                 //write
   writedata(' ');                                 //write
   writedata('O');                                 //write
   writedata('f');                                 //write
   writedata('f');                                 //write
   ReturnHome();    
}
else if (a<127) //above mid point
{
  /* code */

     InitTimer0();           // Start Timer0
      
  while(1)
  {
    if( msCounter == 0 )                                       // msCounter becomes zero after exact one sec
    {
      displaymode4( secCounter);   // Displays time in HH:MM:SS format
    }

    UpdateTimeCountersformode4();                                      // Update sec, min, hours counters
    }

}
while(1){}

}

//mode 3 starts
void mode3(){
  cct_init();                                     //Make all ports zero
	a = P3;
	P2=a;
	   lcdinit_clock();              // Initilize LCD

second( a );   // Displays time in HH:MM:SS format
while(1){}

}

//mode 3 ends


void UpdateTimeCountersformode4(void)
{
	if (msCounter==1000)
	{
		secCounter++;
		msCounter=0;
	}

	if(secCounter==30)
	{
		secCounter=0;
	}
	if (P17 == 1){
				secCounter=0;

	}

}

void displaymode4(  unsigned int s )   // Displays time in HH:MM:SS format
{
	ClearLCDScreen();      // Move cursor to zero location and clear screen

	//Display Seconds
	WriteDataToLCD( (s/10)+0x30 );
	WriteDataToLCD( (s%10)+0x30 );
}



//mode 1
void second( unsigned int h )   // Displays time in HH:MM:SS format
{
	ClearLCDScreen();      // Move cursor to zero location and clear screen

	// Display Hour
	h = 255-h;
	if(h<10){
		e= h*180;
	f = e/255;
	WriteDataToLCD( f+0x30 );
	}
	else{
		e= h*180;
	f = e/255;
	//suppose h = 255
	a = f/10; //25
	b= f% 10; //5
	c = a/10; //2
 	d = a%10; //5
	WriteDataToLCD( c+0x30 );
	WriteDataToLCD( d+0x30 );	//Display ':'
	WriteDataToLCD( b+0x30 );
	WriteDataToLCD(':');
}
}

void mode1(){

while(1){
while(T0){
  
 while(T0);
highs++;
  //display on ssd
  if (highs<10)
  {
    /* code */
  
     b=highs;a=0;
        bcd1(a);
    bcd2(b);
    }
    else{
			lcd_dis++;   
			b=0;a=0;
      bcd1(a);
			bcd2(b);
      
	
   lcdinit_hello();                                      //Initilize LCD
	if(lcd_dis==0){writedata('0');}
	else if(lcd_dis==1){writedata('1');}
	else if(lcd_dis==2){writedata('2');}
	else if(lcd_dis==3){writedata('3');}
	else if(lcd_dis==4){writedata('4');}
	else if(lcd_dis==5){writedata('5');}
	else if(lcd_dis==6){writedata('6');}
	else if(lcd_dis==7){writedata('7');}
	else if(lcd_dis==8){writedata('8');}
	else if(lcd_dis==9){writedata('9');}
	highs=0;
   ReturnHome();                                   //Return to 0 position

    }//else ends
}


}
      


    //clock ends
  while(1)
  {
  }

} //mode1 functions ends here
//mode 1 end

void display_highs_lcd(a){

}

//mode 0 func
void mode0(){
   cct_init();                                     //Make all ports zero

   lcdinit_hello();                                      //Initilize LCD

   writedata('S');                                 //write
   writedata('t');                                 //write
   writedata('o');                                 //write
   writedata('p');                                 //write
   writedata(' ');                                 //write
   writedata('W');                                 //write
   writedata('a');                                 //write
   writedata('t');                                 //write
   writedata('c');                                 //write
   writedata('h');                                 //write
   writedata(' ');                                 //write
   writedata('M');                                 //write
   writedata('o');                                 //write
   writedata('d');                                 //write
   writedata('e');                                 //write

   ReturnHome();                                   //Return to 0 position

//starting clock
   lcdinit_clock();              // Initilize LCD
   InitTimer0();           // Start Timer0
			
	while(1)
	{
		if( msCounter == 0 )                                       // msCounter becomes zero after exact one sec
		{
			DisplayTimeToLCD(hrCounter, minCounter, secCounter);   // Displays time in HH:MM:SS format
		}
//button "reset"detector
		/*
		if(reset == 1){
		// Define Clock variables
			usecCounter = 0;
			msCounter   = 0;
			secCounter  = 0;
			minCounter  = 0;
			hrCounter   = 0;
			
			//reset counter
			laps++;
}
		*/
		
		
		//LED int holds the number;
		//converting LED to deci;
		if(LED>9){
		a = LED /10;
		b = LED % 10;
		bcd1(a);
		bcd2(b);}
		else{
			b=LED;a=0;
				bcd1(a);
		bcd2(b);
		}
			
		
		InitINT0();      // Intialize INT0 interrupts
 
		UpdateTimeCounters();                                      // Update sec, min, hours counters
  	}

		//clock ends
	while(1)
	{
	}

} //mode0 functions ends here

//main functions ends here
void bcd1(int a){
	
	
	
	if(a==0){ A1=0; B1=0; C1=0; D1=0;}
  else if(a==1){	A1=0; B1=0; C1=0; D1= 1;}
  else if(a==2){	A1=0; B1=0; C1=1; D1=0;}
  else if(a==3){	A1=0; B1=0; C1=1; D1=1;}
  else if(a==4){	A1=0; B1=1; C1=0; D1=0;}
  else if(a==5){	A1=0; B1=1; C1=0; D1=1;}
  else if(a==6){	A1=0; B1=1; C1=1; D1=0;}
  else if(a==7){	A1=0; B1=1; C1=1; D1=1;}
  else if(a==8){	A1=1; B1=0; C1=0; D1=0;}
  else if(a==9){	A1=1; B1=0; C1=0; D1=1;}
}
void bcd2(int a){
	if(a==0){ A2=0; B2=0; C2=0; D2=0;}
  else if(a==1){	A2=0; B2=0; C2=0; D2= 1;}
  else if(a==2){	A2=0; B2=0; C2=1; D2=0;}
  else if(a==3){	A2=0; B2=0; C2=1; D2=1;}
  else if(a==4){	A2=0; B2=1; C2=0; D2=0;}
  else if(a==5){	A2=0; B2=1; C2=0; D2=1;}
  else if(a==6){	A2=0; B2=1; C2=1; D2=0;}
  else if(a==7){	A2=0; B2=1; C2=1; D2=1;}
  else if(a==8){	A2=1; B2=0; C2=0; D2=0;}
  else if(a==9){	A2=1; B2=0; C2=0; D2=1;}
}


void delay(int a)
{
   int i;
   for(i=0;i<a;i++);   //null statement
}

void writedata(char t)
{
   RS = 1;             // => RS = 1
   P0 = t;             //Data transfer
   E  = 1;             // => E = 1
   delay(150);
   E  = 0;             // => E = 0
   delay(150);
}


void writecmd(int z)
{
   RS = 0;             // => RS = 0
   P0 = z;             //Data transfer
   E  = 1;             // => E = 1
   delay(150);
   E  = 0;             // => E = 0
   delay(150);
}

void lcdinit_hello(void)
{
  ///////////// Reset process from datasheet /////////
     delay(15000);
   writecmd(0x30);
     delay(4500);
   writecmd(0x30);
     delay(300);
   writecmd(0x30);
     delay(650);
  /////////////////////////////////////////////////////
   writecmd(0x38);    //function set
   writecmd(0x0c);    //display on,cursor off,blink off
   writecmd(0x01);    //clear display
   writecmd(0x06);    //entry mode, set increment
}

void ReturnHome(void)     //Return to 0 location
{
  writecmd(0x02);
    delay(1500);
}


void InitTimer0(void)
{
	TMOD &= 0xF0;    // Clear 4bit field for timer0
	TMOD |= 0x02;    // Set timer0 in mode 2
	
	TH0 = 0x05;      // 250 usec reloading time
	TL0 = 0x05;      // First time value
	
	ET0 = 1;         // Enable Timer0 interrupts
	EA  = 1;         // Global interrupt enable
	
	TR0 = 1;         // Start Timer 0
}


void Timer0_ISR (void) interrupt 1     // It is called after every 250usec
{
	usecCounter = usecCounter + 250;   // Count 250 usec
	
	if(usecCounter==1000)              // 1000 usec means 1msec 
	{
		msCounter++;
		usecCounter = 0;
	}

	TF0 = 0;     // Clear the interrupt flag
}


void UpdateTimeCounters(void)
{
	if (msCounter==1000)
	{
		secCounter++;
		msCounter=0;
	}

	if(secCounter==60)
	{
		minCounter++;
		secCounter=0;
	}

	if(minCounter==60)
	{
		hrCounter++;
		minCounter=0;
	}

	if(hrCounter==24)
	{
		hrCounter = 0;
	}
}


void DisplayTimeToLCD( unsigned int h, unsigned int m, unsigned int s )   // Displays time in HH:MM:SS format
{
	ClearLCDScreen();      // Move cursor to zero location and clear screen

	// Display Hour
	WriteDataToLCD( (h/10)+0x30 );
	WriteDataToLCD( (h%10)+0x30 );

	//Display ':'
	WriteDataToLCD(':');

	//Display Minutes
	WriteDataToLCD( (m/10)+0x30 );
	WriteDataToLCD( (m%10)+0x30 );

	//Display ':'
	WriteDataToLCD(':');

	//Display Seconds
	WriteDataToLCD( (s/10)+0x30 );
	WriteDataToLCD( (s%10)+0x30 );
}



void WriteDataToLCD(char t)
{
   RS = 1;             // This is data

   P0 &= 0x0F;		   // Make P2.4 to P2.7 zero
   P0 |= (t&0xF0);     // Write Upper nibble of data

   E  = 1;             // => E = 1
   delay(150);
   E  = 0;             // => E = 0
   delay(150);

   P0 &= 0x0F;		   // Make P2.4 to P2.7 zero
   P0 |= ((t<<4)&0xF0);// Write Lower nibble of data

   E  = 1;             // => E = 1
   delay(150);
   E  = 0;             // => E = 0
   delay(150);
}


void WriteCommandToLCD(int z)
{
   RS = 0;             // This is command

   P0 &= 0x0F;		   // Make P2.4 to P2.7 zero
   P0 |= (z&0xF0);     // Write Upper nibble of data

   E  = 1;             // => E = 1
   delay(150);
   E  = 0;             // => E = 0
   delay(150);

   P0 &= 0x0F;		   // Make P2.4 to P2.7 zero
   P0 |= ((z<<4)&0xF0);// Write Lower nibble of data

   E  = 1;             // => E = 1
   delay(150);
   E  = 0;             // => E = 0
   delay(150);
}

void lcdinit_clock(void)
{
  ///////////// Reset process from datasheet /////////
     delay(15000);

	 P0 &= 0x0F;		   // Make P2.4 to P2.7 zero
	 P0 |= (0x30&0xF0);    // Write 0x3
	
	 E  = 1;               // => E = 1
	 delay(150);
	 E  = 0;               // => E = 0
	 delay(150);

     delay(4500);

	 P0 &= 0x0F;		   // Make P2.4 to P2.7 zero
	 P0 |= (0x30&0xF0);    // Write 0x3
	
	 E  = 1;               // => E = 1
	 delay(150);
	 E  = 0;               // => E = 0
	 delay(150);

     delay(300);

	 P0 &= 0x0F;		   // Make P2.4 to P2.7 zero
	 P0 |= (0x30&0xF0);    // Write 0x3
	
	 E  = 1;               // => E = 1
	 delay(150);
	 E  = 0;               // => E = 0
	 delay(150);

     delay(650);

	 P0 &= 0x0F;		   // Make P2.4 to P2.7 zero
	 P0 |= (0x20&0xF0);    // Write 0x2
	
	 E  = 1;               // => E = 1
	 delay(150);
	 E  = 0;               // => E = 0
	 delay(150);

	 delay(650);

  /////////////////////////////////////////////////////
   WriteCommandToLCD(0x28);    //function set
   WriteCommandToLCD(0x0c);    //display on,cursor off,blink off
   WriteCommandToLCD(0x01);    //clear display
   WriteCommandToLCD(0x06);    //entry mode, set increment
}

void ClearLCDScreen(void)
{
	WriteCommandToLCD(0x01);   // Clear screen command
	delay(1000);
}









void cct_init(void)
{
	P0 = 0x00;    // Make all pins zero
	P1 = 0x00;    // Make all pins zero
	P2 = 0x00;    // Make all pins zero
	P3 = 0x04;    // Make P3.2 (INT0) pin high only
}

// External INT0 pin interrupt init function
void InitINT0(void)
{
	IT0 = 1;      //Edge triggered interrupt mode (Neg Edge)
	EX0 = 1;      //Enable external interrupt INT0
	EA  = 1;      //Enable global interrupts
}

// INT0 ISR
void external0_isr(void) interrupt 0     
{
	LED ++;   // Toggle LED pin
			usecCounter = 0;
			msCounter   = 0;
			secCounter  = 0;
			minCounter  = 0;
			hrCounter   = 0;
			
} 
