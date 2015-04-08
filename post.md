## ARM (Advanced RISC Machine)
They power your smart phone. Their in your smart home. People build wearable devices and drones with them. ARM architecture has become the defacto standard for things requiring micro processing (or micro control), and low energy consumption.

You can build ARM powered devices, too! Want to build the next wearable with built in wireless? How about your very own LED light show? There are infinite possiblities with ARM - and in this four (4) part series, I will go over what's needed to build ARM powered devices.


 - Part 1 covers a basic design tools, PCB fabrication, assembly of your board, and a basic ARM board design.
 - Part 2 will show you how to program and debug the ARM chip.
 - Part 3 goes over common protocols, such as SPI, I2C, and USB
 - Part 4 implements basic wireless communications (Bluetooth Low Energy, ZigBee/Xbee)

Notice: You should have at least some familiarity with electronics in order to gain the most from this article. Knowledge of electrical conductivity, and what a resistor, capacitor, and diode do should be enough to get you started.

## Design tools
I would recommend using either EAGLE PCB. The Standard edition let's us do real PCB design and schematic work, while having an extensive community providing all sorts of tools, footprints, and projects using the EAGLE PCB file format. There are many other PCB design tools out there, such as KiCAD, and Fritzing... but for the sake of this multi-part article, we will focus on EAGLE PCB Standard edition.


## PCB Fabrication
PCBs (Printed Circuit Board) come in all shapes and sizes. Here's a few things you should know.

### What's in a PCB design, anyways?
PCBs are commonly made of one (1), two (2), four (4), or eight (8) layers. Much more complicated designs, such as the ones that make your smart phone, can be even more layers. Generally, the less area and layers your design uses, the cheaper your design will be to fabricate.

A layer of fiber glass usually has some copper clad adhered to both sides of the board. A PCB design is simply composed of traces (copper lines) that are later printed on to copper clad, and used to protect the bits of copper that should remain when the entire copper clad is submerged in an etchant (Ferric Chloride). Anything that isn't a trace is usually disolved.

Drill holes are made, and copper plugs are inserted where traces of copper between two or more layers need to be electrically connected. These are referred to as vias. Through-hole vias go through the entire board (cheaper), and stacked vias are meant to connect two or more layers without disturbing unconnected layers (very expensive).

PCBs are then usually covered in something called solder mask. Solder mask is what gives a PCB it's color (green, yellow, black, purple, etc), and serves to keep the metallic solder from flowing to places it shouldn't when melted.

Finally, if your design includes text, it's generally printed on to your PCB as silk screen. This is the (usually) white painted text you see all over a PCB.

### What guidelines should I keep in mind?
When designing a PCB, you can make it any size you want. Larger PCBs will cost more than smaller PCBs, so try and design your PCB to be sized enough to fit your components, without too much excess. 

The traces, vias, drill holes, and component pads are usually measured in mils. Mils are defined as 1/1000 of an inch. Traces are generally considered safe to fabricate at 10 mils width, with 10 mil vias that have 12mil thick rings of copper, and are at least 10mils away from other copper on the board.

Some PCB fabricators can offer smaller sizes, such as 5mil trace width/spacing, with 8mil via holes (20mil diameter total). Always check the PCB fabricator's specification page to see what they can offer.

Try to keep copper traces, drill holes, and components at least 10 to 20mils away from the edge of the PCB (with the exception of connectors, such as USB ports). This reduces the risk of damage to the PCB during fabrication.

As you place components on your board, and start connecting traces, you may find it indispensable to run Design Rule Checks (DRC). EAGELE PCB lets you select a DRC file, such as the one provided by a PCB fabricator, to check for potential issues in your PCB design.

If something is too close to another trace, or something is overlapping where it shouldn't, the DRC tool will help you catch and fix it before your design is sent off to be fabricated.

### How do I get my boards made?

Once you have a board design, it is relatively easy to get a few prototype PCBs made. OSHPark is the best place to have a small amount of PCBs fabricated and shipped to your home for a reasonable price. OSHPark also offeres a DRC file, so you can check your design before uploading it to their service. You can order in sets of 3, and shipping is generally free. Expect about a 10 day turn around.

