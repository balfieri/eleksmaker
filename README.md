I followed Marco Reps' build for the Eleksmaker A3 laser engraver.  I, too, am using it primarly for 
PCBs.

I found this to be useful as a pre-start: https://github.com/jandelgado/eleksmaker_a3

Downloaded https://github.com/gnea/grbl/releases/tag/v1.1f.20170801 
brew install avrdude

Decided to just flash the pre-built .hex file from the command line:

avrdude -c arduino -b 57600 -P /dev/cu.usbserial-1420 -p atmega328p -vv -U flash:w:grbl_v1.1f.20170801.hex

Test connection:

screen /dev/cu.usbserial-1420 115200
$         - to get help
ctrl A \  - to exit

Check Settings:

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


Current Settings:

$30=1000  // default, note that this is max power
$32=1
$100=80
$101=80
$102=80
$120=200
$121=200
$122=200

Testing Laser:

Put on glasses.

To Turn laser on and off (make sure the weak light button is not pressed):

M3     ; constant laser power mode
$32=0  ; Laser mode off
S250   ; 250 = 1/4 POWER ;)     aka "spindle speed"
S0     ; OFF

M4     ; switch back to dynamic laser power mode


Tool On:
M42 P15 S1

Tool Off:
M42 P15 S1


Download Marco's Example PCB:

https://reps.cc/?p=5

test_pcb.gcode
test_pcb.brd (Eagle)
test_pcb.png (2400 dpi)


Install Latest LaserWeb:

https://laserweb.yurl.ch/documentation/installation/33-install-osx
https://github.com/LaserWeb/LaserWeb4-Binaries/releases
https://github.com/LaserWeb/LaserWeb4-Binaries/releases/download/untagged-4818330b6baa8213d4a7/LaserWeb-v4.0.996-130.dmg

Manual Mode:

Run LaserWeb
Fool around with manual "jogging".
Make sure can't go too far.


Load test_pcb and Laser It:
