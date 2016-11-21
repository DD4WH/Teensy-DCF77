# Teensy-DCF77

https://de.wikipedia.org/wiki/DCF77

Receive and decode atomic clock time signals from longwave station DCF77 on 77.5kHz with minimal hardware effort. Fully hackable for other longwave time signal stations around the world (LF below 96kHz).

Based on an idea by Frank Boesing and DD4WH

[![Teensy DCF77 video](http://img.youtube.com/vi/-SrumhKsAKk/0.jpg)](http://www.youtube.com/watch?v=-SrumhKsAKk)

HARDWARE (Option 1 - very low budget < 1$/€):
- a few meters of wire soldered to the MIC INPUT (optionally add a 100nF cap)
- connect GND of the MIC input to a ground connection (the heating for example): NEVER EVER USE THE GND CONNECTION OF YOUR MAINS CONNECTOR! (Do not even think about that!)
--> this option could possibly not work, if you are too far away from Frankfurt or have too much local noise from all your plasma TVs, switching power supplies etc.

HARDWARE (Option 2 - low budget < 5$/€)
- ferrite rod with a few hundred windings --> measure inductance L
- choose parallel capacitor with capacitance C, so that 1 / (2 * PI * SQRT (L * C) == 77.5kHz
should be in the range of a few nF
- connect the parallel circuit to the MIC input (via a 100nF cap to prevent MIC bias short circuit!) and MIC GND
--> this works very well here about 500km from Frankfurt inside a building with fairly high noise level
![](https://github.com/DD4WH/Teensy-DCF77/blob/master/IMG_2183.JPG)
HARDWARE (Option 3 - about 10$ / €)
- buy a DCF77 receive module and connect the output to the Teensy LINEIN
--> this should be a bullet-proof solution, if you are inside the 2000km circle around Frankfurt

SOFTWARE:
- the Teensy audio board takes the MIC input signal and digitizes it with 176400ksps (so you are able to receive up to 88kHz)
- that is a Direct Sampling Receiver like the really expensive and professional SDRs
- the signal is bandpass-filtered around 77.5kHz
- the signal is fed to a 1024-point FFT for visual inspection AND for peak analysis in order to extract the time information bits
- the signal is multiplied with a local oscillator working at 76900Hz
that´s the principle of a DC (direct conversion) receiver, I think.

Audio:
- the signal is lowpass-filtered (however, the biquads are not very good at low frequencies, be careful here!)
- you can hear the audio signal from DCF77 with a 600Hz tone (77500Hz-76900Hz)


The "BIN" of FFT-Data which corresponds to 77.5kHz is now used for the remaining steps:


AGC:
The fft-bin-value is used to generate a very slow moving average. It is compared against upper and lower constants. A too high value decreases the gain, a too low value increases it.

Decoding:
A factor of 10 faster moving average is used to detect the bits. The signal is reduced to 25% at the beginning of a second for 100 or 200 milliseconds. These pauses result in a decreasing average value. A decreasing of more than 90ms is detected as a bit - if the duration is more than 150ms, it is "1", otherwise a "0". 




