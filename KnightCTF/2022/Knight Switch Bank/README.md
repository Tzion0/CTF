# Knight Switch Bank
### Task source: https://github.com/Tzion0/CTF/tree/master/KnightCTF

By importing the ELF executable into Ghidra, we quickly noticed that the code will encode our input, store into a variable (We will call it temp by now), encode again and looping through each character of temp to do final comparison with the encoded flag.

The encoding algorithm:
```
  for (; inpt[i] != '\0'; i = i + 1) {
    if ((inpt[i] < 'A') || ('M' < inpt[i])) {
      if ((inpt[i] < 'a') || ('m' < inpt[i])) {
        if ((inpt[i] < 'N') || ('Z' < inpt[i])) {
          if ((inpt[i] < 'n') || ('z' < inpt[i])) {
            temp[i] = inpt[i] + -0x20;
          }
          else {
            temp[i] = inpt[i] + -0xd;
          }
        }
        else {
          temp[i] = inpt[i] + -0xd;
        }
      }
      else {
        temp[i] = inpt[i] + '\r';
      }
    }
    else {
      temp[i] = inpt[i] + '\r';
    }
  }
  for (; temp[x] != '\0'; x = x + 1) {
    temp[x] = temp[x] + '\x02';
  }
```

Great, now we know the algorithm, let's see what is the encoded flag:
```
local_38 = 0x4164485d5549525a;
local_30 = 0x41494447414a644e;
local_28 = 0x4173444476414978;
local_20 = 0x71444479;
local_1c = 0x5f;
```
After unpacking it, we will get: 
```
ZRIU]HdANdJAGDIAxIAvDDsAyDDq_
```
#### Note: I did some adjustment manually because the initial unpacked value looks weird

My approach to solve this is to generate a list of printable string, let them goes through the encoding algorithm, loop each character to compare with encoded flag, if it matched, get its index, print the original not encoded value. Please do take note that my code probably wasn't the best in term of simplicity but at least it works. Let's get it started.

First. initialize the required variables, the `enc` variable is the `temp` variable for above encoding algorithm:
```
#!/usr/bin/env python3
from pwn import *
import string
ct = list('ZRIU]HdANdJAGDIAxIAvDDsAyDDq_')
printable = list(string.printable)
enc = ['A' for i in range(len(printable))]
```

Next, we can just mimic the encoding algorithm we saw in Ghidra:
```
for i in range(len(printable)):
	if printable[i] < 'A' or printable[i] > 'M':
		if printable[i] < 'a' or printable[i] > 'm':
			if printable[i] < 'N' or printable[i] > 'Z':
				if printable[i] < 'n' or printable[i] > 'z':
					enc[i] = ord(printable[i]) - 0x20
				else:
					enc[i] = ord(printable[i]) - 0xd
			else:
				enc[i] = ord(printable[i]) - 0xd
		else:
			enc[i] = ord(printable[i]) + 13
	else:
		enc[i] = ord(printable[i]) + 13

for i in range(len(enc)):
	enc[i] = chr(abs(enc[i] + 0x2))
```

Lastly, loop through each `enc` value to compare with encoded flag:
```
for i in range(len(ct)):
	for x in range(len(enc)):
		if ct[i] == enc[x]:
			print(printable[x], end="")
```

### Final Code
```
#!/usr/bin/env python3
from pwn import *
import string
ct = list('ZRIU]HdANdJAGDIAxIAvDDsAyDDq_')
printable = list(string.printable)
enc = ['A' for i in range(len(printable))]

for i in range(len(printable)):
	if printable[i] < 'A' or printable[i] > 'M':
		if printable[i] < 'a' or printable[i] > 'm':
			if printable[i] < 'N' or printable[i] > 'Z':
				if printable[i] < 'n' or printable[i] > 'z':
					enc[i] = ord(printable[i]) - 0x20
				else:
					enc[i] = ord(printable[i]) - 0xd
			else:
				enc[i] = ord(printable[i]) - 0xd
		else:
			enc[i] = ord(printable[i]) + 13
	else:
		enc[i] = ord(printable[i]) + 13

for i in range(len(enc)):
	enc[i] = chr(abs(enc[i] + 0x2))

for i in range(len(ct)):
	for x in range(len(enc)):
		if ct[i] == enc[x]:
			print(printable[x], end="")
```

### Flag
```
KCTF{So_YoU_ROT_iT_gOOd_jOOb}
```
