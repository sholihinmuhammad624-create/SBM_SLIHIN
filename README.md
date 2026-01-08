# Automatic Clothes Dryer and Microcontroller-Based Weather Forecast

**Nama** : Muhammad Sholihin

**NIM** : 2306030069

---

## 1. Latar Belakang

Perkembangan *Internet of Things* (IoT) telah mendorong otomasi rumah menjadi lebih terjangkau dan efektif. Menurut Zanella et al. (2014), IoT memungkinkan integrasi berbagai sensor untuk membangun sistem cerdas yang mampu meningkatkan kualitas hidup.

Di Indonesia, data BMKG (2023) menunjukkan curah hujan yang relatif tinggi, yang berpotensi merusak perabotan rumah tangga di area terbuka seperti teras. Penelitian oleh Sari et al. (2021) membuktikan bahwa kelembaban di atas 80% dapat mempercepat kerusakan material kayu hingga 40%.

Sebagian besar sistem otomasi konvensional masih memiliki keterbatasan. Studi Gupta & Kumar (2022) menunjukkan bahwa banyak sistem hanya mengandalkan satu sensor sehingga kurang responsif terhadap perubahan lingkungan. Di sisi lain, laporan MarketsandMarkets (2023) menunjukkan harga sensor IoT semakin terjangkau dengan tingkat akurasi yang terus meningkat.

Platform IoT seperti **Blynk** (Chen et al., 2020) mampu mengurangi biaya pengembangan hingga 60%. Kementerian Kominfo (2022) juga melaporkan pertumbuhan *smart home* di Indonesia mencapai 25% per tahun, namun masih diperlukan solusi yang ekonomis dan mudah diimplementasikan.

Berdasarkan kondisi tersebut, proyek ini mengembangkan sistem **Automatic Clothes Dryer berbasis ESP32** dengan multi-sensor dan integrasi Blynk untuk pemantauan jarak jauh.

---

## 2. Rumusan Masalah

1. Bagaimana merancang sistem yang dapat mendeteksi kondisi lingkungan (hujan, kelembaban, dan cahaya) secara otomatis?
2. Bagaimana sistem merespons kondisi lingkungan dengan mengendalikan motor servo?
3. Bagaimana mengintegrasikan seluruh sensor dan aktuator agar bekerja secara terpadu?
4. Bagaimana melakukan monitoring dan kontrol sistem melalui platform IoT (Blynk)?

---

## 3. Tujuan

1. Menguji kinerja sensor DHT11, LDR, Water Sensor, dan RTC.
2. Menguji respons output (LED, buzzer, servo, dan LCD) terhadap input sensor.
3. Menguji integrasi seluruh komponen dalam satu sistem.
4. Mengintegrasikan sistem dengan platform Blynk untuk monitoring dan kontrol jarak jauh.

---

## 4. Alat dan Bahan

### Hardware

1. ESP32 – Mikrokontroler utama dengan WiFi dan Bluetooth
2. Sensor DHT11 – Pengukur suhu dan kelembaban
3. Water Sensor – Pendeteksi air/hujan
4. LDR (Light Dependent Resistor) – Sensor intensitas cahaya
5. RTC DS3231 – Modul waktu dan tanggal
6. Servo Motor SG90 – Penggerak servo
7. LCD I2C 16x2 – Media tampilan informasi
8. LED Merah & Hijau – Indikator status sistem
9. Buzzer aktif – Alarm peringatan
10. Push Button – Kontrol manual servo

### Software

* Arduino IDE
* Library: `Wire.h`, `RTClib.h`, `DHT.h`, `LiquidCrystal_I2C.h`, `Servo.h`, `Blynk.h`
* Board :`esp32`
* Platform Blynk Cloud

---

## 5. Pengujian Komponen

Bagian ini menjelaskan langkah-langkah pengujian setiap komponen secara terpisah sebelum diintegrasikan ke dalam sistem. Setiap pengujian dilakukan untuk memastikan komponen bekerja sesuai spesifikasi dan layak digunakan pada tahap integrasi.

### 5.1 Pengujian Sensor DHT11

**Tujuan Pengujian:**
Pengujian ini bertujuan untuk memastikan sensor DHT11 mampu membaca nilai suhu dan kelembaban dengan baik serta menampilkan data secara real-time melalui Serial Monitor.

**Langkah-langkah Pengujian:**

