# MOCHI — ESP32 desktop companion robot

Animated face (55 emotions), live clock, live weather, and touch/button
interaction on a single SSD1306 OLED.

## Wiring

| Component        | Connects to                          |
|-------------------|---------------------------------------|
| OLED SDA          | GPIO 21                               |
| OLED SCL          | GPIO 22                               |
| OLED VCC          | 3V3                                   |
| OLED GND          | GND                                   |
| Touch pad         | GPIO 4 (T0)                           |
| Button — MODE     | GPIO 32 → other leg to GND            |
| Button — NEXT     | GPIO 33 → other leg to GND            |
| Button — SELECT   | GPIO 25 → other leg to GND            |
| Buzzer +          | GPIO 26                               |
| Buzzer −          | GND                                   |
| LED anode (+)     | 120 Ω resistor → GPIO 27              |
| LED cathode (−)   | GND                                   |

Buttons use `INPUT_PULLUP`, so they read LOW when pressed — no external
pull-up resistors needed. The 120 Ω resistor is safe for any common LED
color at 3.3 V logic level (works out to roughly 3–11 mA depending on
the LED's forward voltage).

## Libraries (Arduino IDE → Tools → Manage Libraries)

- **Adafruit GFX Library**
- **Adafruit SSD1306**
- **ArduinoJson** (v7.x — the code uses `JsonDocument`, which also works
  fine if Library Manager gives you v6)

Board package: **esp32 by Espressif Systems** (Boards Manager), v3.x.
Select **Board → ESP32 Dev Module**, pick the right COM port, and set
partition scheme to Default.

## Before you flash

Open `config.h` and fill in:

```cpp
#define WIFI_SSID        "your wifi name"
#define WIFI_PASSWORD    "your wifi password"
#define OWM_API_KEY      "your openweathermap key"
```

Get a free OpenWeatherMap key at openweathermap.org/api — new keys can
take up to about an hour to activate, so a `401 Invalid API key` right
after signup is normal, not a bug.

## Files

- `mochi_robot.ino` — main state machine, input handling, boot animation
- `config.h` — every pin, credential, and timing constant
- `faces.h` — the 55-emotion parametric face engine
- `weather.h` — WiFi, NTP clock, OpenWeatherMap fetch + simple icons

## Controls

- **MODE** — cycle Face → Time → Weather → Menu
- **NEXT** — next emotion (Face) / next item (Menu) / force-refresh (Weather)
- **SELECT short press** — confirm menu item
- **SELECT long press (~0.8s)** — toggle auto-idle emotion cycling on/off
- **Touch pad** — "pet" Mochi for a happy/love reaction

## Known first-flash tuning

- Touch threshold is auto-calibrated at boot (don't touch the pad while
  "Calibrating touch" is on screen). If it still feels too sensitive or
  not sensitive enough, adjust `TOUCH_RATIO` in `config.h`.
- The face shapes are drawn with trig, not a bitmap editor, since there
  was no way to preview actual OLED pixels while writing this. Logic is
  correct, but a few emotions may want a small coordinate nudge once you
  see them on your real screen — normal first-flash polish, not a bug.
