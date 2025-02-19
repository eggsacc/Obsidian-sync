
Created <font style="color:tomato; font-family:Consolas;">26-12-2024</font>

Tags: 

Related: NIL

Sources: NIL

****

## Differential signal

Differential signal is used in high-speed communication protocols where 2 data wires, routed side-by-side, carry signals of equal magnitude but opposite polarities. The receiver reads the **difference** between the 2 signal lines instead of just one.

![[Pasted image 20241226165018.png#invert]]

### Common-mode noise suppression
 
 One major advantage of using such a transmission method is noise immunity. Since the 2 data lines are very close to each other and carries opposing signals, any noise affecting one data line will also almost equally affect the other

![[Pasted image 20241226165518.png#invert]]

Since the receiver interprets the difference in the signals and both signal lines are affected equally by noise, the noise effectively cancels each other out. This allows a differential signal pair to eliminate noise without any extra filtering.

However, one caveat to this is the fact that both signal lines may not always be affected equally by noise. Hence, special design considerations regarding the trace width, length and spacing have to be taken into account when designing PCBs working with high speed signals.

### EMI

Since differential pair signals usually operate at very high frequencies, the rapid switching in each line produces a more significant magnetic field which induces more electromagnetic interference ($\frac{dI}{dt}$ is large).
However, since the 2 data lines have opposite polarities, the EMI produced cancels each other out in the region between the 2 lines. While there are still some EMI around the data lines, it is relatively weak and induces little noise in nearby single-ended traces.

