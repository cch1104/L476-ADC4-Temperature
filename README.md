# L476-ADC4-Temperature
STM32 Temperature Meter with LCD1602 + TMP36 Sensor

## Project Overview
This project uses **STM32**, **ADC**, **TMP36 temperature sensor**, and **LCD1602A** display.

The STM32 reads analog voltage from TMP36 using ADC, converts it to temperature, and shows the result on LCD.

Display Example:

Temperature :
 25.36 C

---

# Hardware Used

| Component | Description |
|--------|-------------|
| STM32 Nucleo / STM32 Board | Main controller |
| TMP36GT9Z | Analog temperature sensor |
| LCD1602A | 16x2 LCD Display |
| Jumper Wires | Connections |
| 5V / USB Power | Power source |

---

# TMP36 Pinout

| TMP36 Pin | Connect To |
|---------|------------|
| Vs | 3.3V |
| Vout | PA2 (ADC1_IN3) |
| GND | GND |

---

# LCD1602 Pin Connection (4-bit Mode)

| LCD Pin | STM32 Pin |
|--------|----------|
| RS | PA6 |
| EN | PA7 |
| D4 | PA8 |
| D5 | PA9 |
| D6 | PA10 |
| D7 | PA11 |

---

# STM32CubeMX Configuration

## ADC1 Settings

- Resolution = 12 Bit
- Data Align = Right
- Continuous Conversion = Disabled
- Number of Conversion = 1
- External Trigger = Software Start
- Channel = ADC_CHANNEL_3
- Sampling Time = 47.5 Cycles

## GPIO Output

Set pins:

PA6  
PA7  
PA8  
PA9  
PA10  
PA11

as Output Push Pull

---

# Formula Used

## ADC to Voltage

```c
Voltage(mV) = ADC_Value × 3300 / 4095

# TMP36 Voltage to Temperature

Temperature = (Voltage - 500) / 10

Because TMP36 outputs:

500mV = 0°C
+10mV per °C

Main Code Explanation
Variables
char buff[16];
float temperature;
uint32_t adcResult;
LCD Startup
lcd_Init();
lcd_Clear();
lcd_Puts("Temperature :");
Start ADC
HAL_ADC_Start(&hadc1);
Main Loop
while(1)
{
    HAL_ADC_PollForConversion(&hadc1,100);

    adcResult = HAL_ADC_GetValue(&hadc1);

    temperature=((float)adcResult)*3300.0/4095.0;

    temperature=(temperature-500.0)/10;

    lcd_Goto(0,1);

    sprintf(buff,"%5.2f C",temperature);

    lcd_Puts(buff);

    HAL_Delay(1000);
}
How It Works
STM32 starts ADC
Reads TMP36 analog voltage
Converts ADC value into mV
Converts mV into Celsius
Shows temperature on LCD
Updates every 1 second
Example ADC Reading

If ADC = 930

Voltage = 930 × 3300 / 4095 = 749mV

Temperature = (749 - 500) / 10

Temperature = 24.9°C
If LCD Shows Garbage

Use:

lcd_Clear();
HAL_Delay(50);

Check wiring:

RW pin to GND
Contrast pin with potentiometer
Correct 4-bit mode wiring
Future Upgrades
Add Degree Symbol
sprintf(buff,"%5.2f%cC",temperature,223);
Add Fahrenheit
F = C × 9/5 + 32
Add Averaging Filter

Take 10 ADC samples average.

Add UART Serial Output

Monitor on PC.

Expected Output
Temperature :
 26.45 C
Files Needed
main.c
lcd.c
lcd.h
stm32xxxx_hal_adc.c
stm32xxxx_hal_gpio.c
Author

