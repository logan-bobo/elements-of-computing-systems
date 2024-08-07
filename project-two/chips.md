**half-adder**: Designed to add two bits
```
CHIP HalfAdder {
    IN a, b;    // 1-bit inputs
    OUT sum,    // Right bit of a + b 
        carry;  // Left bit of a + b

    PARTS:
    Xor(a=a , b=b , out=sum );
    And(a=a , b=b , out=carry );
}
```

```
| a | b |sum|car|
| 0 | 0 | 0 | 0 |
| 0 | 1 | 1 | 0 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | 0 | 1 |
```

**full-adder**: Designed to add three bits
```
CHIP FullAdder {
    IN a, b, c;  // 1-bit inputs
    OUT sum,     // Right bit of a + b + c
        carry;   // Left bit of a + b + c

    PARTS:
        HalfAdder(a=a , b=b , sum=ha1sum , carry=ha1carry );
        HalfAdder(a=c , b=ha1sum , sum=sum , carry=ha2carry );
        Or(a=ha1carry , b=ha2carry , out=carry );
}
```

```
| a | b | c |sum|carry|
| 0 | 0 | 0 | 0 |  0  |
| 0 | 0 | 1 | 1 |  0  |
| 0 | 1 | 0 | 1 |  0  |
| 0 | 1 | 1 | 0 |  1  |
| 1 | 0 | 0 | 1 |  0  |
| 1 | 0 | 1 | 0 |  1  |
| 1 | 1 | 0 | 0 |  1  |
| 1 | 1 | 1 | 1 |  1  |
```

**Add16**: Designed to add two 16 bit numbers
```
CHIP Add16 {
    IN a[16], b[16];
    OUT out[16];

    PARTS:
    HalfAdder(a=a[0], b=b[0], sum=out[0], carry=ha_0_out_carry);
    FullAdder(a=a[1], b=b[1], c=ha_0_out_carry, sum=out[1], carry=fa_1_out_carry);
    FullAdder(a=a[2], b=b[2], c=fa_1_out_carry, sum=out[2], carry=fa_2_out_carry);
    FullAdder(a=a[3], b=b[3], c=fa_2_out_carry, sum=out[3], carry=fa_3_out_carry);
    FullAdder(a=a[4], b=b[4], c=fa_3_out_carry, sum=out[4], carry=fa_4_out_carry);
    FullAdder(a=a[5], b=b[5], c=fa_4_out_carry, sum=out[5], carry=fa_5_out_carry);
    FullAdder(a=a[6], b=b[6], c=fa_5_out_carry, sum=out[6], carry=fa_6_out_carry);
    FullAdder(a=a[7], b=b[7], c=fa_6_out_carry, sum=out[7], carry=fa_7_out_carry);
    FullAdder(a=a[8], b=b[8], c=fa_7_out_carry, sum=out[8], carry=fa_8_out_carry);
    FullAdder(a=a[9], b=b[9], c=fa_8_out_carry, sum=out[9], carry=fa_9_out_carry);
    FullAdder(a=a[10], b=b[10], c=fa_9_out_carry, sum=out[10], carry=fa_10_out_carry);
    FullAdder(a=a[11], b=b[11], c=fa_10_out_carry, sum=out[11], carry=fa_11_out_carry);
    FullAdder(a=a[12], b=b[12], c=fa_11_out_carry, sum=out[12], carry=fa_12_out_carry);
    FullAdder(a=a[13], b=b[13], c=fa_12_out_carry, sum=out[13], carry=fa_13_out_carry);
    FullAdder(a=a[14], b=b[14], c=fa_13_out_carry, sum=out[14], carry=fa_14_out_carry);
    FullAdder(a=a[15], b=b[15], c=fa_14_out_carry, sum=out[15], carry=fa_15_out_carry);
}
```

```
|        a         |        b         |       out        |
| 0000000000000000 | 0000000000000000 | 0000000000000000 |
| 0000000000000000 | 1111111111111111 | 1111111111111111 |
| 1111111111111111 | 1111111111111111 | 1111111111111110 |
| 1010101010101010 | 0101010101010101 | 1111111111111111 |
| 0011110011000011 | 0000111111110000 | 0100110010110011 |
| 0001001000110100 | 1001100001110110 | 1010101010101010 |
```
