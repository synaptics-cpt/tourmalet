![Synaptics Concept Prototyping Team](Pictures/Small/CPT_logo.png) 

&copy; 2018 Synaptics, Incorporated. All Rights Reserved

# Known issues with current distribution #

last update: May 4, 2018

### May 4, 2018 ###

- Headphone jack doesn't seem to output anything in either the android or iphone switch setting.
- Volume up button acts identical to power/sleep button (just above it)
- Volume down doesn't seem to do anything
- Internal speaker doesn't seem to work (for media audio); it may work with system sounds
- WiFi is always disabled upon reboot.  Tapping WiFi widget on home screen upon restart will reconnect to the last WiFi connection though.
- Accelerometer / gyroscope / magnetometer chip (the Bosch BMX055 chip) data feed does not seem to be making it into Android stack -- running a 3rd party sensor app (Sensor Kinetics) shows no sensors connected
-  There seems to be some high current draw issue if the device runs continuously for too long.  This manifests itself with the daughterboard and the display getting fairly hot to touch.  For now, we are not exactly sure why this is happenning.  We've measured power consumption when this happens and the device draws about 1.5-2A during this state.   Otherwise, the device seems to run normally.  Presumably, the battery's own power regulation circuitry will kick-in if current draw goes even higher, so there probably isn't any danger in running the device, per se.  That said, please don't run the device unsupervised until a fix for this current draw issue is released.