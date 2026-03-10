Project: convert a dumb tap burner into a pulsed machine with stepper control. "Sinker", but smart, to allow me to get a nice surface finish.
We use the power stage of the tap burner almost as-is, and throw away its original servo control board (and the servo)

### Hardware
| Component | Description |
|---|---|
|**Controller**|Raspberry Pi Pico (C SDK) — 2-core setup|
|**Stepper Drive**|A4988/DRV8825 with TR6x1 screw, 1/8 microstep (1600 steps/mm)|
|**Motion Speed**|0–120 mm/min (2 mm/sec), 3200 steps/sec max|
|**Spark Voltage**|+80 V on workpiece (always HOT when enabled)|
|**Electrode**|At GND, moves down to contact|
|**Coolant**|Water (manually activated pump for flushing, or still water running for micro-jobs)|

[linear axis to be used](https://www.amazon.co.uk/dp/B0CL3PT831?ref=ppx_yo2ov_dt_b_fed_asin_title)

For driving the pulses, we're using FR120N MOSFET bank (4x of them), and hacking into [one of these](https://www.amazon.co.uk/dp/B07HBQZ9BK?ref=ppx_yo2ov_dt_b_fed_asin_title) PWM modules - this gives us an optocouple and a way to drive FETs via 24V power

Power:
[this 2A power supply](https://www.amazon.co.uk/dp/B0DDXNB7RN?ref=ppx_yo2ov_dt_b_fed_asin_title) - pick apart. Powers:
- stepper driver (and stepper)
- FETs driver (might need a divider, since the max gate-source voltage spec is 20V? although original board claims to take up to 30V, might already have a divider inside)
- [24V->5V stepdown](https://www.amazon.co.uk/dp/B0DQ4WJ1BT?smid=A7XQ1VBF2L9AH&ref_=chk_typ_imgToDp&th=1), which powers:
	- Pi
	- Display

### Control arch
- main thread lives on core-0, handles display via [this library](https://github.com/gavinlyonsrepo/displaylib_1bit_PICO/blob/main/examples/ssd1306/hello_128_32/main.cpp), handles buttons
- sparking/sensing/stepping thread lives on core-1

### Resources
https://github.com/TuriSc/RP2040-Button/tree/main - button debouncing library
https://www.diyengineers.com/2022/04/21/a4988-how-to-use-with-arduino-or-nodemcu-esp8266/ - stepper driver usage
