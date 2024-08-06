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
