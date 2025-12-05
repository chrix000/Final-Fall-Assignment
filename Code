/*
  KY-040 Rotary Encoder — Interrupt Example
  ----------------------------------------
  This sketch uses an interrupt on the CLK pin (D2)
  for more reliable reading of the encoder at high speeds.
  The pushbutton is still read in the main loop.

  ⚠️ Note: The KY-040 is a low-cost mechanical encoder and may still
  skip or bounce at *very* high spin speeds. For most hobby uses,
  this interrupt approach is reliable enough.

  Wiring (KY-040 → Arduino Uno)
  - GND  → GND
  - +    → 5V
  - CLK  → D2 (interrupt pin)
  - DT   → D3
  - SW   → D4

  Serial Monitor: open at 9600 baud.
*/

const int PIN_CLK = 2;   // Encoder CLK (interrupt)
const int PIN_DT  = 3;   // Encoder DT
const int PIN_SW  = 4;   // Pushbutton

volatile int counter = 0;    // Updated inside ISR
int lastCLK;                 // Last state of CLK (used in ISR)

void setup() {
  pinMode(PIN_CLK, INPUT_PULLUP);
  pinMode(PIN_DT, INPUT_PULLUP);
  pinMode(PIN_SW, INPUT_PULLUP);

  Serial.begin(9600);

  lastCLK = digitalRead(PIN_CLK);
  // Attach interrupt to CLK pin, triggers on any change
  attachInterrupt(digitalPinToInterrupt(PIN_CLK), updateEncoder, CHANGE);
}

void loop() {
  // Print encoder position (copy volatile safely)
  static int lastCounter = 0;
  noInterrupts();
  int pos = counter;
  interrupts();

  if (pos != lastCounter) {
    Serial.print("Position: ");
    Serial.println(pos);
    lastCounter = pos;
  }

  // Read pushbutton
  if (digitalRead(PIN_SW) == LOW) {
    Serial.println("Button pressed");
    delay(200); // simple debounce
  }
}

// Interrupt Service Routine: called on every CLK change
void updateEncoder() {
  int currentCLK = digitalRead(PIN_CLK);
  int currentDT  = digitalRead(PIN_DT);

  if (currentCLK != lastCLK) { // only act on real changes
    if (currentDT != currentCLK) {
      counter++; // Clockwise
    } else {
      counter--; // Counter-clockwise
    }
  }
  lastCLK = currentCLK;
}