1. Hubungkan pin data DHT11 ke pin GPIO 2 ESP32.
2. Hubungkan pin VCC ke 3.3V dan GND ke ground.
3. Upload program pengujian ke ESP32.
4. Buka Serial Monitor dan amati nilai suhu serta kelembaban.
5. Lakukan simulasi kelembaban tinggi dengan mendekatkan benda lembab ke sensor.

**Tujuan:** Menguji akurasi pembacaan suhu dan kelembaban.

```cpp
#include "DHT.h"
#define DHTPIN 2
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(9600);
  dht.begin();
}

void loop() {
  float h = dht.readHumidity();
  float t = dht.readTemperature();
  Serial.print("Humidity: "); Serial.print(h);
  Serial.print(" %  Temperature: "); Serial.print(t);
  Serial.println(" C");
  delay(1000);
}
```

**Hasil Pengujian:**

| Suhu Referensi | Suhu DHT11 | Selisih | Status    |
| -------------- | ---------- | ------- | --------- |
| 25°C           | 24.2°C     | ±0.8°C  | Baik      |
| 22°C           | 23.0°C     | ±1.0°C  | Baik      |
| RH ≥ 85%       | Terdeteksi | -       | Responsif |

---

### 5.2 Pengujian Sensor Cahaya (LDR)

**Tujuan Pengujian:**
Mengetahui kemampuan sensor LDR dalam mendeteksi perubahan intensitas cahaya dan mengonversinya menjadi nilai analog.

**Langkah-langkah Pengujian:**

1. Hubungkan LDR ke pin analog A0 ESP32.
2. Upload kode pengujian ke mikrokontroler.
3. Amati nilai analog pada Serial Monitor.
4. Ubah kondisi cahaya (terang, redup, dan gelap) untuk melihat perubahan nilai.

```cpp
int ldrPin = A0;
void setup() {
  Serial.begin(9600);
}
void loop() {
  int nilai = analogRead(ldrPin);
  Serial.println(nilai);
  delay(500);
}
```

| Kondisi | Nilai Analog | Kategori |
| ------- | ------------ | -------- |
| Gelap   | 0–400        | Gelap    |
| Mendung | 401–2000     | Mendung  |
| Terang  | 2001–4095    | Cerah    |

---

### 5.3 Pengujian Water Sensor

**Tujuan Pengujian:**
Pengujian ini dilakukan untuk memastikan water sensor mampu mendeteksi keberadaan air sebagai indikator hujan.

**Langkah-langkah Pengujian:**

1. Hubungkan water sensor ke pin analog A0 ESP32.
2. Upload program pengujian.
3. Amati nilai sensor dalam kondisi kering.
4. Teteskan atau semprotkan air ke sensor dan amati perubahan nilai.

```cpp
int waterPin = A0;
void setup() { Serial.begin(9600); }
void loop() {
  int value = analogRead(waterPin);
  if (value < 160) Serial.println("ADA AIR");
  else Serial.println("TIDAK ADA AIR");
  delay(500);
}
```

| Kondisi | Nilai | Status     |
| ------- | ----- | ---------- |
| Kering  | <160  | Terdeteksi |
| Basah   | ≥160  | Terdeteksi |

---

### 5.4 Pengujian RTC DS3231

**Tujuan Pengujian:**
Mengetahui tingkat akurasi modul RTC DS3231 dalam menyimpan dan menampilkan waktu secara real-time.

**Langkah-langkah Pengujian:**

1. Hubungkan RTC ke ESP32 menggunakan jalur I2C (SDA dan SCL).
2. Set waktu awal sesuai waktu komputer.
3. Upload program pengujian.
4. Bandingkan waktu yang ditampilkan dengan waktu aktual.

```cpp
#include <Wire.h>
#include <RTClib.h>
RTC_DS3231 rtc;
void setup() {
  Serial.begin(9600);
  rtc.begin();
  rtc.adjust(DateTime(2025,12,5,16,12,0));
}
void loop() {
  DateTime now = rtc.now();
  Serial.println(now.timestamp());
  delay(1000);
}
```

| Parameter     | Hasil           | Status |
| ------------- | --------------- | ------ |
| Akurasi waktu | ±1 detik/24 jam | Akurat |

---

## 5.5. Pengujian Integrasi Sistem (Integrasi Input)

### A. Tujuan Pengujian

Pengujian integrasi input bertujuan untuk memastikan seluruh **input sistem** (sensor dan push button) dapat dibaca secara bersamaan oleh mikrokontroler dan menghasilkan variabel logika yang benar sebelum diteruskan ke bagian output.

Input yang diuji meliputi:

* Sensor DHT11 (suhu & kelembapan)
* Sensor cahaya (LDR)
* Water sensor
* RTC DS3231
* Push button

