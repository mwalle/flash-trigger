# Tiny LED Trigger

<img src="/images/flash-trigger1.jpg" align="center">

Your simple, cheap and open LED flash trigger for your underwater strobe.

## Features

<img src="/images/oshw-de00130.svg" align="right" width="150">

The flash trigger provides the following features:

- Reliably triggers your optical underwater strobe in manual mode.
- Converts any trigger pulse from your camera to a constant 100µs LED
  pulse.
- Very bright pulse because the LED is driven with a very high current
  (most LEDs have a peak forward current of 100mA-150mA specified for
  100µs for a 1/10 duty cycle).
- Pre-flash supression mode. Useful if your camera has a pre-flash pulse on
  the hot shoe pin.
- Powered by just one CR2032 coin cell.
- More than 10.000 cycles per coin cell.
- Five different positions for the LED.
- Supports up to two LEDs in parallel.
- Battery good indication.
- Extremely small.
- [Open Hardware](https://certification.oshwa.org/de000130.html),
  schematics and layout are free for everyone.

## Pictures

<p>
<img src="/images/flash-trigger2.jpg" width="300">
<img src="/images/flash-trigger3.jpg" width="300">
</p>

## Schematics
The LED trigger consists of:
 - the [base board](flash-trigger/flash-trigger.pdf) and
 - a [Canon compatible hot shoe](canon-hotshoe/canon-hotshoe.pdf).

Only populate the X pin of the hot shoe to be compatible with any camera.

## Theory Of Operation

At the heart of the circuit, there is a monostable multivibrator (`U1B`)
which converts any falling edge of the trigger output of your camera to a
constant length pulse of about 100µs. The time constant is given by the
values of `C1` and `R3`. The multivibrator is retriggerable, thus if there
would be a second falling edge within that 100µs pulse the period would
start over. But in practice, this shouldn't matter.

A second monostable multivibrator (`U1A`) can be used to supress a
pre-flash pulse. In this case, the second input of `U1B` is connected to
the output of `U1A` and only if that output is active the 100µs pulse is
generated. The output of `U1A` in turn, will be active for about 100ms
after a *rising* edge of the flash trigger input. Thus the sequence is as
follows:
 1. The camera pulses the trigger output low to generate a pre-flash.
 2. Normally, `U1B` would now trigger a pulse on the falling edge, but
	because the second input is still inactive, no pulse is generated.
 3. On the rising edge (that is, at the end of the pre-flash pulse), `U1A`
	triggers a 100ms pulse and arm `U1B`.
 4. The camera triggers the actual main flash by pulsing the output trigger
	again.
 5. This time, the second input of `U1B` is active and the 100µs trigger
	pulse will be generated.

This has the following two implications:
 * The duration between pre-flash and the main flash must not exceed 100ms.
 * Repeated flashes will be limited to about 10Hz (reciprocal value of the
   100ms)

Both shouldn't really matter. But of course you can tune this time constant
by changing the values of `C3` and `R9`.

The 100µs pulse is enabling the logic level N-channel MOSFET `Q1`, which is
driving the LEDs `D1`..`D5` through individual series resistors `R1`..`R5`.
Depending on the value of the series resistor and the LEDs, it should be
possible to connect up to two LEDs.

The circuit is designed to work down to the forward voltage of the LED.
`U1` will work down to 1.0V. Nevertheless, there is a voltage supervisor,
which is pretty simple: `U2` will pull its output to high if its supply
voltage is greater than 2.7V, thus driving the green LED `D6` with its
series resistor `R11`. At 2.7V the coin cell should still have about 25% of
its capacity, giving the user enough margin.

## Trigger LEDs

The flash trigger has five different LED positions facing left to right.
It should be possible to populate up to two LEDs. The LEDs should be chosen
according to the flash you like to use. The author has successfully tried
red and infrared LEDs with INON flashes. The more the LED is towards the
infrared spectrum, the lower is its forward voltage and the longer it will
work with a given coin cell.

## Bill Of Materials

| Ref | Qty | Part | Footprint |
| --- | --- | --- | --- |
| R1, R2, R3, R4, R5, R6 | 6 | 10Ω Resistor | 0603 |
| R7, R8, R10 | 3 | 100kΩ Resistor | 0603 |
| R9 | 1 | 1MΩ Resistor | 0603 |
| R11, R12 | 2 | 1.5kΩ Resistor | 0603 |
| C1 | 1 | 10nF Ceramic Capacitor | 0603 |
| C2 | 1 | 100µF Ceramic Capacitor | 0805 |
| C3 | 1 | 200nF Ceramic Capacitor | 0603 |
| C4 | 1 | 2.2nF Ceramic Capacitor | 0603 |
| C5 | 1 | 100pF Ceramic Capacitor | 0603 |
| D1, D2, D3, D4, D5 | 5 | LED | THT |
| D6 | 1 | Green LED | 0603 |
| Q1 | 1 | NDS331N N-Channel MOSFET | SOT23-3 |
| U1 | 1 | 74LV123 Dual Retriggerable Monostable Multivibrator | SSOP16 |
| U2 | 1 | BD49K27G Voltage Supervisor 2.7V | SSOP3 |
| SW1 | 1 | PCM12 Switch | - |
| SW2 | 1 | PTS810 Push Button | - |
| BT1 | 1 | CR2032 Coin Cell Holder | - |
| J1, J2 | 2 | MillMax 0550 Pin Receptable | - |
| J3, J4 | 2 | MillMax 0508 PCB Pin | - |

## Kits and PCBs

If you need a PCB, a kit or maybe an assembled board, contact me at
<flash-trigger@walle.cc>.

## License

This source describes Open Hardware and is licensed under the CERN-OHL-S
v2.
