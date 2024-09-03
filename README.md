# Arduino-based-Digital-Safe-Lock
task 3 code:


#include <Keypad.h>
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

// LCD Setup (address 0x27 for 16x2 LCD)
LiquidCrystal_I2C lcd(0x27, 16, 2);

// Keypad Setup
const byte ROWS = 4; // four rows
const byte COLS = 4; // four columns
char keys[ROWS][COLS] = {
  {'1', '2', '3', 'A'},
  {'4', '5', '6', 'B'},
  {'7', '8', '9', 'C'},
  {'*', '0', '#', 'D'}
};
byte rowPins[ROWS] = {2, 3, 4, 5}; // connect to the row pins of the keypad
byte colPins[COLS] = {6, 7, 8, 9}; // connect to the column pins of the keypad
Keypad keypad = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

const String correctPasscode = "1234"; // Predefined passcode
String enteredPasscode = ""; // To store the entered passcode
int attempts = 0; // Counter for failed attempts
const int maxAttempts = 3; // Maximum allowed attempts

void setup() {
  lcd.begin(16, 2); // Initialize the LCD with 16 columns and 2 rows
  lcd.backlight(); // Turn on the LCD backlight
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Enter Password:");
}

void loop() {
  char key = keypad.getKey();

  if (key) {
    if (key == '#') { // Submit passcode
      if (enteredPasscode == correctPasscode) {
        lcd.clear();
        lcd.setCursor(0, 0);
        lcd.print("Access Granted");
        delay(2000);
        lcd.clear();
        lcd.setCursor(0, 0);
        lcd.print("Enter Password:");
        enteredPasscode = "";
        attempts = 0; // Reset attempts on success
      } else {
        attempts++;
        if (attempts >= maxAttempts) {
          lcd.clear();
          lcd.setCursor(0, 0);
          lcd.print("Locked Out");
          delay(5000); // Lockout period
          attempts = 0; // Reset attempts after lockout period
          lcd.clear();
          lcd.setCursor(0, 0);
          lcd.print("Enter Password:");
        } else {
          lcd.clear();
          lcd.setCursor(0, 0);
          lcd.print("Access Denied");
          delay(2000);
          lcd.clear();
          lcd.setCursor(0, 0);
          lcd.print("Enter Password:");
          enteredPasscode = "";
        }
      }
    } else if (key == '*') { // Clear passcode
      enteredPasscode = "";
      lcd.clear();
      lcd.setCursor(0, 0);
      lcd.print("Enter Password:");
    } else { // Append entered digit
      enteredPasscode += key;
    }
  }
}