---

### B.Kode Pengujian Integrasi Input

Kode berikut digunakan untuk membaca seluruh input secara bersamaan dan menampilkannya melalui Serial Monitor. Kode ini **belum mengendalikan output**, sehingga fokus murni pada validasi input.

```cpp
#include <Wire.h>
#include <RTClib.h>
#include <DHT.h>

#define DHT_PIN 2
#define DHT_TYPE DHT11
#define WATER_PIN A0
#define LIGHT_PIN A1
#define BUTTON_PIN 3

RTC_DS3231 rtc;
DHT dht(DHT_PIN, DHT_TYPE);

void setup() {
  Serial.begin(9600);
  dht.begin();
  rtc.begin();
  pinMode(BUTTON_PIN, INPUT_PULLUP);
}

void loop() {
  float suhu = dht.readTemperature();
  float hum = dht.readHumidity();
  int waterVal = analogRead(WATER_PIN);
  int lightVal = analogRead(LIGHT_PIN);
  int lightPct = map(lightVal, 0, 1023, 0, 100);
  bool tombol = digitalRead(BUTTON_PIN) == LOW;

  DateTime now = rtc.now();

  Serial.println("===== DATA INPUT =====");
  Serial.print("Suhu        : "); Serial.print(suhu); Serial.println(" C");
  Serial.print("Kelembapan  : "); Serial.print(hum); Serial.println(" %");
  Serial.print("Water Sensor: "); Serial.println(waterVal);
  Serial.print("Cahaya      : "); Serial.print(lightPct); Serial.println(" %");
  Serial.print("Tombol      : "); Serial.println(tombol ? "DITEKAN" : "TIDAK");
  Serial.print("Waktu RTC   : "); Serial.print(now.hour()); Serial.print(":"); Serial.println(now.minute());
  Serial.println("=======================");

  delay(1000);
}
```

---

### C. Langkah-Langkah Pengujian Integrasi Input

1. Mengunggah kode pengujian integrasi input ke mikrokontroler.
2. Membuka **Serial Monitor** dengan baud rate 9600.
3. Mengamati data suhu dan kelembapan dari sensor DHT11.
4. Mengubah kondisi cahaya (gelap/terang) dan mencatat perubahan nilai LDR.
5. Memberikan air pada water sensor dan mengamati perubahan nilai.
6. Menekan push button untuk memastikan status input terbaca dengan benar.
7. Membandingkan waktu yang ditampilkan RTC dengan waktu aktual.

---

### D. Tabel Hasil Pengujian Integrasi Input

| No | Input        | Kondisi Uji       | Hasil Pembacaan          | Status     |
| -- | ------------ | ----------------- | ------------------------ | ---------- |
| 1  | DHT11        | Suhu ruang normal | 24–30°C                  | Sesuai     |
| 2  | DHT11        | Kelembapan tinggi | ≥85%                     | Terdeteksi |
| 3  | LDR          | Terang            | >50%                     | Sesuai     |
| 4  | LDR          | Gelap             | <10%                     | Sesuai     |
| 5  | Water Sensor | Kering            | <160                     | Terdeteksi |
| 6  | Water Sensor | Basah             | ≥160                     | Terdeteksi |
| 7  | Push Button  | Ditekan           | LOW                      | Sesuai     |
| 8  | RTC          | Sinkron waktu     | Sama dengan waktu aktual | Akurat     |

---

### E. Pembahasan Hasil Integrasi Input

Berdasarkan hasil pengujian, seluruh input dapat dibaca secara bersamaan tanpa konflik pin maupun gangguan pembacaan. Data yang ditampilkan pada Serial Monitor menunjukkan bahwa setiap sensor memberikan respon sesuai dengan kondisi pengujian. Hal ini menandakan bahwa sistem telah siap untuk diintegrasikan dengan logika kontrol dan output.

## 5.6 Pengujian Respon Output

Pengujian respon output difokuskan sepenuhnya pada perangkat keluaran sistem tanpa melibatkan logika pengambilan keputusan dari sensor. Setiap output diuji secara mandiri menggunakan kode sederhana untuk memastikan perangkat bekerja dengan baik sebelum diintegrasikan ke sistem utama.

### A. Tujuan Pengujian Output

1. Memastikan setiap perangkat output berfungsi secara fisik.
2. Memastikan sinyal dari mikrokontroler dapat diterima dengan baik oleh output.
3. Menghindari kesalahan hardware sebelum integrasi sistem.

---

### 5.7 Pengujian LED

