Code 1/3: Emoji-Button and Display Code

// MAX7219 pin configuration
const int DATA_PIN = 12; // MOSI
const int CLK_PIN = 13;    // SCK
const int CS_PIN = 11;     // SS
// Button pin
const int button1Pin = 2;
const int button2Pin = 3;
const int button3Pin = 4;


// Emoji pattern
const byte happyEmoji[8] = {
  B00111100,
  B01000010,
  B10100101,
  B10000001,
  B10100101,
  B10011001,
  B01000010,
  B00111100
};
const byte sadEmoji[8] = {
B00111100,
  B01000010,
  B10100101,
  B10000001,
  B10011001,
  B10100101,
  B01000010,
  B00111100
};

const byte questionMarkEmoji[8] = {
  B00011000,
  B00100100,
  B00000100,
  B00001000,
  B00010000,
  B00010000,
  B00000000,
  B00010000
};


// MAX7219 register addresses
const byte MAX7219_REG_NOOP = 0x00;
const byte MAX7219_REG_DIGIT0 = 0x01;
const byte MAX7219_REG_DIGIT1 = 0x02;
const byte MAX7219_REG_DIGIT2 = 0x03;
const byte MAX7219_REG_DIGIT3 = 0x04;
const byte MAX7219_REG_DIGIT4 = 0x05;
const byte MAX7219_REG_DIGIT5 = 0x06;
const byte MAX7219_REG_DIGIT6 = 0x07;
const byte MAX7219_REG_DIGIT7 = 0x08;
const byte MAX7219_REG_DECODEMODE = 0x09;
const byte MAX7219_REG_INTENSITY = 0x0A;
const byte MAX7219_REG_SCANLIMIT = 0x0B;
const byte MAX7219_REG_SHUTDOWN = 0x0C;
const byte MAX7219_REG_DISPLAYTEST = 0x0F;
bool isDisplayOn = true;

void sendMax7219(byte reg, byte data) {
  digitalWrite(CS_PIN, LOW);
  shiftOut(DATA_PIN, CLK_PIN, MSBFIRST, reg);
  shiftOut(DATA_PIN, CLK_PIN, MSBFIRST, data);
  digitalWrite(CS_PIN, HIGH);
}

void setup() {
  // Set the pin modes
  pinMode(DATA_PIN, OUTPUT);
  pinMode(CLK_PIN, OUTPUT);
  pinMode(CS_PIN, OUTPUT);
  pinMode(button1Pin, INPUT_PULLUP);
  pinMode(button2Pin, INPUT_PULLUP);
  pinMode(button3Pin, INPUT_PULLUP);


  // Initialize the MAX7219 display
  sendMax7219(MAX7219_REG_SCANLIMIT, 7);
  sendMax7219(MAX7219_REG_DECODEMODE, 0);
  sendMax7219(MAX7219_REG_DISPLAYTEST, 0);
  sendMax7219(MAX7219_REG_INTENSITY, 8);

  // Turn on the display initially
  isDisplayOn = true;
  sendMax7219(MAX7219_REG_SHUTDOWN, 1);
  // Display the initial pattern
  displayPattern(happyEmoji);
  displayPattern(sadEmoji);
  displayPattern(questionMarkEmoji);
}

void loop() {
  // Check if the button is pressed
  if (digitalRead(button1Pin) == LOW) {
    delay(50);  // Debounce delay
    if (digitalRead(button1Pin) == LOW) {
      // Toggle the display state
      isDisplayOn = !isDisplayOn;
      if (isDisplayOn) {
        sendMax7219(MAX7219_REG_SHUTDOWN, 1);  // Turn on the display
        displayPattern(happyEmoji);
      } else {
        sendMax7219(MAX7219_REG_SHUTDOWN, 0);  // Turn off the display
      }
      delay(200);  // Delay to avoid rapid toggling
    }
  }

   // Check if the button is pressed
  if (digitalRead(button2Pin) == LOW) {
    delay(50);  // Debounce delay
    if (digitalRead(button2Pin) == LOW) {
      // Toggle the display state
      isDisplayOn = !isDisplayOn;
      if (isDisplayOn) {
        sendMax7219(MAX7219_REG_SHUTDOWN, 1);  // Turn on the display
        displayPattern(sadEmoji);
      } else {
        sendMax7219(MAX7219_REG_SHUTDOWN, 0);  // Turn off the display
      }
      delay(200);  // Delay to avoid rapid toggling
    }
  }

   // Check if the button is pressed
  if (digitalRead(button3Pin) == LOW) {
    delay(50);  // Debounce delay
    if (digitalRead(button3Pin) == LOW) {
      // Toggle the display state
      isDisplayOn = !isDisplayOn;
      if (isDisplayOn) {
        sendMax7219(MAX7219_REG_SHUTDOWN, 1);  // Turn on the display
        displayPattern(questionMarkEmoji);
      } else {
        sendMax7219(MAX7219_REG_SHUTDOWN, 0);  // Turn off the display
      }
      delay(200);  // Delay to avoid rapid toggling
    }
  }

}

void displayPattern(const byte* pattern) {
  for (int i = 0; i < 8; i++) {
    sendMax7219(MAX7219_REG_DIGIT0 + i, pattern[i]);
  }
}
