I followed Marco Reps' build for the Eleksmaker A3 laser engraver.  I, too, am using it primarly for 
PCBs.

I found this to be useful as a pre-start: https://github.com/jandelgado/eleksmaker_a3

Downloaded https://github.com/gnea/grbl/releases/tag/v1.1f.20170801.
I've included the pre-built .hex file here.

brew install avrdude

Flash the pre-built .hex file from the command line.  This must be done each
time you power-on the Eleksmaker board.

ls /dev/cu*
avrdude -c arduino -b 57600 -P /dev/cu.usbserial-1420 -p atmega328p -vv -U flash:w:grbl_v1.1f.20170801.hex

Watch closely and make sure it actually worked.  It should write it then read it back to
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
                                Marco's Settings        Eleksmaker.com Settings
$0=10
$1=25
$2=0
$3=0                            // 7 
$4=0
$5=0
$6=0
$10=1                           // 3
$11=0.010
$12=0.002
$13=0
$20=0
$21=0                           // 1
$22=0                           // 1
$23=0
$24=25.000                      // 1000.000
$25=500.000                     // 2000.000
$26=250
$27=1.000                       // 10.0
$30=1000                        
$31=0
$32=0                           // 1                    1
$100=250.000                    // 80.000               80
$101=250.000                    // 80.000               80
$102=250.000                    // 80.000               80
$110=500.000                    // 5000.000
$111=500.000                    // 5000.000
$112=500.000                    // 5000.000
$120=10.000                     // 100.000              200
$121=10.000                     // 100.000              200
$122=10.000                     // 100.000              200
$130=200.000                    // 5000.000
$131=200.000                    // 5000.000
$132=200.000                    // 5000.000
</pre>

<p>
It seems to actually remember the last settings, so you can set them once.
In the end, this is my configuration, which is the same as Marco's:

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
$130=5000.000
$131=5000.000
$132=5000.000
</pre>

Testing Laser:

Put on glasses.

To Turn laser on and off (make sure the weak light button is not pressed):

<pre>
$32=0  ; 
M3     ; constant laser power mode
S250   ; 250 = 1/4 POWER ;)     aka "spindle speed"
S0     ; OFF
M4     ; switch back to dynamic laser power mode
</pre>


Using LaserWeb

Install Latest (one time):

https://laserweb.yurl.ch/documentation/installation/33-install-osx<br>
https://github.com/LaserWeb/LaserWeb4-Binaries/releases<br>
https://github.com/LaserWeb/LaserWeb4-Binaries/releases/download/untagged-4818330b6baa8213d4a7/LaserWeb-v4.0.996-130.dmg<br>
</pre>

LaserWeb configuration:

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

Create an "Eleksmaker Init" macro that has these commands (we'll change these to Marco's settings later):

<pre>
$30=1000  
$32=1
$100=80
$101=80
$102=80
$120=200
$121=200
$122=200
</pre>

Create a "Goto XY Zero" macro that has these commands:

<pre>
G0 X0Y0
</pre>

Manual Testing:

Go to "Control".
Fool around with manual jogging.
Use "Set Zero" to set the 0,0 point after you position over substrate.
Use "Laser Test" to do a 5-second test of laser at full throttle.
You can focus the laser during these 5 seconds.


Run Marco's Test Case:

Download Marco's Example PCB from: https://reps.cc/?p=5

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

* Polish board up to 10000 grit
* Cut board into pieces
* Sand the board edges with 400 grit sandpaper to remove burrs and flatten edges
* Clean the board with soap and water to remove loose particles
* Treat the board with muriatic acid (weaker HCl) to remove oxides and sulfates; can also try vinegar
* Spin-coat photoresist
* Laser the board
* Use a sponge to apply FeCl - saves etchant