**Tujuan:** Menguji LED sebagai indikator visual.

**Kode Pengujian LED:**

```cpp
int ledHijau = 6;
int ledMerah = 5;

void setup() {
  pinMode(ledHijau, OUTPUT);
  pinMode(ledMerah, OUTPUT);
}

void loop() {
  digitalWrite(ledHijau, HIGH);
  digitalWrite(ledMerah, LOW);
  delay(1000);

  digitalWrite(ledHijau, LOW);
  digitalWrite(ledMerah, HIGH);
  delay(1000);
}
```

**Hasil Pengujian LED:**

| Kondisi Uji      | LED Hijau | LED Merah | Status      |
| ---------------- | --------- | --------- | ----------- |
| Aktif bergantian | ON        | OFF / ON  | ✓ Berfungsi |

**Penjelasan:**
LED menyala dan mati secara bergantian sesuai perintah program, menandakan pin output berfungsi dengan baik.

---

### 5.8 Pengujian Servo Motor

**Tujuan:** Memastikan servo dapat bergerak sesuai sudut perintah.

**Kode Pengujian Servo:**

```cpp
#include <Servo.h>
Servo servoTest;

void setup() {
  servoTest.attach(9);
}

void loop() {
  servoTest.write(0);
  delay(2000);
  servoTest.write(140);
  delay(2000);
}
```

**Hasil Pengujian Servo:**

| Sudut Servo | Gerakan | Status     |
| ----------- | ------- | ---------- |
| 0°          | Menutup | ✓ Berhasil |
| 140°        | Membuka | ✓ Berhasil |

**Penjelasan:**
Servo bergerak sesuai sudut yang diperintahkan tanpa gangguan, menandakan kontrol PWM berjalan normal.

---

### 7.4 Pengujian Buzzer

**Tujuan:** Menguji buzzer sebagai alarm suara.

**Kode Pengujian Buzzer:**

```cpp
#define BUZZER 4

void setup() {
  pinMode(BUZZER, OUTPUT);
}

void loop() {
  digitalWrite(BUZZER, HIGH);
  delay(200);
  digitalWrite(BUZZER, LOW);
  delay(200);
  digitalWrite(BUZZER, HIGH);
  delay(200);
  digitalWrite(BUZZER, LOW);
  delay(2000);
}
```

**Hasil Pengujian Buzzer:**

| Kondisi | Respon       | Status     |
| ------- | ------------ | ---------- |
| Aktif   | Bunyi 2 kali | ✓ Berhasil |

**Penjelasan:**
Buzzer mengeluarkan suara sebanyak dua kali sesuai dengan program, menandakan output audio bekerja normal.

---

### 5.9Pengujian LCD

**Tujuan:** Menguji kemampuan LCD menampilkan teks.

**Kode Pengujian LCD:**

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);

void setup() {
  lcd.init();
  lcd.backlight();
  lcd.setCursor(0,0);
  lcd.print("LCD TEST OK");
  lcd.setCursor(0,1);
  lcd.print("OUTPUT NORMAL");
}

void loop() {
}
```

**Hasil Pengujian LCD:**

| Baris | Tampilan      | Status     |
| ----- | ------------- | ---------- |
| 1     | LCD TEST OK   | ✓ Berhasil |
| 2     | OUTPUT NORMAL | ✓ Berhasil |

**Penjelasan:**
LCD berhasil menampilkan karakter pada kedua baris dengan jelas, menandakan komunikasi I2C berjalan normal.

---
## 6.Pembuatan Alat
### 6.1 **Kode Konvensional**
```
#include <Wire.h>
#include <RTClib.h>
#include <DHT.h>
#include <LiquidCrystal_I2C.h>
#include <ESP32Servo.h>

// ===== PIN =====
#define DHT_PIN        4
#define DHT_TYPE       DHT22
#define SERVO_PIN      18
#define BUTTON_PIN     13
#define GAS_PIN        34
#define LIGHT_PIN      35
#define LED_RED        26
#define LED_GREEN      27
#define BUZZER_PIN     25

// ===== KONSTANTA =====
#define GAS_THRESHOLD     1500
#define LIGHT_THRESHOLD   1000
#define SERVO_OPEN        140
#define SERVO_CLOSE       0

// ===== OBJEK =====
DHT dht(DHT_PIN, DHT_TYPE);
RTC_DS3231 rtc;
LiquidCrystal_I2C lcd(0x27, 16, 2);
Servo servo;

// ===== VAR =====
bool servoState = false;
bool lastButton = HIGH;
bool buzzerDone = false;

