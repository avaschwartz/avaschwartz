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

#include <Keypad.h>

// -------------------------
// KEYPAD
// -------------------------

const byte ROWS = 4;
const byte COLS = 4;

char keys[ROWS][COLS] = {
  {'1', '2', '3', 'A'},
  {'4', '5', '6', 'B'},
  {'7', '8', '9', 'C'},
  {'*', '0', '#', 'D'}
};

byte rowPins[ROWS] = {9, 8, 7, 6};
byte colPins[COLS] = {5, 4, 3, 2};

Keypad keypad = Keypad(
  makeKeymap(keys),
  rowPins,
  colPins,
  ROWS,
  COLS
);

// -------------------------
// OUTPUT PINS
// -------------------------

const int greenLED = 11;
const int redLED = 12;
const int buzzerPin = 13;

// -------------------------
// PASSWORD
// -------------------------

String correctCode = "1234";
String enteredCode = "";

// -------------------------
// CHANGE PASSWORD MODE
// -------------------------

bool changingPassword = false;
bool checkingOldPassword = false;

// -------------------------
// SETUP
// -------------------------

void setup() {

  pinMode(greenLED, OUTPUT);
  pinMode(redLED, OUTPUT);
  pinMode(buzzerPin, OUTPUT);

  digitalWrite(greenLED, LOW);
  digitalWrite(redLED, LOW);
}

// -------------------------
// MAIN LOOP
// -------------------------

void loop() {

  char key = keypad.getKey();

  if (key) {

    // -------------------------
    // A = CHANGE PASSWORD
    // -------------------------

    if (key == 'A') {

      enteredCode = "";
      changingPassword = true;
      checkingOldPassword = true;

      // Beep to show change mode
      tone(buzzerPin, 600);
      delay(150);
      noTone(buzzerPin);

      return;
    }

    // -------------------------
    // CLEAR
    // -------------------------

    if (key == '*') {

      enteredCode = "";

      tone(buzzerPin, 300);
      delay(100);
      noTone(buzzerPin);

      return;
    }

    // -------------------------
    // ENTER
    // -------------------------

    if (key == '#') {

      // =========================
      // CHANGE PASSWORD MODE
      // =========================

      if (changingPassword) {

        // Check the old password
        if (checkingOldPassword) {

          if (enteredCode == correctCode) {

            // Old password correct
            checkingOldPassword = false;
            enteredCode = "";

            tone(buzzerPin, 1000);
            delay(150);
            noTone(buzzerPin);

          } else {

            // Old password incorrect
            tone(buzzerPin, 400);
            delay(200);
            noTone(buzzerPin);

            changingPassword = false;
            checkingOldPassword = false;
            enteredCode = "";
          }

        }

        // Save the new password
        else {

          if (enteredCode.length() > 0) {

            correctCode = enteredCode;

            // Success sound
            tone(buzzerPin, 1200);
            delay(150);
            noTone(buzzerPin);

            delay(100);

            tone(buzzerPin, 1500);
            delay(150);
            noTone(buzzerPin);
          }

          changingPassword = false;
          checkingOldPassword = false;
          enteredCode = "";
        }

        return;
      }

      // =========================
      // NORMAL PASSWORD CHECK
      // =========================

      if (enteredCode == correctCode) {

        // GREEN LED
        digitalWrite(greenLED, HIGH);
        digitalWrite(redLED, LOW);

        // Success beep
        tone(buzzerPin, 1200);
        delay(200);
        noTone(buzzerPin);

        delay(200);

        // PLAY SONG
        playSong();

        // Turn green LED off
        digitalWrite(greenLED, LOW);

      } else {

        // RED LED
        digitalWrite(redLED, HIGH);
        digitalWrite(greenLED, LOW);

        // Wrong password sound
        tone(buzzerPin, 400);
        delay(200);
        noTone(buzzerPin);

        delay(100);

        tone(buzzerPin, 400);
        delay(200);
        noTone(buzzerPin);

        delay(1000);

        digitalWrite(redLED, LOW);
      }

      enteredCode = "";
      return;
    }

    // -------------------------
    // NORMAL NUMBER KEY
    // -------------------------

    enteredCode += key;

    // Short beep for every key
    tone(buzzerPin, 800);
    delay(50);
    noTone(buzzerPin);
  }
}


// =====================================================
// SONG
// =====================================================

void playSong() {

  // This is an ORIGINAL example melody.
  // You can replace these notes with a melody
  // from your own composition.

  int melody[] = {
    262, 294, 330, 348,
    392, 349, 330, 294,
    262, 330, 392, 440
  };

  int noteLength[] = {
    300, 300, 300, 300,
    400, 300, 300, 300,
    400, 300, 300, 600
  };

  int numberOfNotes = 12;

  for (int i = 0; i < numberOfNotes; i++) {

    tone(buzzerPin, melody[i]);

    delay(noteLength[i]);

    noTone(buzzerPin);

    delay(50);
  }
}
Result:
<img src="https://raw.githubusercontent.com/avaschwartz/avaschwartz/main/IMG_4491.jpeg" alt="OG Design">
<img src="https://raw.githubusercontent.com/avaschwartz/avaschwartz/main/IMG_4507.jpeg" alt="Final Design">

Changeable Password:
<video controls src="images/My Movie 1.mp4"></video>
