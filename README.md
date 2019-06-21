# 
Computer Assignment: DTMF Decoder
Build a DTMF decoder. Your code should accept a tonal sequence and output a string of key presses.

Rules:
* Use a sampling rate of 8000 samples per second.
* Use a set of bandpass filters, not a FFT.
* For full credit, use FIR filters of length 31 or less or second order (two pole) IIR filters. You get partial credit for working implementations that use larger filters.
* You may assume the silence and tonal periods are both multiples of 400 samples. (This is a simplifying assumption.)
* You may not assume the tones or the silence periods are the same length.

## Reference DTMF encoder
Here is a reference DTMF encoder (and the solution to the first part of the assignment).
```
Fs = 8000

#define the keypad and its frequencies
validkeys = '*#0123456789ABCD'
rowfreqs = [697, 770, 852, 941]
colfreqs = [1209, 1336, 1477, 1633]
buttons = {'1':(0,0), '2':(0,1), '3':(0,2), 'A':(0,3),
           '4':(1,0), '5':(1,1), '6':(1,2), 'B':(1,3),
           '7':(2,0), '8':(2,1), '9':(2,2), 'C':(2,3),
           '*':(3,0), '0':(3,1), '#':(3,2), 'D':(3,3)}

def dtmf_encoder(phonenumber, dur = 0.5, silencedur=0.1, Fs=8000):
    """return the DTMF tones for a phone number"""
    t = np.linspace(0,dur,int(dur*Fs),endpoint=False)
    silence = np.zeros(int(silencedur*Fs))
    
    sounds = []
    for key in phonenumber:
        if key.upper() in validkeys:
            r,c = buttons[key]
            fr, fc = rowfreqs[r], colfreqs[c]
            #print key, fr, fc
            sounds.append(np.sin(2*np.pi*fr*t)+np.sin(2*np.pi*fc*t))
            sounds.append(silence)
    return np.concatenate(sounds[:-1]) #drop last silence period  
```

## Automatially test the decoder
test_dtmfdecoder iterates over increasing noise until the dtmfdecoder routine fails. It runs each level 5 times. We reserve the right to change the default values for dur and silencedur; do not assume the current defaults will always be used.