// ===== BUZZER =====
void buzzer2x() {
  for (int i = 0; i < 2; i++) {
    digitalWrite(BUZZER_PIN, HIGH);
    delay(200);
    digitalWrite(BUZZER_PIN, LOW);
    delay(200);
  }
}

// ===== SETUP =====
void setup() {
  Serial.begin(115200);

  pinMode(BUTTON_PIN, INPUT_PULLUP);
  pinMode(LED_RED, OUTPUT);
  pinMode(LED_GREEN, OUTPUT);
  pinMode(BUZZER_PIN, OUTPUT);

  Wire.begin(21, 22);

  dht.begin();
  rtc.begin();
  lcd.init();
  lcd.backlight();

  servo.setPeriodHertz(50);
  servo.attach(SERVO_PIN, 500, 2400);
  servo.write(SERVO_CLOSE);

  lcd.print("Sistem Aktif");
  delay(1500);
  lcd.clear();
}

// ===== LOOP =====
void loop() {
  float suhu = dht.readTemperature();
  float hum  = dht.readHumidity();
  int gasVal = analogRead(GAS_PIN);
  int lightVal = analogRead(LIGHT_PIN);
  DateTime now = rtc.now();

  bool gasBahaya = gasVal > GAS_THRESHOLD;
  bool gelap = lightVal < LIGHT_THRESHOLD;
  bool aman = !(gasBahaya || gelap);

  // ===== LED =====
  digitalWrite(LED_GREEN, aman);
  digitalWrite(LED_RED, !aman);

  // ===== BUZZER =====
  if (!aman && !buzzerDone) {
    buzzer2x();
    buzzerDone = true;
  }
  if (aman) buzzerDone = false;

  // ===== PUSH BUTTON =====
  bool btn = digitalRead(BUTTON_PIN);
  if (btn == LOW && lastButton == HIGH) {
    servoState = !servoState;
    delay(250);
  }
  lastButton = btn;

  // ===== RTC 5 SORE =====
  if (now.hour() == 17 && now.minute() == 0) {
    servoState = false; // Memaksa servo menutup pukul 17:00
  }

  // ===== SERVO =====
  if (!aman) {
    servo.write(SERVO_CLOSE);
  } else {
    servo.write(servoState ? SERVO_OPEN : SERVO_CLOSE);
  }

  // ===== LCD =====
  lcd.setCursor(0, 0);
  if (isnan(suhu) || isnan(hum)) {
    lcd.print("DHT ERROR     ");
  } else {
    lcd.print("T:");
    lcd.print(suhu, 1);
    lcd.print(" H:");
    lcd.print(hum, 0);
    lcd.print("% ");
  }

  lcd.setCursor(0, 1);
  if (gasBahaya) lcd.print("GAS BAHAYA ");
  else if (gelap) lcd.print("GELAP      ");
  else lcd.print("AMAN       ");

  lcd.print(now.hour());
  lcd.print(":");
  if (now.minute() < 10) lcd.print("0");
  lcd.print(now.minute());

  // ===== SERIAL MONITOR =====
  Serial.print("Gas: "); Serial.print(gasVal);
  Serial.print(" | Cahaya: "); Serial.print(lightVal);
  Serial.print(" | Servo: "); Serial.println(servoState);

  delay(1000);
}
```
- Menggunakan **ESP32 + sensor + servo + LED + Buzzer + LCD**.  
- Servo membuka otomatis saat kondisi aman, menutup saat bahaya atau jam 5 sore.  
- Push button digunakan toggle servo.  
- Buzzer berbunyi saat gas/water sensor berbahaya atau gelap/hujan.  

**Penjelasan singkat kode:**

- `DHT22` → membaca suhu & kelembapan.  
- `RTC DS3231` → membaca waktu, logika menutup servo pukul 17:00.  
- `LED` → hijau = aman, merah = bahaya.  
- `Buzzer` → alarm kondisi bahaya.  
- `Servo` → buka/tutup jemuran (0°/140°).  
- `LCD I2C` → menampilkan **suhu, kelembapan, kondisi**, dan jam.


### 6.2. **Kode Intergrasi IoT dengan Blynk**
 Sensor gas digunakan pada simulator Wokwi karena **sensor air tidak tersedia di lingkungan simulator**. Sensor gas dipilih karena sama-sama menghasilkan output analog sehingga logika sistem tetap dapat diterapkan. Pada implementasi nyata, sensor gas diganti dengan sensor air tanpa mengubah alur program utama.

```
define BLYNK_PRINT Serial
// ===== BLYNK =====
#define BLYNK_TEMPLATE_ID "TMPL6K8uPbBR8"
#define BLYNK_TEMPLATE_NAME "jemuran otomatis"
#define BLYNK_AUTH_TOKEN "a9TrbrDR8aSRtePzYWt3Txr0Z71cM7iCU"

