// RGB LED pins
const int R_PIN = 10;
const int G_PIN = 11;
const int B_PIN = 9;

// Button pin
const int BUTTON_PIN = 2;

int mode = 0;
bool lastButtonState = HIGH;
bool currentButtonState;

// 呼吸燈用
int brightness = 0;
int fadeAmount = 5;

// 計時用（避免 delay 卡死）
unsigned long previousMillis = 0;
const long interval = 20;

void setup() {
  pinMode(R_PIN, OUTPUT);
  pinMode(G_PIN, OUTPUT);
  pinMode(B_PIN, OUTPUT);

  pinMode(BUTTON_PIN, INPUT);

  // 初始狀態：白光恆亮
  setColor(100, 100, 100);
}

void loop() {
  currentButtonState = digitalRead(BUTTON_PIN);

  // 偵測按鈕「按下瞬間」
  if (lastButtonState == HIGH && currentButtonState == LOW) {
    mode++;
    if (mode > 4) mode = 0;
    delay(200); // 防彈跳
  }
  lastButtonState = currentButtonState;

  switch (mode) {
    case 0:
      // 恆亮白光
      setColor(100, 100, 100);
      break;

    case 1:
      // 呼吸燈
      breathing();
      break;

    case 2:
      // RGB 循環
      rgbCycle();
      break;

    case 3:
      // 閃爍
      blink();
      break;

    case 4:
      // 全滅
      setColor(0, 0, 0);
      break;
  }
}

// =======================
// 功能函式
// =======================

// 共陽 RGB：數值越大越暗
void setColor(int r, int g, int b) {
  analogWrite(R_PIN, 255 - r);
  analogWrite(G_PIN, 255 - g);
  analogWrite(B_PIN, 255 - b);
}

void breathing() {
  unsigned long currentMillis = millis();
  if (currentMillis - previousMillis >= interval) {
    previousMillis = currentMillis;

    brightness += fadeAmount;
    if (brightness <= 0 || brightness >= 120) {
      fadeAmount = -fadeAmount;
    }
    setColor(brightness, brightness, brightness);
  }
}

void rgbCycle() {
  setColor(120, 0, 0);
  delay(300);
  setColor(0, 120, 0);
  delay(300);
  setColor(0, 0, 120);
  delay(300);
}

void blink() {
  setColor(120, 120, 120);
  delay(150);
  setColor(0, 0, 0);
  delay(150);
}
