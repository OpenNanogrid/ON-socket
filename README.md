# ON-socket
Drafts for a smart Extra Low Voltage Direct Current (ELVDC) socket.

![](/cad/Wall Socket v4 raytraced2.png)

## Components

### Voltage Reference

Needed for precise ADC measurements. 2.56V?

### Switching

1. n-channel MOSFET switching the negative rail, while a common conductor is tied to the positive rail and possibly earth grounded. This will reduce the overhead for gate drivers, charge pumps or p-channel mosfets as switches. What are the downsides to this approach? Voltage sensing will be difficult as the MCU and ADC will be tied at the low side, not floating. A differential sensing with a common voltage higher than the maximum bus voltage is needed.

2. p-channel MOSFET on positive rail. More expensive, Rds worse, but common GND at 0V.

potential MOSFETs:

n-Channel
	CSD18540Q5B, 2.0 mOhm Rds
    CSD18542KTT, 4.0 mOhm Rds
        
Gate drivers: LM5109B-Q1

### Current Sensing

1. MOSFET Vds sensing. Only valid if the consumer is a pure current source or current sink. What happens if the gate is discharged and Vds rises to 50V? The following amp and ADC will go into saturation, this needs some more thought. High impedance line from the amp, limited with a diode (crowbar)? Possible if the input impedance is high enough. Temperature drift.

2. PCB shunt. Inexpensive. Needs calibration. Quite high real estate on the PCB. Lowest part count, bidirectional sensing no problem.

3. SMD/THT resistor. Inexpensive but rather large for a high current rating. No calibration needed.

4. Integrated hall effect sensor, e.g. Allegro ACS712. Good efficiency. High price. Sourcing and sinking will both be covered (still needs antiparallel MOSFETs though). (heavy idle: 5V supply, 10mA Iq)

ADCs with integrated amps:
INA220: 12bit Bi-Directional, Zero-Drift, Low- or High-Side, I2C Out Current/Power Monitor, max 26V bus voltage. 1mA Iq
ADS1115: 16bit 4channel 8-800sps ADC with 16x PGA, i2c, differential mode, 150µA Iq
HX711: 24bit (less than 20bit usable) i2c ADC with amplifier, commonly used in weight scales, 1.5mA Iq

### Voltage Sensing

Via high impedance voltage divider into ADC: input voltage, output voltage.

### Arc Sensing

Unplugging a consumer with an inductive load or a high current flow will result in arcing. Can this event be mitigated? How to identify arcing? The resulting plasma will be a large voltage drop right at the plug.

### Micro Controller

8bit with integrated ADC should be sufficient. ESP8266 with integrated WiFi is inexpensive but has high power demands.

### Power Supply

For logic power a low Iq buck converter. Best with synchronous rectification, few external parts.  Potential candidates: TPS54061 (0.09 mA), LM5156 (0.01 mA), LM5009A (0.5 mA), all types up to 60 Vin and about 150mA output.
If the n-channel MOSFET is switched at the high side a gate driver is needed. Power supply for the gate driver can be derived from the 3.3 V logic power if the gate driver can bootstrap it's high potential from that. Although a driver might charge and discharge the FET gate with 1..2A, the charge time is sufficiently low and can be buffered by caps. For a very low switching frequency (ie. using the FET as a power switch) this is fine. When the FET is used to throttle the output (e.g. via BAM / bit ampltitude modulation), the gate driver needs more power.

### Communication

Wireless: NRF24L01+ or WiFi with ESP8266
However, everything should be passive first and work plug and play without having a communication channel. 

### Optional Display

Small OLED

![](/cad/Wall Socket v4 raytraced.png)

## Faults and Errors

1. Overload: Hiccup pattern with recovery time depending on input voltage drop.
1. Short Circuit: Switch output off. Probe current for 1ms every 500ms. Activate output again if current is below threshhold. Needs a more sophisticated algorithm for plug-in events when input capacitors are charged, soft start could be implemented with PWM.
1. Polyfuse? 
1. Diode over output against back-EMF.

![](/cad/Wall Socket v4 inside.png)
![](/cad/Wall Socket v4 side2.png)
![](/cad/Wall Socket v4 front2.png)
