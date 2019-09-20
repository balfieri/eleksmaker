Note: this is a work in progress.  


I followed Marco Reps' build for the Eleksmaker A3 laser engraver.  I, too, am using it primarly for 
PCBs.  A couple differences from his build:

- I am using the voltage-regulator circuit that came with the laser, rather than trying to drive the 
  laser diode directly.  I still use an FQP30N06L N-channel MOSFET to control the power to the laser.
  This kind of MOSFET has a gate that is voltage-controlled by the PWM signal.  Most MOSFETs
  are current-controlled.  This means no resister is required between the Arduino and the 
  gate of the FQP30N06L. [By the way, the P-channel version is FQP27P06.]
- I am using the newer Trinamic TMC2209 rather than TMC2130.  
  I snipped the same four pins that Marco did, but they have slightly different meanings with the
  TMC2209.  Regardless, it still forces the TMC2209 to use good defaults, which means
  I didn't have to add the extra Arduino to control the Trinamic.  

I also found this to be useful as a pre-start: https://github.com/jandelgado/eleksmaker_a3.  It
references Marco's video and settings.

Downloaded https://github.com/gnea/grbl/releases/tag/v1.1f.20170801.
I've included the pre-built .hex file here, which is all you need.

Flash the pre-built .hex file from the command line.  This must be done each
time you power-on the Eleksmaker board.

brew install avrdude            (one-time)
ls /dev/cu\*
avrdude -c arduino -b 57600 -P /dev/cu.usbserial-1420 -p atmega328p -vv -U flash:w:grbl_v1.1f.20170801.hex

Watch closely and make sure it actually worked.  It should write the firmware and then read it back to
verify it.  If it finishes in a few seconds, it didn't work.


Manual Tests Outside LaserWeb:

Test Connection 

<pre>
screen /dev/cu.usbserial-1420 115200
$         - to get help
ctrl A \  - to exit
</pre>

Check Settings:

<pre>
screen /dev/cu.usbserial-1420 115200
$$ ENTER
                Meaning                 Marco's Settings        Eleksmaker.com Settings
$0=10           step pulse, usec
$1=25           step idle delay, msec
$2=0            step port invert mask
$3=0            step dir  invert mask   // 7 
$4=0            step enable invert bool
$5=0            limit pins invert bool
$6=0            probe pin invert bool
$10=1           status report mask      // 3
$11=0.010       junction deviation, mm
$12=0.002       arc tolerance, mm
$13=0           report inches, bool
$20=0           soft limits, bool
$21=0           hard limits, bool       // 1
$22=0           homing cycle, bool      // 1
$23=0           homing dir invert, mask
$24=25.000      homing feed, mm/min     // 1000.000
$25=500.000     homing seek, mm/min     // 2000.000
$26=250         homing debounce, msec
$27=1.000       homing pull-off, mm     // 10.0
$30=1000        max spindle speed, rpm
$31=0           min spindle speed, rpm
$32=0           laser mode, bool        // 1                    1
$100=250.000    x, step/mm              // 80.000               80
$101=250.000    y, step/mm              // 80.000               80
$102=250.000    z, step/mm              // 80.000               80
$110=500.000    x max rate, mm/min      // 5000.000
$111=500.000    y max rate, mm/min      // 5000.000
$112=500.000    z max rate, mm/min      // 5000.000
$120=10.000     x accel, mm/sec^2       // 100.000              200
$121=10.000     y accel, mm/sec^2       // 100.000              200
$122=10.000     z accel, mm/sec^2       // 100.000              200
$130=200.000    x max travel, mm        // 5000.000
$131=200.000    y max travel, mm        // 5000.000
$132=200.000    z max travel, mm        // 5000.000
</pre>

<p>
The GRBL firmware will actually remember the last settings after power-off/on, 
so you can set them once and forget about them.  This is true even if you 
re-flash the GRBL code on the Aruino, though may not be true if you re-flash with
a different version.  In the end, this is my configuration, which is 
the same as Marco's:

<pre>
$0=10
$1=25
$2=0
$3=7
$4=0
$5=0
$6=0
$10=3
$11=0.010
$12=0.002
$13=0
$20=0
$21=1
$22=1
$23=0
$24=1000.000
$25=2000.000
$26=250
$27=10.000
$30=1000
$31=0
$32=1
$100=80.000
$101=80.000
$102=80.000
$110=5000.000
$111=5000.000
$112=5000.000
$120=100.000
$121=100.000
$122=100.000
$130=5000.000           // sometimes I set these much lower to avoid hitting the end
$131=5000.000
$132=5000.000
</pre>

Testing Laser Using a Terminal Program:

Put on glasses.  Use high-quality UV-resistant glasses, not the ones that came with
the Eleksmaker.

To Turn the laser on and off (make sure the weak light button is not pressed):

<pre>
$32=0  ; 
M3     ; constant laser power mode
S250   ; 250 = 1/4 POWER ;)     aka "spindle speed"
S0     ; OFF
M4     ; switch back to dynamic laser power mode
</pre>


Using LaserWeb

We've confirmed that things are basically working and got all the GRBL settings correct.
Now it's time to switch to LaserWeb.

Install Latest (one time):

https://laserweb.yurl.ch/documentation/installation/33-install-osx<br>
https://github.com/LaserWeb/LaserWeb4-Binaries/releases<br>
https://github.com/LaserWeb/LaserWeb4-Binaries/releases/download/untagged-4818330b6baa8213d4a7/LaserWeb-v4.0.996-130.dmg<br>
</pre>

LaserWeb Connect to Device:

Always remember to go into Communication and Connect to your Eleksmaker device.
It's easy to forget to do that.  It must be done each time you start LaserWeb.


LaserWeb configuration settings (one-time):

<pre>
GCode Start:
G21
G90
M4 S0

GCode End:
M5

GCode Homing
$H

Tool On:
M42 P15 S1

Tool Off:
M42 P15 S1

Laser Intensity:
S

PWM Min S Value:
0

PWM Max S Value:
1000

Check-Size Power:
25%

Tool Test Power:
100%

Tool Test Duration:
5000 ms
</pre>

Create a "Goto XY Zero" macro that has these commands.  You can create
other macros, but this is the only one I use.

<pre>
G0 X0Y0
</pre>


Manual Testing:

I hope you didn't forget to Connect to your device!

Go to "Control".
Fool around with manual jogging.

Use "Set Zero" to set the 0,0 point after you position over the left-bottom
corner of your substrate.

Use "Laser Test" to do a 5-second test of laser at 25% throttle.
You can focus the laser during these 5 seconds.  If you usually need
more time, change the 5000ms above to 10000ms.

Run Marco's Test Case:

Download Marco's Example PCB from: https://reps.cc/?p=5 (one-time).

<pre>
test_pcb.gcode
test_pcb.brd (Eagle)
test_pcb.png (2400 dpi)
</pre>

Go to Files and click on Add Document.
Select test_pcb.gcode.
You should see a 50x50 mm image that includes a zebra head.

Go back to Control
Click on Run Job.
The first time you run this, you might want to use a piece
of plywood.  That implies that you would first focus the laser
using "Laser Test" above.

Once you know it works, substitute a blank pre-sensitized PCB.
I recommend focusing the laser using the un-sensitized side, then
flipping it over to etch the pre-sensitized side.  
The laser is strong enough to cut the photoresist.

* Cut board into pieces
* Sand the board edges with 400 grit sandpaper to remove burrs and flatten edges
* Polish each board up to 10000 grit
* Clean the board with soap and water to remove loose particles
* Treat the board with muriatic acid (weaker HCl) to remove oxides and sulfates; can also try vinegar
* Spin-coat thin layer of photoresist
* Laser the board
* Use a sponge to apply FeCl - saves etchant
