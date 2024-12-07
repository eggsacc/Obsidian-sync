
Created <font style="color:tomato; font-family:Consolas;">29-11-2024</font>

Tags: #stm32 

Related: NIL

****

## Setting up USB

Pins `PA11` and `PA12` are responsible for USB connectivity on the STM32, and is configured from the STM32 IOC by enabling USB connections. On most STM32 boards, these pins are already tied to the on-board USB header, but double check the board schematics just to be sure.


![[Pasted image 20241129144132.png]]

However, for the STM32 to be recognized by our computer and hence initiate communication, we also have to implement some middleware which makes it emulate a USB device. This middleware is included in the default library.


![[Pasted image 20241129144337.png]]

The middleware provides multiple classes which the STM32 could emulate. For simple data exchange tasks, we can just pick the `Communications Device Class` (CDC) to turn the STM32 into a virtual COM port that the computer can talk to.

Selecting the middleware will automatically include the relevant libraries in a separate `USB_DEVICE` folder. The `usb_device.h` header file is automatically included in `main.c` to initialize the device, but we also have to manually include the `usbd_cdc_if.h` file to access the relevant functions.

![[Pasted image 20241129145305.png]]

If we look at `usbd_cdc_if.c`, there is only one function `CDC_Transmit_FS()` available for us to use. This is the function used to send a string or character array to the computer via USB.

To send data using this function, we have to define a character buffer and format the string using the `sprintf()` function included in the standard library.

````c
char buff[50];
sprintf(buff, "Data1: %d, Data2: %d\n", data1, data2);
CDC_Transmit_FS((uint8_t *)buff, strlen(buff));
````

The buffer has to be type-casted to `uint8_t *` pointer since that is the type of parameter the function expects. This is to eliminate any sign-related issues since the type `char` could be signed or unsigned depending on the development platform.

>[!tip] No floats!
>The `sprintf()` function is unable to properly format `float` type variables in STM32 Cube IDE since it uses a light-weight implementation of the C library. For `float` type variables, use fixed-point arithmetic to format it into an integer instead.
