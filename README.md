
### OFDM MODEM

## How to compile

To test this against Rattlegram 
Clone this repository branch  named "short":

```
git clone -b short https://github.com/aicodix/modem.git
```

Look and read makefile! ;-)
I.e. you will also need these repos in the same directory where you clonned modem repo:
```
git clone https://github.com/aicodix/code.git
git clone https://github.com/aicodix/dsp.git
```

You can use g++ or clang++ compilers. To configure which one - un/comment lines beginning with CXX in makefile.
Compiling - simple run make command.


## Quick start:

Create file ```uncoded.dat``` with ```1360``` bits of random data:

```
dd if=/dev/urandom of=uncoded.dat bs=1 count=170
```

Encode file ```uncoded.dat``` to ```encoded.wav``` [WAV](https://en.wikipedia.org/wiki/WAV) audio file with 8000 Hz sample rate, 16 bits and only 1 (real) channel:

```
./encode encoded.wav 8000 16 1 uncoded.dat
```

Start recording to ```recorded.wav``` audio file and stop after 5 seconds:

```
arecord -c 1 -f S16_LE -r 8000 -d 5 recorded.wav
```

Start playing ```encoded.wav``` audio file:

```
aplay encoded.wav
```

Decode ```recorded.wav``` audio file to ```decoded.dat``` file:

```
./decode decoded.dat recorded.wav
```

Compare original ```uncoded.dat``` with ```decoded.dat```:

```
diff -s uncoded.dat decoded.dat
```
### Testing decode of audio file recorded from Rattlegram

In this case you could probably have to play wich mic sensivity and audio volumes.
Record an audiofile by using come audiorecorder either directly from your Android Rattlegram app or transmitted one from receiver.

To decode file must be WAV audiofile with 8000 Hz sample rate, 16 bits and only one (real) channel.
If not - convert them, f.ex.:

```
sox Rec.wav -r 8000 -b 16 -c 1 Rec_16b.wav
```

Then decode with the same method above:
```
./decode decoded.dat Rec_16b.wav
```

On success you will have similar output with no error messages:

```
symbol pos: 2287
coarse cfo: 1450.06 Hz 
oper mode: 14
call sign: ANONYMOUS
demod .... done
coarse sfo: 7.8106 ppm
finer cfo: 1450 Hz 
Es/N0 (dB): 17.152 17.3675 17.7039 17.6775
bit flips: 0
```

and message text is in decoded.dat file.

### Simulating

Prerequisite: [disorders](https://github.com/aicodix/disorders)

Encode ```uncoded.dat``` to [analytic](https://en.wikipedia.org/wiki/Analytic_signal) audio signal, add [multipath](https://en.wikipedia.org/wiki/Multipath_propagation), [CFO, SFO](https://en.wikipedia.org/wiki/Carrier_frequency_offset), [AWGN](https://en.wikipedia.org/wiki/Additive_white_Gaussian_noise), decode and compare:

```
./encode - 8000 16 2 uncoded.dat 2000 | ../disorders/multipath - - ../disorders/multipath.txt 10 | ../disorders/cfo - - 234.567 | ../disorders/sfo - - 147 | ../disorders/awgn - - -30 | ./decode - - | diff -s uncoded.dat -
```

### Reading

* Robust frequency and timing synchronization for OFDM  
by Timothy M. Schmidl and Donald C. Cox - 1997
* On Timing Offset Estimation for OFDM Systems  
by H. Minn, M. Zeng, and V. K. Bhargava - 2000

