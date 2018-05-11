![Synaptics Concept Prototyping Team](Pictures/Small/CPT_logo.png) 

&copy; 2018 Synaptics, Incorporated. All Rights Reserved

# Developer Guide #

last update, May 10, 2018

Contact [Synaptics](mailto:tourmalet@synaptics.com)

Writing Android apps to use Tourmalet hardware features such as GPIOs, UART, and buses requires some understanding of three layers: (#electrical) connections & pinout, (#sysfs) interface for Linux hardware access, and using (#libsuperuser) to get a privileged command shell from Android.

## Electrical ##

The DragonBoard 410c provides access to many I/O lines on the 40 pin low speed header. Some of these are used by the DragonBoard or Tourmalet expansion board and should generally not be used for further expansion, as this may cause hardware contention issues and unexpected behavior.

| Pin number | Signal name  | GPIO number |      comment     |   | Pin number | Signal name  | GPIO number |      comment     |
| ---------: | :----------: | ----------: | :--------------- | - | ---------: | :----------: | ----------: | :--------------- |
|          1 | GND          |             | logic level low  |   |          2 | GND          |             |                  |
|          3 | UART0_CTS    |         904 |                  |   |          4 | Power button |             |                  |
|          5 | UART0_TX     |         902 |                  |   |          6 | Reset button |             |                  |
|          7 | UART0_RX     |         903 |                  |   |          8 | SPI0_CLK     |         921 | accelerometer    |
|          9 | UART0_RTS    |         905 |                  |   |         10 | SPI0_MISO    |         919 | accelerometer    |
|         11 | UART1_TX     |         906 |                  |   |         12 | SPI0_CS_N    |         920 | accelerometer    |
|         13 | UART1_RX     |         907 |                  |   |         14 | SPI0_MOSI    |         918 | accelerometer    |
|         15 | I2C0_SCL     |         909 | touch at 0x20    |   |         16 | Reserved     |             |                  |
|         17 | I2C0_SDA     |         908 | touch at 0x20    |   |         18 | Reserved     |             |                  |
|         19 | I2C1_SCL     |         925 | sensors          |   |         20 | Reserved     |             |                  |
|         21 | I2C1_SDA     |         924 | sensors          |   |         22 | Reserved     |             |                  |
|         23 | GPIO_36      |         938 | 5V enable        |   |         24 | GPIO_12      |         914 |                  |
|         25 | GPIO_13      |         915 | touch ATTN       |   |         26 | GPIO_69      |         971 |                  |
|         27 | GPIO_115     |        1017 | gyro ATTN        |   |         28 | Reserved     |             | compass ATTN     |
|         29 | GPIO_24      |         926 | input only       |   |         30 | GPIO_25      |         927 |                  |
|         31 | GPIO_35      |         937 |                  |   |         32 | GPIO_34      |         936 | volume up        |
|         33 | GPIO_28      |         930 |                  |   |         34 | GPIO_33      |         935 | volume down      |
|         35 | 1.8V PWR     |             | logic level high |   |         36 | SYS DC IN    |             |                  |
|         37 | 5.0V PWR     |             |                  |   |         38 | SYS DC IN    |             |                  |
|         39 | GND          |             | logic level low  |   |         40 | GND          |             |                  |

### Notes: ###

- Any GND signal (pin 1, 2, 39, or 40) can be used as a reference for logic level low ("false" or '0"); 1.8V can be used as a reference for logic level high ("true" or "1").
- Any input connected to a GPIO (e.g. switches, buttons, etc.) will require a pull-up resistor.  We recommend a 4.7k&Omega; resistor.
- I2C0 (pins 15 & 17) has Synaptics Touch at address 0x20, Gyroscope and accelerometer at 0x68, ambient light and proximity sensor at 0x39, and compass at 0x0c; any other address should be available for use.
- I2C1 (pins 19 & 21) has the DSI to HDMI video converter at address 0x39; any other address should be available for use.
- GPIO_36 (pin 23) controls the 5V power supply on pin 37.
- SPI0 is in use by an accelerometer.
- GPIO_34 (pin 32) and GPIO_33 (pin 34) are the volume buttons on the side of the Tourmalet case/board. They are configured as keycodes 116 and 117.

## sysfs ##

The Linux kernel provides a hardware interface within the filesystem. Many devices, including GPIOs, can be accessed by reading and writing files under the `/sys` directory (exported by a kernel subsystem called sysfs).

### sysfs GPIOs ###

General Purpose Input/Outputs (GPIOs) can be accessed from `/sys/class/gpio/`. Access generally requires a root shell.

```{r, engine=sh}
adb root
adb shell
```

To use a particular GPIO, first write its number to `/sys/class/gpio/export`

```{r, engine=sh}
echo 902 > /sys/class/gpio/export
```

This will create a directory `/sys/class/gpio/gpio<number>`, in this case `/sys/class/gpio/gpio902`, containing several files to access the GPIO.
To use the GPIO as input or output, write `in` or `out` to `/sys/class/gpio/gpio<number>/direction`.

```{r, engine=sh}
echo in > /sys/class/gpio/gpio902/direction
```

To get or set the logic level of the pin (1 for high, 1.8V, 0 for low, 0V), read or write `/sys/class/gpio/gpio<number>/value`

```{r, engine=sh}
cat /sys/class/gpio/gpio902/value
```

For more information, see https://www.kernel.org/doc/Documentation/gpio/sysfs.txt.

## libsuperuser ##

Android apps can get a root shell using [libsuperuser](https://github.com/Chainfire/libsuperuser) and a permissive `su` binary such as [SuperSU](http://www.supersu.com/). Although libsuperuser provides several ways to use a root shell, the simplest and most commonly used is likely

```{r, engine=java}
List<String> Shell.SU.run(String command);
```

This can be called with the shell commands above to access GPIOs, for example

```{r, engine=java}
Shell.SU.run("echo 902 > /sys/class/gpio/export");
Shell.SU.run("echo in > /sys/class/gpio/gpio902/direction");
final String value = Shell.SU.run("cat /sys/class/gpio902/value");
```

[Tourmalet GPIO Sample](../Software/TourmaletGpioSample.zip) is an Android app that exports pin 5 as output, with buttons to set its logic level, and exports pin 7 as input, with a radio button indicating its value. A quick test is to click the buttons with and without pins 5 and 7 shorted together.

![Sample GPIO](Pictures/Small/Tourmalet_GPIO_Sample.jpg "Sample GPIO test App")
