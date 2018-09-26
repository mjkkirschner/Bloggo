### 16bits and bootloaders.

#### 16 bit upgrade.

At the end of the last post I was describing what would need to be 
done to support a larger address space in the design. I considered 
adding a second memory address register, but as I started to draw out
the consequences of that change I found I would need to change all the
existing microcode for each instruction if I wanted them to take advantage
of the larger address range - and then I might have some instructions with
limnited address support. Instead I decided to simply change all registers to
16 bits - because everthing is defined in the simulator using the hardware description lanaguge
apis - and then eventually translated to verilog - this change wasn't so painful. 

 Heres the diff:
 https://github.com/mjkkirschner/8chipsimulator/compare/debugParts...16bitAddressing
 
 The most difficult thing in this change was actually to support drawing the larger memory space.
 I ended up using react-virtualized - which worked great to limit the displayed memory cells. We
 went from needing to draw a SRAM of 256(bytes) cells to one of 65,536 (2bytes each).
 
 I also added an api for writing hex values to RAM since 16 bit arrays were getting hard to look at.
 
 An example looks like this:
 ```js
 ram.writeData(0, hex2BinArray("0x0010").map(x => Boolean(x))); //Load SPI control reg.
 ram.writeData(1, hex2BinArray("0x0001").map(x => Boolean(x))); //put a 1 in LSB... this starts coms.

 ram.writeData(2, hex2BinArray("0x0010").map(x => Boolean(x))); //Load SPI control reg.
 ram.writeData(3, hex2BinArray("0x0000").map(x => Boolean(x))); //reset coms control.
```

This reminds me I should really start on an assembler soon programs can be written
in mnemonics, like `LOADSPICON` - instead of `hex2BinArray("0x0010")` in the example above.
The assembler should also support symbols and labels so memory locations can be referred to by names.
But thats for another post.
....
