# Knight Vault
### Task source: https://github.com/Tzion0/CTF/tree/master/KnightCTF

By importing the ELF executable into Ghidra, we quickly noticed that the code will encode our input, store into a variable (We will call it temp by now) and looping through each character of temp to do final comparison with the encoded flag.

The encoding algorithm:
```
  for (c = 0; inpt[c] != '\0'; c = c + 1) {
    temp[c] = inpt[c] + -10;
    if (temp[c] == 'A') {
      temp[c] = '*';
    }
  }
```

Now, let's see what's the encoded flag:
```
local_438 = 0x554569713c4a392a;
local_430 = 0x556a455d556b456f;
local_428 = 0x555a45455d553b63;
local_420 = 0x66375e5558454560;
local_418 = 0x5f2a59375e556f46;
local_410 = 0x735d44;
```
After unpacking it, we will get: 
```
*9J<qiEUoEkU]EjUc;U]EEZU`EEXU^7fFoU^7Y*_D]s
```

My Approach to solve this is just simply reverse back the encoded flag. So we will loop through each character of encoded flag, then we will now `+ 10` instead of `- 10` and if any of those character matched `*`, we will change back to `A`.

### Final Code
```
#!/usr/bin/env python3
from pwn import *

ct = list('*9J<qiEUoEkU]EjUc;U]EEZU`EEXU^7fFoU^7Y*_D]s')

for x in range(len(ct)):
    ct[x] = chr(ord(ct[x]) + 10)
    if ct[x] == '*':
        ct[x] = 'A'

print(''.join(str(i) for i in ct))
```

### Flag
```
4CTF{sO_yOu_gOt_mE_gOOd_jOOb_hApPy_hAc4iNg}
```
#### Note: The flag i get apparently not perfect, i solve it by replacing some characters manually