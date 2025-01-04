
Created <font style="color:tomato; font-family:Consolas;">25-12-2024</font>

Tags: 

Related: NIL

Sources: NIL

****

## Decoupling capacitors for microcontrollers

Local decoupling capacitors should be added as close as possible to the power supply (VDD) pins of microcontrollers to filter out noise and ensure a stable voltage.

A $100nF$ capacitor is usually added per VDD pin to filter out high frequency signal noise. Note that adding 1 capacitor to each power input pin is preferred over adding a single larger capacitor since smaller capacitors have a higher self resonant frequency and respond better to high frequency noise.

Another $10uF$ capacitor called a **bulk capacitor** is added close to the power input (like USB, DC power supply etc.) to smooth out any lower frequency noise and provide additional energy storage.

![[Pasted image 20241225150521.png#invert]]

### Separating analog & digital voltages

The analog power supply (VCCA) often needs to be decoupled separately from the digital side since it is more sensitive to noise generated from switching. A separate set of decoupling capacitors are used in conjunction with a ferrite bead to isolate the analog domain from the digital counterpart.

The circuit below is commonly used for STM32 series microcontrollers. 

![[Pasted image 20241225153644.png#invert]]


## Switch

Also called a single pole double throw (SPDT) switch, a 2 way switch.


## Crystal oscillator

When choosing capacitance for capacitors to use with crystal oscillators, look up the capacitance of the oscillator, subtract the stray capacitance (about 3-5 pF) and multiply the value by 2.

For example, using a 16Mhz oscillator with a capacitance of 10 pF, the capacitance required will be:

$$
(10pF-5pF)\times 2=10pF
$$

![[Pasted image 20241225234824.png#invert]]


## 3.3V Power supply

One way of supplying power is to use the 5V from a USB and step it down to 3.3V using a linear regulator like the [AMS1117](http://www.advanced-monolithic.com/pdf/ds1117.pdf). An example schematic is usually provided in the datasheets.

![[Pasted image 20241225235207.png#invert]]


## USBC

USB type C connectors have a few more connectors than other USB variants, namely the `VCONN` and `CC` pins.

The `VCONN` pin provides voltage to any circuitry that may be included with the USB-C connector (PD ICs etc.), and is usually left unconnected if unused.

The `CC` (configuration channel) pin is used to set a USB-C port as either a source or a sink (power supply or consumer), as well as configure the amount of current to be drawn.

### Configuration channel

Pulling the `CC` pin low will mark the port as a power input, while pulling it high to `+5V` will mark it as a power source. The CC pin is also used to detect when a device is connected to a source: a pull-up and pull-down circuit connected together forms a simple voltage divider.

This voltage divider circuit is how the `CC` pin is used to also configure the amount of current to be supplied. By changing the resistance of the pull-up resistor in the source, the `CC` pin could measure the voltage in the wire to determine the current capabilities of the voltage source. The common resistor values used are:

| Current level (suppliable by source) | Source pull-up resistance | Voltage on CC |
| ------------------------------------ | ------------------------- | ------------- |
| Default (500-900mA)                  | 56kΩ                      | 0.25 - 0.60V  |
| 1.5A @ 5V                            | 22kΩ                      | 0.60 - 1.20V  |
| 3.0A @ 5V                            | 10kΩ                      | 1.20 - 2.00V  |

On the device (power consumer) side, the pull-down resistance is fixed at 5.1kΩ.

![[Pasted image 20241226132913.png#invert]]


## Power flag

The electrical rules checker (ERC) in Ki Cad always complains about power input pins not being driven by a power output pin. This is because it has no idea how or where power is coming from, so we can add a `PWR_FLAG` to indicate that we know the wires are connected correctly to the power source. This flag may be added to ground connections sometimes too.

![[Pasted image 20241226140718.png#invert]]