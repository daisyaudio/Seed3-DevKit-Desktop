# Changelog

## Rev3

- Removed components from audio input circuit that introduced noise: R64, R65, D21, D22, D23, D24
- Connected switching connection on CV Input jacks to GND to prevent idling at non-0V
- Added silk screen indications for software mapping to Daisy for all pins/peripheral information.
- Swapped CV out 1 and 2 from the seed so that the software channel matches the hardware label

## Rev2

- Fixed INH pin on CD4051 to be properly connected to GND.
- Fixed LED OE pin on PCA9685 to be properly connected to GND so it is always on.
- Huge rework of power supply circuit
  - USB +5V now goes to a boost to generate +9V
  - +9V is split to provide DSY_VIN, +9V_A
  - +9V_A now powers an LD1117-5V to generate a more reliable +5V (for headphones, DAC MCP6002, and CV input ref voltage)
- AREF_+2V5 has been replaced with +4v5 generated from buffered +9V_A
- Added clamping diodes (1N5817) to the audio input detection to help protect from higher level audio potentially damaging the GPIO.
- Added explicit, "C0G" in value field for the few caps that should be that
- Added series AC coupling cap to input of the line out circuit (to better match, the already tested Pedal circuit).
- Added series 100R resistor to the output of the line out circuit
- Added a 10k to GND after the output coupling cap so the HPF is fixed (once again, to match the pedal circuit)
- Updated rev text to rev2
- Moved Open hardware logo to below the CV/Gate jacks (new beefy power circuits occupied previous location)
- Added some more vias to GND where it makes sense.
- Updated QR code to point to: <https://electro-smith.com/seed3-desktop-redirect>

## Rev1

Original Hardware
