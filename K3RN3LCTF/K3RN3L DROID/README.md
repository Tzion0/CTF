# K3RN3L DROID

Our goal is to retrieve the valid pin number to combine with another half of the flag: `flag{K3RN3L_DR0ID_%s}`

The only way to view `validPinMessage` is through `valid_pin`. We can see `label5` is comparing the **pinCode** and jump to `valid_pin` if matched. The table below have references for intructions we need.

```
Mnemonic        Condition tested  Description  
jo              OF = 1            overflow 
jno             OF = 0            not overflow 
jc, jb, jnae    CF = 1            carry / below / not above nor equal
jnc, jae, jnb   CF = 0            not carry / above or equal / not below
je, jz          ZF = 1            equal / zero
jne, jnz        ZF = 0            not equal / not zero
jbe, jna        CF or ZF = 1      below or equal / not above
ja, jnbe        CF or ZF = 0      above / not below or equal
js              SF = 1            sign 
jns             SF = 0            not sign 
jp, jpe         PF = 1            parity / parity even 
jnp, jpo        PF = 0            not parity / parity odd 
jl, jnge        SF xor OF = 1     less / not greater nor equal
jge, jnl        SF xor OF = 0     greater or equal / not less
jle, jng    (SF xor OF) or ZF = 1 less or equal / not greater
jg, jnle    (SF xor OF) or ZF = 0 greater / not less nor equal 
```

Apparently the length of **pinCode** was 8:
```
call strlen
cmp rax, 0x8
jne invalid_pin_length 
```

### pinCode 1:
```
mov r9b, [pinCode + 0x0]
cmp r9b, 0x30
jne invalid_pin
```
We can see it is comparing with `0x30` and jump to `invalid_pin` if not equal. So the first pinCode was `0x30` which is equivalent to 0 in ASCII.

#### Flag in progress: flag{K3RN3L_DR0ID_0}

### pinCode 2:
```
mov r9b, [pinCode + 0x1]
cmp r9b, 0x34
jne invalid_pin
```
Same as first one, so the second pinCode was `0x34` which is equivalent to 4 in ASCII.

#### Flag in progress: flag{K3RN3L_DR0ID_04}

### pinCode 3:
```
mov r9b, [pinCode + 0x2]
cmp r9b, 0x37
jg invalid_pin
cmp r9b, 0x30
jne invalid_pin
```
We can see it is comparing with `0x37` and jump to `invalid_pin` if it was greater than `0x37`, in other words, the correct pinCode was < 0x37.
Now for the second comparison, the instruction was jump if not equal, which means the correct pinCode was `0x30`, equivalent to 0 in ASCII. 

#### Flag in progress: flag{K3RN3L_DR0ID_040}

### pinCode 4:
```
mov r9b, [pinCode + 0x3]
cmp r9b, 0x39
je invalid_pin
cmp r9b, 0x30
jne invalid_pin
```
The first comparison compare if the pinCode is equal to `0x39`, jump to `invalid_pin`, which is useless for us. For second comparison, again, jump to `invalid_pin` if not equal, so the correct pinCode was `0x30`, equivalent to 0 in ASCII.

#### Flag in progress: flag{K3RN3L_DR0ID_0400}

### pinCode 5:
```
mov r8b, [pinCode + 0x4]
cmp r8b, 0x31
jb invalid_pin
cmp r8b, 0x32
jge invalid_pin
```
The first comparison compare if the pinCode was below than `0x31`, jump to `invalid_pin`. So the pinCode must be >= 0x31. For second comparison, if the pinCode was greater or equal to `0x32`, jump to `invalid_pin`. Which means the correct pinCode is 0x32 > x >= 0x31. Thereforce the `0x31` was the correct pinCode, equivalent to 1 in ASCII.

#### Flag in progress: flag{K3RN3L_DR0ID_04001}

### pinCode 6:
```
mov r8b, [pinCode + 0x5]
cmp r8b, 0x30
jbe invalid_pin
cmp r8b, 0x32
jae invalid_pin
```
First comparison check if pinCode was below or equal to `0x30`, which means the pinCode must be > 0x30. The second comparison check if the pinCode is above or equal to `0x32`, hence the correct pinCode is `0x31`, equivalent to 1 in ASCII.

#### Flag in progress: flag{K3RN3L_DR0ID_040011}

### pinCode 7:
```
mov r8b, [pinCode + 0x6]
cmp r8b, 0x39
jne invalid_pin
```
Again, jump if not equal. So the correct pinCode was `0x39`, equivalent to 9 in ASCII.

#### Flag in progress: flag{K3RN3L_DR0ID_0400119}

### pinCode 8:
```
mov r8b, [pinCode + 0x7]
cmp r8b, 0x36
jl invalid_pin
cmp r8b, 0x36
jg invalid_pin
```
The first comparison check if the pinCode was less than `0x36`, means the correct pinCode is >= 0x36. For second comparison, it check if the pinCode is greater than 0x36, hence the correct pinCode was `0x36`, equivalent to 6 in ASCII.

#### Final Flag: flag{K3RN3L_DR0ID_04001196}
