# LoRaGo Firmware

This is the firmware for the Uputronics LoRaGo device which is a USB LoRa receiver for use with an Android phone or tablet or connected to a Windows PC.

Suitable host software includes:

[HAB Explora for Android phones](https://www.daveakerman.com/?page_id=2494)

[HAB PADD for Android tablets](https://www.daveakerman.com/?page_id=2552)

[HAB Base for Windows](http://www.daveakerman.com/?page_id=2760)

[Lora Serial Gateway](https://github.com/daveake/LoRaSerialGateway)



Programming
---------------

Connect the device to a computer with the Arduino IDE installed.

Open the LoRaOTG.ino file in the Arduino IDE.

**IMPORTANT**

Use the Tools menu to set the board type to <u>Adafruit Feather 32u4</u> (you will need to have installed the Adafruit board definitions first) or <u>Lilypad Arduino USB</u>.

Then use the Tools menu to select the serial port that the device has enumerated as.

Finally, select the Upload option to program the device.

**NOTES**

During the programming sequence, the device is reset so will disappear from the list of connected serial devices.  The reset causes the bootloader to start, and that causes the device to reconnect to the host computer.  The Arduino IDE waits for this to happen and, if this process is not delayed, then it will automatically reconnect to the device and successfully program it.  However there are some instances where it does not wait long enough, in which case the Arduino IDE will report "No programmer connection".  If this happens, try again.  If it continues to happen, see below.

First, use of a USB hub between the host PC and device is not recommended since it can delay the reconnection.  So, connect the device directly to a USB port on the PC.

Second, if VMWare is installed and running then VMWare may pop up a dialog asking if you want to connect the USB device to the host computer or the virtual machine.  Similar software such as Virtual Box may do the same.  This delays the process, causing the Arduino IDE to time out.  So, stop or suspend any virtual machines that you are running.

If the program step still times out, try a different PC.



Serial Protocol
---------------

If you want to write your own software then you need to know the serial protocol for controlling the device.  The device appears as a serial port at 57600 baud), and will send status information and incoming LoRa packets without being polled.  Each of these is of the form

something=value<CR><LF>

The somethings are, currently:

	- CurrentRSSI=<RSSI>
	- Message=<telemetry>
	- Hex=<hex packet e.g. SSDV>
	- FreqErr=<error in kHz>
	- PacketRSSI=<RSSI>
	- PacketSNR=<SNR>

The serial interface accepts a few commands, each of the form

~<command><value><CR>

(a trailing <LF> can be sent but is ignored).  Accepted commands are responded to with an OK (* <CR> <LF>) and rejected commands (unknown command, or invalid command value) with a WTF (? <CR> <LF>)

The current commands are:

	- F<frequency in MHz>
	- M<mode>
	- B<bandwidth>
	- E<error coding from 5 to 8>
	- S<spreading factor from 6 to 11>
	- I<1=implicit mode, 0=explicit mode>
	- L(low data rate optimisation: 1=enable, 0=disable)

The supported modes are:

0 = (normal for telemetry)  Explicit mode, Error coding 4:8, Bandwidth 20.8kHz, SF 11, Low data rate optimize on
1 = (normal for SSDV)       Implicit mode, Error coding 4:5, Bandwidth 20.8kHz,  SF 6, Low data rate optimize off
2 = (normal for repeater)   Explicit mode, Error coding 4:8, Bandwidth 62.5kHz,  SF 8, Low data rate optimize off	

Bandwidth value strings can be 7K8, 10K4, 15K6, 20K8, 31K25, 41K7, 62K5, 125K, 250K, 500K