## Assembling your board
A PCB will need to have solder applied, components placed, and then re-flowed. I would recommend Chip Quik brand low temperature lead free paste. Simply apply a thin coating to each exposed copper (or gold) pad on your PCB, place your components within the hour (before the paste dries), and heat the assembled board at 330F inside a reflow oven, reflow gun, or even a toaster oven, until the solder paste liquifies, and becomes shiney metal. Any solder bridges or gaps can be reworked with a clean, fine tiped soldering iron, some cleaning flux, and a bit of solder. 

Warning: Solder fumes, solder paste, and hot equipment are all dangerous. Please use a well ventilated location, wear proper PPE (Personal Protective Equipment), and NEVER re-use reflow equipment to heat food back up. If you decide to use a toaster oven to re-flow boards, it must never be used to re-heat food, since it will become contaminated with potentially hazardous materials. Solder paste should generally be refrigerated and kept in an air tight container when not in use. NEVER store solder paste in the same place as your food! There are cheap mini fridges you can purchase to store solder paste.

(If you would rather skip this part, you can also just buy a pre-assembled board and follow along with the rest of this article.)

## ARM Board Design
An ARM micro controller has a few basic requirements - some capacitors, some resitors, a crystal clock, and a few pins broken out so we can program it. This is generally applies to other micro controllers as well.

For ease of use, we can design around an ARM micro controller that includes USB support, so we do not need to worry about additional complexities.

The Atmel SAM D21 line of ARM Cortex M0+ chips are easy to source, easy to program, and are reasonably priced. The FemtoUSB is based on the ATSAMD21E18A, and is provided as Open Source Hardware (OSH). The design files, bill of materials (BoM), and schematics are available on github as a breadboard friendly design, and a very tiny PCB design. https://github.com/femtoio/femto-usb

This micro controller can be given a small bit of logic to act as a boot loader, and let us upload our main program via USB. You will need to connect the Atmel ICE programmer to the Serial Wire Debug (SWD) pins of your micro controller. While one USB cable powers the Atmel ICE programmer, another USB cable must be used to power your micro controller via USB. See the femto-usb README for instructions on connecting the Atmel ICE programmer to your FemtoUSB micro controller.

You will need a Windows machine to install Atmel Studio.

Once you have a good board hooked up, and Atmel Studio running, open up the Device Programmer dialog by navigating to the Tools -> Device Programmer menu item.

In the Device Programmer dialog, select "ATMEL-ICE" as the tool, select "ATSAMD21E18A" as the device (double check to be certain you read this right), and select "SWD" as the interface. Click the "Apply" button. Set the "SWD Clock" value to anything from 32KHz to 2MHz and click "Set". 2MHz is plenty fast, but if you ever run into issues reading the chip, try going as low as 32KHz.

You should now click the "Read" button under "Device Signature" to see if you get back a hex value. The board's blue LED will go from semi-off, to bright blue. If you get a message saying your device is unreadable, double check your connections, double check your solder work, and try again.

Assuming you got back a hex value under "Device Signature", congrats! You appear to have a functional board!

Let's now burn the bootloader.

Click the "Memories" section of the Device Programmer dialog. In the "Flash" area, use the file navigator button to browse to the downloaded (and extracted) `Atmel-42366-SAM-BA-Bootloader-for-SAM-D21_ApplicationNote_AT07175/load sam-ba/samd21e18a/` folder, and select `samd21_sam_ba_usbcdc.hex` Assert the "Erase Flash before programming" and "Verify Flash after programming" checkboxes are checked. Click "Program". After a brief moment, the Device Programmer dialog should come back with:

```
Erasing device... OK
Programming Flash...OK
Verifying Flash...OK
```

Success! You now have a board with the SAM-BA bootloader! If you're on a Windows machine, you may notice Windows will now attempt to install drivers for the new device. The Windows driver install tool should succeed in finding the correct drivers. You can now see your board on Linux/Mac as well. Simply plug it in via USB, and run the 'lsusb' command from a terminal. You will see an Atmel 'SAM-BA' device listing.

In the next article, I will go over a basic 'blinking LED' example and show you how to upload, debug, and work with your ARM powered board.




