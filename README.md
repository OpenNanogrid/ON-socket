# ON-socket
Drafts for a smart Extra Low Voltage Direct Current (ELVDC) socket.

## Components

### Voltage Reference

Needed for precise ADC measurements. 2.56V?

### Switching

1. n-channel MOSFET switching the negative rail, while a common conductor is tied to the positive rail and possibly earth grounded. This will reduce the overhead for gate drivers, charge pumps or p-channel mosfets as switches. What are the downsides to this approach?
2. p-channel MOSFET on positive rail. More expensive, Rds worse, but common GND at 0V.

### Current Sensing

1. MOSFET Vds sensing. Only valid if the consumer is a pure current source or current sink. What happens if the gate is discharged and Vds rises to 50V? The following amp and ADC will go into saturation, this needs some more thought. High impedance line from the amp, limited with a diode (crowbar)? Possible if the input impedance is high enough. Temperature drift.

2. PCB shunt. Inexpensive. Needs calibration. Quite high real estate on the PCB. Lowest part count, bidirectional sensing no problem.

3. SMD/THT resistor. Inexpensive but rather large for a high current rating. No calibration needed.

ADCs with integrated amps:
ADS1115: 16bit 4channel 8-800sps ADC with 16x PGA, i2c, differential mode
HX711: 24bit (less than 20bit usable) i2c ADC with amplifier, commonly used in weight scales

4. Integrated hall effect sensor, e.g. Allegro ACS712. Good efficiency. High price. Sourcing and sinking will both be covered (still needs antiparallel MOSFETs though).

### Voltage Sensing

Via high impedance voltage divider into ADC: input voltage, output voltage.

### Arc Sensing

Unplugging a consumer with an inductive load or a high current flow will result in arcing. Can this event be mitigated? How to identify arcing? The resulting plasma will be a large voltage drop right at the plug.

### Micro Controller

8bit with integrated ADC should be sufficient. ESP8266 with integrated WiFi is inexpensive but has high power demands.

### Communication

Wireless: NRF24L01+ or WiFi with ESP8266

### Display

Small OLED.

## Faults and Errors

Overload: Hiccup pattern with recovery time depending on input voltage drop.
Short Circuit: Switch output off. Probe current for 1ms every 500ms. Activate output again if current is below threshhold. Needs a more sophisticated algorithm for plug-in events when input capacitors are charged, soft start could be implemented with PWM.
Polyfuse?
Diode over output against back-EMF.