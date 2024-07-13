# Thermocouple-Test-Board
Uses both the MCP9801 and MCP3425 onboard temperature sensor and 16 bit ADC to read a thermocouple

* Parts list is self explanatory. MCP9801 temp sensor and MCP3425 SOT ADC. The 16bit or 18 bit versions should work. 

* C3 and C4 are 4.7uF to 10uF

* C1 and C2 are 0.1uF or lesser to help filter out the input.

* JP1 is a standard 2.54mm screw terminal block. J2 is 2.54mm pins.

* R1 and R2 are 4.7k ohm resistors.

  ## The Code
  ![ExampleCircuit](https://github.com/chrissavage2300/Thermocouple-Test-Board/assets/24416184/49aca6b6-4118-47a3-8593-96b55f836f41)

Rather than using a dedicated Thermocouple IC to read a thermocouple, we can just use a sensitive ADC to read it. K type thermocouples have a signal of 40uV/C, which is pretty small. So we have to amplify it using a PGA. We also need a high resolution ADC. Ive found that 16 to 18 bits should give enough resolution and I only need it to be within 1 degree C or better. The algorithm works by first setting the PGA to 4x, then using the full 16 bits of the ADC. We then use some floating point math to get our voltage of the thermocouple. 

After that, we convert the temperature reading of the MCP9801 to a voltage. We dont add the temperatures together, rather, we add the voltages. Once the two voltages are added, then we shove it into the thermocouple formula to get our temperature. 

In testing Ive found it to be within +/-1C°. I could max out the PGA and use 8x mode, but 4x gives me roughly 1.5 codes per degree C. I tested this circuit both at boiling water (as close as I can get it) and freezing water. Freezing water is easy, I used an insulated bottle and tried to crush up ice as best as I can. Boiling water is a bit different as I had to move my kettle into the same room, and it doesnt stay boiling either, so measurements had to be quick. 

This code is currently being tested on a dspic33. Anything that has I2C will work, as long as you have a library to do so. 

## Math
![Math](https://github.com/user-attachments/assets/36124b59-55a0-411d-a79e-8d0891d484df)
Some of this was taken from the datasheet of a similar ADC. We are basically using a 16 bit ADC, set for max resolution. I initially had selected a PGA of 8 but decided that I dont need that much accuracy.
If you need that much accuracy, you can re-do the math to figure out your code value. Based on these formula's above, for the temperature I will be using, my max code that I will recieve is 640 (base 10). 

## Schematic
![schematic](https://github.com/chrissavage2300/Thermocouple-Test-Board/assets/24416184/8acd59b6-994e-437b-89c1-85277e2db2ee)

![Board](https://github.com/chrissavage2300/Thermocouple-Test-Board/blob/main/board.png?raw=true)
