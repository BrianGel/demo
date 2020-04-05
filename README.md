# Embedded Coding Challenge

short text add*

Hi, we want to give you an impression what working with us means.  So we
created a challenge, that is representative for the things we do and are
passionate about.

Please read [the exercise](#the-exercise) below carefully.

What you have to do:

1. Fork this repository.
1. Implement the exercise.

   Use as many commits as you find helpful; more commits would help us
   understand your method and thought process.

1. Create a Pull Request.

What we will do after that:

1. We say "Thank You" for your effort!
1. Next we review your work.

   Provide you with feedback, what we like and discuss what could be improved.

1. This review will be used in our interview.
1. We are happy to get to know you and like to share our culture with you.

## The Exercise

### Data Extraction from CAN Bus

The following Programming Exercise is about extracting data points from a
CAN-Bus and to convert those to floating point values.

The goal is to create a tiny library/interface/API that takes a CAN
packet/frame and extracts and scales values.

`Input(CAN-packet) => Filtering => Bit Masking/Shifting => Scaling => Output(values)`

### Requirements

- The library (static or dynamic) shall be implemented in C++11 that runs on
  Debian or Ubuntu Linux

- The Library shall use CMake as a build system

- The library shall offer a function that takes a `struct can_frame` as an input
  `(#include <linux/can.h>)`, e.g.  

  - `bool process(const struct can_frame& frame);`

  - The function shall return false in case the can_frame is dropped, true
  otherwise 

- The signature of the result signal shall be:

   - `void resultSignal(uint32_t key, double scaledValue);` 

   - The key's value shall be passed from the call to `configure()` 

- The library shall filter CAN frames based on the CAN identifier.

- CAN frames that don’t match a configured identifier shall be dropped
  silently.  

- The library shall be able to extract values (encoded as length 1 to 64 bits
  little endian unsigned integers) at an arbitrary bit position within the can
  frame payload and scale that value from an output range.

- The library shall offer the following configuration interface:

   ```cpp
   bool configure(
        uint32_t key,
        uint32_t matchingCanId,
        uint32_t bitOffset,
        uint32_t bitLength,
        std::pair<double> outputRange
  )
   ```

   - The function shall return false in case of invalid input values or if the
     configure function is called multiple times with the same key, true
     otherwise 

- The library shall be able to extract multiple values from a single can frame 
- The library shall provide observer pattern for the results (that is
  implemented with a signal/slot library of your choice, e.g.  

   - Boost.Signals2
   - libsigc++ 
   - sigslot 
   - Signals 
   - Qt

- Provide an example program that uses the library with the following examples
    
### Examples

The scaler shall be configured to 

```plain
key = 17 
canID = 0x815 
Bit offset = 15 Bit
length = 9 
Scale output range = [-220..10000] 
```

Example Input1: 

```plain
CAN frame
ID = 0x815
Payload = [0x00 0x11 0x22 0x33 0x44 0x55 0x66 0x77]
```

Expected resuladsdasdasdt:

`Call of resultSignal with parameters (17, 5800.0);`

Example Input2: 

```plain
CAN frame
ID = 0x816
Payload = [0x00 0x00 0x00 0x00]
```

Expected result:

`resultSignal is not called because the configured CAN ID does not match`
