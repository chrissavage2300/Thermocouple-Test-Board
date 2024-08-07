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

Once you follow the main formula, you are now reading voltage and can use it how ever you would like. For this application, the final result is in uV * 100, ie a voltage of 40uV will be read as 400. Next the cold temperature from the MCP9800 needs to be converted into a voltage. I used a linearized formula for that, that I found using Libre Office. The truncated  formula is basically "Temperature*4" so we use integers only. The exact formula is:

![image](https://github.com/user-attachments/assets/96f0d731-762b-40c2-9d39-2cdfe5d9860e)

Note that I multiply by 0.01. This is so we dont have to divide by 100.

Then we perform an addition on the two values:
![image](https://github.com/user-attachments/assets/6383d8dc-bfcc-425d-bc51-25958fc83d19)

Now we can finally use this voltage in the polynomial formula given by NIST to figure out our temp. In my case though, I used a linearized formula, since K thermocouples are mostly linear. I also used a short version due to using integers:
![image](https://github.com/user-attachments/assets/64b7329f-039c-4a0b-9935-9d3be59c85f2)

This is the exact linearized formula. Its within a degree or two.

![image](https://github.com/user-attachments/assets/f3600287-cc5d-4225-aabf-61a3b4071f50)

This is the integer formula I used to do most of the math. In testing, its also accurate to within a degree or two, which is fine. My final system can only change that much. As far as the code goes, you now have your final temp, however, I did perform a division by 10000 after this because the number is too big to be used. 


## Schematic
![schematic](https://github.com/chrissavage2300/Thermocouple-Test-Board/assets/24416184/8acd59b6-994e-437b-89c1-85277e2db2ee)

![Board](https://github.com/chrissavage2300/Thermocouple-Test-Board/blob/main/board.png?raw=true)