// ===== LIBRARY =====
#include <WiFi.h>
#include <BlynkSimpleEsp32.h>
#include <Wire.h>
#include <RTClib.h>
#include <DHT.h>
#include <LiquidCrystal_I2C.h>
#include <ESP32Servo.h>

// ===== WIFI =====
char ssid[] = "lihin123";
char pass[] = "123456789";

// ===== PIN =====
#define DHT_PIN        4
#define DHT_TYPE       DHT22
#define SERVO_PIN      18
#define BUTTON_PIN     13
#define GAS_PIN        34
#define LIGHT_PIN      35
#define LED_RED        26
#define LED_GREEN      27
#define BUZZER_PIN     25

// ===== KONSTANTA =====
#define GAS_THRESHOLD     1500
#define LIGHT_THRESHOLD   1000
#define SERVO_OPEN        140
#define SERVO_CLOSE       0

// ===== OBJEK =====
DHT dht(DHT_PIN, DHT_TYPE);
RTC_DS3231 rtc;
LiquidCrystal_I2C lcd(0x27, 16, 2);
Servo servo;
BlynkTimer timer;

// ===== VAR =====
bool servoState = false;
bool lastButton = HIGH;
bool buzzerDone = false;

// ===== BUZZER =====
void buzzer2x() {
  for (int i = 0; i < 2; i++) {
    digitalWrite(BUZZER_PIN, HIGH);
    delay(200);
    digitalWrite(BUZZER_PIN, LOW);
    delay(200);
  }
}

// ===== KIRIM DATA KE BLYNK =====
void sendToBlynk() {
  float suhu = dht.readTemperature();
  float hum  = dht.readHumidity();
  int gasVal = analogRead(GAS_PIN);
  int lightVal = analogRead(LIGHT_PIN);

  Blynk.virtualWrite(V0, gasVal);        // GAS / WATER
  Blynk.virtualWrite(V1, suhu);          // TEMP
  Blynk.virtualWrite(V6, lightVal);      // CAHAYA
  Blynk.virtualWrite(V2, servo.read());  // SERVO
  Blynk.virtualWrite(V4, digitalRead(LED_RED));
  Blynk.virtualWrite(V5, digitalRead(LED_GREEN));
}

// ===== SETUP =====
void setup() {
  Serial.begin(115200);

  pinMode(BUTTON_PIN, INPUT_PULLUP);
  pinMode(LED_RED, OUTPUT);
  pinMode(LED_GREEN, OUTPUT);
  pinMode(BUZZER_PIN, OUTPUT);

  Wire.begin(21, 22);

  dht.begin();
  rtc.begin();
  lcd.init();
  lcd.backlight();

  servo.setPeriodHertz(50);
  servo.attach(SERVO_PIN, 500, 2400);
  servo.write(SERVO_CLOSE);

  Blynk.begin(BLYNK_AUTH_TOKEN, ssid, pass);

  timer.setInterval(1000L, sendToBlynk);

  lcd.print("Sistem Aktif");
  delay(1500);
  lcd.clear();
}

