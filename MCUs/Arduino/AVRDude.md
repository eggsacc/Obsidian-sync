
Created <font style="color:tomato; font-family:Consolas;">27-11-2024</font>

Tags: 

Related: NIL

****

## Programming without IDE

AVRDude is a utility program used to program and manipulate the ROM and EEPROM of AVR series microcontrollers widely used by Arduino boards. It uses In-System Programming (ISP), and features a command-line driven interface so users could easily flash code onto their microcontrollers using only a terminal.

By the way, AVRDude actually stands for **AVR D**ownloader / **U**ploa-**DE**r (weird.)

If we enable `verbouse` output during compilation in the Arduino IDE, we see that it executes this command whenever we press the upload button:

````
"C:\Users\yizha\AppData\Local\Arduino15\packages\arduino\tools\avrdude\6.3.0-arduino17/bin/avrdude" "-CC:\Users\yizha\AppData\Local\Arduino15\packages\arduino\tools\avrdude\6.3.0-arduino17/etc/avrdude.conf" -v -V -patmega328p -cstk500v1 -PCOM4 -b19200 "-Uflash:w:C:\Users\yizha\AppData\Local\Temp\arduino\sketches\272FA9BE0822B46BF22FCE3ADDDC0534/sketch_nov27a.ino.hex:i"
````

We can remove all the path variables to make it more readable:

````
"<path>/avrdude" "-C<path>/avrdude.conf" -v -V -patmega328p -cstk500v1 -PCOM4 -b19200 "-Uflash:w:<path>/sketch_nov27a.ino.hex:i"
````

Breaking it down into the individual flags:

- **Command:** `avrdude`
- **Config:** `-C<path>/avrdude.conf`
- **Verbose:** `-v`
- **Disable verification:** `-V`
- **Programmer:** `-cstk500v1`
- **Serial port:** `PCOM4`
- **Baud rate:** `b19200`
- **Operation:** `-U flash : w : <path>/sketch_name.ino.hex:i`

The last flag telling AVRDude the type of operation to perform is still quite long, so let's break it down further:

````
-U memtype : operation : filename : [:format]
````

- `memtype:` `lfuse, hfuse, efuse, lock, eeprom, flash ... etc`
- `operation:` `r` (read), `w` (write), `v` (verify)
- `filename:` path to file or a number (setting fuse bits)
- `format:` `i, m, r, e, s`. `i` stands for intel hex format, which is basically the format that Arduino code uses, so this flag is used when flashing code. `m` stands for immediate value, or just a hard-coded number.

According to the format above, the line

````
-Uflash:w:<path>/sketch_nov27a.ino.hex:i
````

Translates to:

````
write to flash memory sketch_nov27a in intel hex format
````

Similarly, if we want to set the `lfuse` bit to `0xF2`, we can use the command:

````
-Ulfuse:w:0xF2:m
````

>[!info] Fuse bits
>Fuse bits are special configuration bits in microcontrollers that configures the microcontroller during startup, before any code is executed.


## AVRDude

AVRDude comes packaged with Arduino and can be found at the path specified by the IDE.

````
C:\Users\user\AppData\Local\Arduino15\packages\arduino\tools\avrdude\6.3.0-arduino17/bin/avrdude.exe
````

We need to `cd` into the directory the `avrdude.exe` executable is found ant test it by running:

````
avrdude.exe
````

It should respond and return a list of available flags.

Now, let's try to set the low-fuse bit `lfuse` in an Arduino UNO using the ATMega328p chip. Type the command:

````
.\bin\avrdude.exe -C .\etc\avrdude.conf -v -V -p atmega328p -cstk500v1 -PCOM4 -b19200 -U lfuse:w:0xF2:m
````

The terminal should return `avrdude.exe done. Thank you.`


## Uploading (flashing) code

To upload code to out Arduino using AVRDude, we first need to export the compiled binary of our code. In the Arduino IDE, head to the top left and click on `Sketch`, then choose `Export Compiles Binary`. This will create some extra files with names like `sketch.ino.hex`, `sketch.ino.with_bootloader.hex` etc.

Copy the file ending with `.hex` (without bootloader) as path, and modify the previous command used to set the fuse bits:

````
.\bin\avrdude.exe -C .\etc\avrdude.conf -v -V -p atmega328p -cstk500v1 -PCOM4 -b19200 -U flash:w:"<path>/sketch_name.ino.hex":i
````

