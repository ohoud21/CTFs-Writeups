# Problem
In RSA d is alot bigger than e, why dont we use d to encrypt instead of e? Connect with nc 2019shell1.picoctf.com 25894.

## Hints:

What is e generally?

## Solution:

First, let's connect and take a look:
```bash
nc 2019shell1.picoctf.com 25894

c: 32950117616519929046587008346516160815226057877968826645877629244894576550703256348506203439061505295203799633515445099993043007605978942929707611059139295898928560779566927581824690802223952694648876430195568087941542985697172689806792195383690978801921522072585496265157840551649682755087301755983683372252
n: 74099114834814918792146417274064760545720257518032172605370821290055675566470562747627307093394207274273642059357613912292342527729390889541215737183492285914772379218363377443030342923133329031630599281966215751043211195238363066072994408559349163694780663187759412241361255979462560597819220688635157707679
e: 47783340573247907258398948484619755408139364377787748579400669077930527195174922911335966742161313270753202934412186690896729784807048198327522765534717033966207008874091435642435050747428570101998615752117823200097562785983804453478990702528809092876534071102167763042997863797467826114042844145154128238833
```

We got a ciphertext and a public-key, but we don't know the private-key.
The public-key (```e```) is pretty big, we might get lucky with a small private-key. Let's try to enumerate it:
```python
#!/usr/bin/env python

from pwn import *
import binascii


c = 32950117616519929046587008346516160815226057877968826645877629244894576550703256348506203439061505295203799633515445099993043007605978942929707611059139295898928560779566927581824690802223952694648876430195568087941542985697172689806792195383690978801921522072585496265157840551649682755087301755983683372252
n = 74099114834814918792146417274064760545720257518032172605370821290055675566470562747627307093394207274273642059357613912292342527729390889541215737183492285914772379218363377443030342923133329031630599281966215751043211195238363066072994408559349163694780663187759412241361255979462560597819220688635157707679
e = 47783340573247907258398948484619755408139364377787748579400669077930527195174922911335966742161313270753202934412186690896729784807048198327522765534717033966207008874091435642435050747428570101998615752117823200097562785983804453478990702528809092876534071102167763042997863797467826114042844145154128238833

for d in range(1, 10 ** 8):
    log.info('Try d: {}'.format(d))

    plain = pow(c, d, n)
    
    try:
        if 'picoCTF' in binascii.unhexlify(hex(plain)[2:]):
            log.info('Found d: {}'.format(d))
            log.info('Flag: {}'.format(binascii.unhexlify(hex(plain)[2:])))

            break
    except TypeError:
        pass
```

After few seconds we get:
```bash
...
[*] Try d: 65536
[*] Try d: 65537
[*] Found d: 65537
[*] Flag: picoCTF{bad_1d3a5_3468581}
```

Flag: picoCTF{bad_1d3a5_3468581}