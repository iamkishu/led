## led

Linux boards often have LEDs that can be controlled from userspace. Out of the
box, the Raspberry Pi has one such LED labeled ACT or OK, the BeagleBone has
four, user led 0 through 3. Some systems allow additional off-board LEDs to be
added at runtime using device tree overlays.

The features supported by these LEDs varies from system from system. One system
may allow the LEDs to be turned on and off while the next system will support
additional fetaures such as heartbeat or hardware accelerated blinking.

This module enables these LEDs to be controlled by JavaScript.

## Installation

    $ [sudo] npm install led

## Usage


**Blink all user LEDs on the BeagleBone Black five times a second**

```js
var Led = require('led');

['usr0', 'usr1', 'usr2', 'usr3'].forEach(function (name) {
  new Led('beaglebone:green:' + name).blink(100, 100);
});
```

**Heartbeat all user LEDs leds on the BeagleBone Black**

```js
var Led = require('led');

['usr0', 'usr1', 'usr2', 'usr3'].forEach(function (name) {
  new Led('beaglebone:green:' + name).heartbeat();
});
```

**Blip all user LEDs on the BeagleBone Black once every two seconds**

```js
var Led = require('led');

['usr0', 'usr1', 'usr2', 'usr3'].forEach(function (name) {
  new Led('beaglebone:green:' + name).blink(1, 1999);
});
```

**Turn the ACT LED on the Raspberry Pi on for one second**

```js
var Led = require('led'),
  led = new Led('led0');

led.on();

setTimeout(function () {
  led.off();
}, 1000);
```

Although it may not be immediately obvious, the four LEDs on the BeagleBone
Black will continue to blink, heartbeat, and blip after the corresponding
programs have terminated. All the heavy work involved in controlling the
LEDs has been off-loaded to the operating system and the number of CPU cycles
required to control the LEDs is minimized.

## How does it work?

Linux systems often have files representing LEDs that appear in
/sys/class/leds. Such LEDs can be controlled by writing the appropriate
values to the appropriate files. More information can be found
[here](https://www.kernel.org/doc/Documentation/leds/)

## API

**Led(name)** Returns a new Led object which can be used to control the LED
with the specified name. The name to use for a particular LED is the name of
the corresponding directory in /sys/class/leds. Examples are led0 on the
Raspberry Pi and beaglebone:green:usr0, beaglebone:green:usr1,
beaglebone:green:usr2, and beaglebone:green:usr3 on the BeagleBone or
BeagleBone Black.

**on()** Turn the LED on.

**off()** Turn the LED off.

**heartbeat()** Heartbeat the LED.

**blink(delayOn, delayOff)** Blink the LED. delayOn and delayOff specify the
on and off time in milliseconds.

**delayOn(val)** Modify the on time for a blinking LED to the specified value
in milliseconds.

**delayOff(val)** Modify the off time for a blinking LED to the specified value
in milliseconds.

## Example - Device tree overlay

The example directory contains a device tree overlay called myled.dto which can
configure P9_12 on the BeagleBone Black as a pin for controlling an LED from
userspace. The script myled performs all the necessray setup and after
everything has been setup correctly, there should be an LED called 'my:red:led'
in /sys/class/leds.

The following program can be used to let 'my:red:led' glow dimly for a second
and then brighlty.

```js
var Led = require('led'),
  led = new Led('my:red:led'),
  dim = true;

led.blink(1, 9);

setInterval(function () {
  if (dim) {
    led.delayOn(9);
    led.delayOff(1);
  } else {
    led.delayOn(1);
    led.delayOff(9);
  }

  dim = !dim;
}, 1000);
```

