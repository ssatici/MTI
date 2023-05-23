# Code 1/3: Emoji-Button and Display Code

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

# Code 2/3: Accelerometer and RGB

#include <Wire.h>

const int MPU_ADDR = 0x68; // I2C address of the MPU-6050

const int redPin = 9;
const int greenPin = 10;
const int bluePin = 11;

int16_t accelerometerX, accelerometerY, accelerometerZ;

void setup() {
  Wire.begin();  // Initialize I2C communication

  // Wake up the MPU-6050
  Wire.beginTransmission(MPU_ADDR);
  Wire.write(0x6B);  // PWR_MGMT_1 register
  Wire.write(0);     // Wake up the MPU-6050
  Wire.endTransmission(true);

  pinMode(redPin, OUTPUT);
  pinMode(greenPin, OUTPUT);
  pinMode(bluePin, OUTPUT);
}

void loop() {
  Wire.beginTransmission(MPU_ADDR);
  Wire.write(0x3B);  // Starting with register 0x3B (ACCEL_XOUT_H)
  Wire.endTransmission(false);
  Wire.requestFrom(MPU_ADDR, 6, true);  // Request a total of 6 registers

  accelerometerX = Wire.read() << 8 | Wire.read();
  accelerometerY = Wire.read() << 8 | Wire.read();
  accelerometerZ = Wire.read() << 8 | Wire.read();

  // Map the accelerometer values to the LED color range (0-255)
  int red = map(accelerometerX, -16384, 16384, 0, 255);
  int green = map(accelerometerY, -16384, 16384, 0, 255);
  int blue = map(accelerometerZ, -16384, 16384, 0, 255);

  analogWrite(redPin, red);
  analogWrite(greenPin, green);
  analogWrite(bluePin, blue);

  delay(100);  // Adjust delay time as needed
}


