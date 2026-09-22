Summative Arduino Extension

Overview:
The skill I am building on is digital output. In class, I learned how to control an Arduino by making an LED flash off and on. I wanted to take this a step further by making the Arduino control something more interesting than just an LED. So, I decided to learn how to use a Piezo buzzer. I chose this because I was interested in how Arduino can combine electronics and programming to create something interactive. I also wanted to learn how different notes can be created using code.

Research:
The new components I used were a piezo buzzer and a keypad. I researched how to connect the piezo buzzer to an Arduino and learned that the tone() function can make the buzzer play different frequencies. Higher or lower frequencies create different musical notes.

Sources: ChatGPT and Arduino 

Process: 
First, I built the keypad password system. I programmed the Arduino so that the user could enter a password and press # to submit it. I also added * to clear the password.
Next, I added a buzzer so that it would make a short sound whenever a key was pressed. I then added two LEDs. The green LED turns on when the password is correct, while the red LED turns on when the password is incorrect.
One thing that did not work at first was getting both LEDs to work correctly. I discovered that the LEDs needed to be connected to separate Arduino pins and that each LED needed its own resistor. I changed the wiring so that the green LED was connected to pin 11 and the red LED was connected to pin 12.
I also added a changeable password feature. Pressing A starts password-change mode. The current password has to be entered first, and then the new password is entered twice so the Arduino can check that it matches.
Finally, I added a small fan that can be turned on after the correct password is entered. I learned that the fan needs to be controlled using a transistor and an external power source rather than being powered directly from an Arduino pin.

Final code:

#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);

const int buzzer = 8;

#define C4 262
#define D4 294
#define E4 330
#define F4 349
#define G4 392
#define A4 440
#define B4 494
#define C5 523

int melody[] = {
  C4, E4, G4, G4,
  A4, G4, E4, D4,
  C4, E4, G4, A4,
  G4, E4, D4, C4
};

int durations[] = {
  300, 300, 300, 500,
  300, 300, 400, 400,
  300, 300, 300, 500,
  300, 300, 400, 600
};

const int numberOfNotes = sizeof(melody) / sizeof(melody[0]);

String lyrics[] = {
  "We build and we",
  "dream together",
  "Lights glow in the",
  "digital weather",
  "One small idea",
  "starts to grow",
  "Press play and",
  "watch it go!"
};

const int numberOfLines = sizeof(lyrics) / sizeof(lyrics[0]);

void setup() {
  pinMode(buzzer, OUTPUT);

  lcd.init();
  lcd.backlight();

  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Arduino Song");
  lcd.setCursor(0, 1);
  lcd.print("Starting...");
  delay(1500);
}

void loop() {

  for (int i = 0; i < numberOfNotes; i++) {

    int lyricNumber = i / 2;

    if (lyricNumber < numberOfLines) {
      showLyrics(lyrics[lyricNumber]);
    }

    tone(buzzer, melody[i], durations[i]);

    delay(durations[i]);

    noTone(buzzer);
    delay(50);
  }

  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Song complete!");
  lcd.setCursor(0, 1);
  lcd.print("Replay...");

  delay(2000);
}

void showLyrics(String text) {

  lcd.clear();

  if (text.length() <= 16) {
    lcd.setCursor(0, 1);
    lcd.print(text);
    return;
  }

  String scrollingText = "                " + text + "                ";

  for (int position = 0;
       position <= scrollingText.length() - 16;
       position++) {

    lcd.setCursor(0, 1);
    lcd.print(scrollingText.substring(position, position + 16));

    delay(120);
  }
}

Result:
<img src="https://raw.githubusercontent.com/avaschwartz/avaschwartz/main/IMG_4491.jpeg" alt="OG Design">
<img src="https://raw.githubusercontent.com/avaschwartz/avaschwartz/main/IMG_4507.jpeg" alt="Final Design">

Changeable Password:
<video controls src="images/My Movie 1.mp4"></video>