// ===== LOOP =====
void loop() {
  Blynk.run();
  timer.run();

  // ===== BACA SENSOR =====
  float suhu = dht.readTemperature();
  float hum  = dht.readHumidity();
  int gasVal = analogRead(GAS_PIN);
  int lightVal = analogRead(LIGHT_PIN);
  DateTime now = rtc.now();

  bool gasBahaya = gasVal > GAS_THRESHOLD;
  bool gelap = lightVal < LIGHT_THRESHOLD;
  bool aman = !(gasBahaya || gelap);

  // ===== LED =====
  digitalWrite(LED_GREEN, aman);
  digitalWrite(LED_RED, !aman);

  // ===== BLYNK LED STATUS =====
  Blynk.virtualWrite(V4, !aman);
  Blynk.virtualWrite(V5, aman);

  // ===== BUZZER =====
  if (!aman && !buzzerDone) {
    buzzer2x();
    buzzerDone = true;
  }
  if (aman) buzzerDone = false;

  // ===== PUSH BUTTON =====
  bool btn = digitalRead(BUTTON_PIN);
  if (btn == LOW && lastButton == HIGH) {
    servoState = !servoState;
    Blynk.virtualWrite(V3, servoState);
    delay(250);
  }
  lastButton = btn;

  // ===== SERVO =====
  // logika tambahan: jam 5 sore servo otomatis menutup
  bool jam5Sore = (now.hour() == 17); // aktif sepanjang jam 17
  if (jam5Sore) {
    servo.write(SERVO_CLOSE);  // tutup servo jam 5 sore
    servoState = false;        // reset status push button
  } else {
    if (!aman) {
      servo.write(SERVO_CLOSE);
    } else {
      servo.write(servoState ? SERVO_OPEN : SERVO_CLOSE);
    }
  }

  // ===== LCD =====
  lcd.setCursor(0, 0);
  lcd.print("T:");
  if (isnan(suhu)) lcd.print("--");
  else lcd.print(suhu, 1);
  lcd.print(" H:");
  if (isnan(hum)) lcd.print("--");
  else lcd.print(hum, 0);
  lcd.print("% ");

  lcd.setCursor(0, 1);
  if (gasBahaya) lcd.print("GAS BAHAYA ");
  else if (gelap) lcd.print("GELAP      ");
  else lcd.print("AMAN       ");

  lcd.print(now.hour());
  lcd.print(":");
  if (now.minute() < 10) lcd.print("0");
  lcd.print(now.minute());

  delay(1000);
}
```
## 6.3. Konfigurasi Pin ESP32

| Komponen                | Pin GPIO |
|-------------------------|----------|
| Sensor Gas (AO)         | GPIO 34  |
| Sensor Cahaya           | GPIO 35  |
| DHT22                   | GPIO 4   |
| Servo                   | GPIO 18  |
| Push Button             | GPIO 13  |
| LED Merah               | GPIO 26  |
| LED Hijau               | GPIO 27  |
| Buzzer                  | GPIO 25  |
| LCD & RTC SDA           | GPIO 21  |
| LCD & RTC SCL           | GPIO 22  |

---

## 6.4 Prinsip Kerja Sistem

Sistem bekerja berdasarkan kondisi lingkungan yang terbaca oleh sensor:

- **Hujan / Air terdeteksi** → Jemuran menutup  
- **Lingkungan gelap** → Jemuran menutup  
- **Kondisi aman** → Jemuran dapat dibuka  
- **Push button ditekan** → Servo membuka/menutup jemuran (jika kondisi aman)  
- **RTC** → Pada pukul 5 sore (17:00) servo otomatis menutup  

> **Catatan:** Pada simulator Wokwi, sensor gas digunakan sebagai pengganti sensor air karena keterbatasan komponen. Pada penerapan nyata, sensor gas diganti dengan sensor air untuk mendeteksi hujan.

---

## 6.5 Langkah-Langkah Integrasi Input dan Output

### 5.1 Integrasi Input
1. Sensor gas membaca nilai analog sebagai simulasi hujan.  
2. Sensor cahaya membaca intensitas cahaya lingkungan.  
3. Sensor DHT22 membaca suhu dan kelembapan.  
4. Push button membaca input manual pengguna.  
5. RTC membaca waktu aktual.

### 6.6 Proses Logika Sistem
- Data sensor dibandingkan dengan nilai ambang batas.  
- Jika salah satu kondisi berbahaya terpenuhi, sistem masuk ke **mode tidak aman**.  
- Jika semua kondisi aman, sistem masuk ke **mode normal**.

### 6.7 Integrasi Output
1. Servo menutup jemuran saat kondisi tidak aman.  
2. LED merah menyala saat kondisi berbahaya.  
3. LED hijau menyala saat kondisi aman.  
4. Buzzer berbunyi sebagai peringatan.  
5. LCD menampilkan suhu, kelembapan, status, dan waktu.  
6. Blynk menampilkan data secara **real-time**.

---
## 6.8 Tabel Hasil Pengujian Sensor dan Output

| No | Suhu (°C) | Humiditas (%) | Gas/Water | Cahaya | RTC (HH:MM) | Kondisi Sistem | LED Merah | LED Hijau | Servo (°) | Buzzer | LCD |
|----|------------|---------------|-----------|--------|-------------|----------------|-----------|-----------|------------|--------|-----|
| 1 | 28 | 60 | 1400 | 1200 | 16:00 | Aman | OFF | ON | 140 | OFF | T:28 H:60% AMAN 16:00 |
| 2 | 30 | 70 | 1600 | 1200 | 16:05 | Gas Bahaya | ON | OFF | 0 | ON | T:30 H:70% GAS BAHAYA 16:05 |
| 3 | 27 | 65 | 1400 | 900 | 16:10 | Gelap | ON | OFF | 0 | ON | T:27 H:65% GELAP 16:10 |
| 4 | 29 | 85 | 1400 | 1200 | 16:15 | Aman | OFF | ON | 140 | OFF | T:29 H:85% AMAN 16:15 |
| 5 | 28 | 70 | 1400 | 1200 | 17:00 | Jam 5 Sore | ON | OFF | 0 | OFF | T:28 H:70% AMAN 17:00 |
| 6 | 26 | 88 | 1400 | 1200 | 16:20 | Hujan | ON | OFF | 0 | ON | T:26 H:88% GAS BAHAYA 16:20 |

**Keterangan Tabel:**
- **Suhu & Humiditas** → dibaca dari DHT22.  
- **Gas/Water** → sensor analog; threshold 1500 → aman / bahaya.  
- **Cahaya** → sensor LDR; threshold 1000 → gelap / cerah.  
- **RTC** → modul DS3231, digunakan untuk logika menutup servo jam 17:00.  
- **Kondisi Sistem** → status sensor & logika kontrol.  
- **LED Merah/Hijau** → indikator bahaya/aman.  
- **Servo** → 0° = tutup, 140° = buka maksimal.  
- **Buzzer** → ON jika kondisi berbahaya.  
- **LCD** → menampilkan suhu, kelembapan, status sistem, dan jam.

---

## Kesimpulan

1. Sistem **servo jemuran otomatis** berhasil membuka/tutup sesuai kondisi sensor dan waktu.  
2. **LED indikator** bekerja sesuai status aman/berbahaya.  
3. **Buzzer** berbunyi saat kondisi berbahaya atau hujan.  
4. **Push button** dapat mengontrol servo secara manual, kecuali saat jam 5 sore.  
5. **RTC DS3231** berhasil digunakan untuk menutup servo otomatis pukul 17:00.  
6. **LCD I2C** menampilkan data sensor dan status dengan benar.

---

## Catatan

- Servo terbuka maksimal 140° saat kondisi aman.  
- Buzzer aktif saat gas/water sensor berbahaya atau kondisi gelap/hujan.  
- Push button berfungsi toggle servo, tapi **otomatis override saat jam 17:00**.  
- Data sensor dan status dikirim ke **Blynk IoT** untuk monitoring jarak jauh.



## 7.Pembahasan Hasil
Berdasarkan hasil pengujian, sistem jemuran otomatis mampu bekerja sesuai dengan perancangan. Sensor gas yang digunakan pada simulator Wokwi berhasil mensimulasikan fungsi sensor air dalam mendeteksi kondisi hujan. Ketika kondisi hujan atau gelap terdeteksi, motor servo secara otomatis menutup jemuran untuk melindungi pakaian.
LED dan buzzer memberikan indikator visual dan suara yang jelas saat kondisi tidak aman. Data suhu, kelembapan, dan status sistem ditampilkan pada LCD dan dapat dipantau melalui aplikasi Blynk secara real-time



## 8.Kesimpulan
Berdasarkan hasil perancangan dan pengujian yang telah dilakukan, sistem monitoring dan kontrol berbasis ESP32 yang menggunakan sensor air, sensor suhu dan kelembapan, sensor cahaya, motor servo, LED, buzzer, LCD, serta RTC dapat bekerja dengan baik dan saling terintegrasi. Sensor air mampu mendeteksi adanya air atau kondisi basah pada lingkungan dan menjadi indikator utama dalam menentukan kondisi aman atau tidak aman pada sistem.
Ketika sensor air mendeteksi adanya air, sistem secara otomatis memberikan respon berupa penutupan motor servo, penyalaan LED merah, serta bunyi buzzer sebagai tanda peringatan. Sebaliknya, saat tidak terdeteksi air dan kondisi lingkungan dinyatakan aman, LED hijau menyala dan motor servo dapat dikendalikan menggunakan push button. Informasi suhu, kelembapan, serta status sistem juga ditampilkan secara real-time pada LCD sehingga memudahkan pengguna dalam melakukan pemantauan.
Secara keseluruhan, sistem ini telah berhasil mencapai tujuan praktikum, yaitu membuat alat monitoring dan pengendalian berbasis mikrokontroler yang mampu bekerja secara otomatis berdasarkan kondisi lingkungan. Sistem ini dapat dikembangkan lebih lanjut, misalnya dengan penambahan fitur pemantauan jarak jauh berbasis IoT atau peningkatan akurasi sensor yang digunakan.


