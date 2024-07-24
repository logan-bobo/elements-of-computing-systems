This file contains HLD snippets to implement all of the logic gates defined in chapter one. 
All of the examples will contain a description, the definition of the chip in HDL and a truth table. 


**Nand**: A primitive gate we do not need to implement and is already builtin.
```
| a | b |out|
| 0 | 0 | 1 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |
```

**Not**: Can be implemented with a single `nand` gate. This gate inverts the signal from 0 to 1 and from 1 to 0.

```
CHIP Not {
    IN in;
    OUT out;

    PARTS:
        Nand(a=in , b=in, out=out);
}
```

```
|in |out|
| 0 | 1 |
| 1 | 0 |
```

**And**: This gate can be produced from a combination of `nand` and `not`. Notice the truth table is just inverted `nand`

```
CHIP And {
    IN a, b;
    OUT out;
    
    PARTS:
        Nand(a=a, b=b, out=nandOut);
        Not(in=nandOut, out=out);
}
```

```
| a | b |out|
| 0 | 0 | 0 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |
```

**Or**: Can be defined using `and` along with `not`

```
CHIP Or {
    IN a, b;
    OUT out;

    PARTS:
        Not(in=a , out=outa);
        Not(in=b , out=outb);
        Not(in=outand , out=out);
        And(a=outa , b=outb , out=outand);
}
```

```
| a | b |out|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 1 |
```

**Xor**: Also know as exclusive `or` can be built from `and`, `not` and `or`

```
CHIP Xor {
    IN a, b;
    OUT out;

    PARTS:
        Not(in=a , out=nota);
        Not(in=b , out=notb);
        And(a=a , b=notb , out=w1);
        And(a=nota , b=b , out=w2);
        Or(a=w1 , b=w2 , out=out);
}
```

```
| a | b |out|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |
```

**Multiplexer**: Can be built from the above gates. The multiplexer uses a selector bit to chose its output. 

```
CHIP Mux {
    IN a, b, sel;
    OUT out;

    PARTS:
        Not(in=sel , out=selOff );
        And(a=a, b=selOff, out=andSelOutA);
        And(a=b , b=sel , out=andSelOutB);
        Or(a=andSelOutA , b=andSelOutB , out=out );
}
```

```
| a | b |sel|out|
| 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 0 |
| 0 | 1 | 0 | 0 |
| 0 | 1 | 1 | 1 |
| 1 | 0 | 0 | 1 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | 0 | 1 |
| 1 | 1 | 1 | 1 |
```

**Demultiplexer**: Can be built from the same primitive components as the `multiplexer` however performs the opisite
function. Taking in a single and selector bit and outputting acording to the selector bit.

```
CHIP DMux {
    IN in, sel;
    OUT a, b;

    PARTS:
        Not(in=sel , out=selOut );
        And(a=in, b=selOut, out=a);
        And(a=in, b=sel, out=b); 
}
```

```
|in |sel| a | b |
| 0 | 0 | 0 | 0 |
| 0 | 1 | 0 | 0 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | 0 | 1 |
```

### Multi Bit Chips

**not16**: The not16 is as simple as it sounds taking 16 inputs and reversing the signal of each input
```
CHIP Not16 {
    IN in[16];
    OUT out[16];

    PARTS:
        Nand(a=in[0] , b=in[0] , out=out[0] );
        Nand(a=in[1] , b=in[1] , out=out[1] );
        Nand(a=in[2] , b=in[2] , out=out[2] );
        Nand(a=in[3] , b=in[3] , out=out[3] );
        Nand(a=in[4] , b=in[4] , out=out[4] );
        Nand(a=in[5] , b=in[5] , out=out[5] );
        Nand(a=in[6] , b=in[6] , out=out[6] );
        Nand(a=in[7] , b=in[7] , out=out[7] );    
        Nand(a=in[8] , b=in[8] , out=out[8] );
        Nand(a=in[9] , b=in[9] , out=out[9] );
        Nand(a=in[10] , b=in[10] , out=out[10] );
        Nand(a=in[11] , b=in[11] , out=out[11] );    
        Nand(a=in[12] , b=in[12] , out=out[12] );
        Nand(a=in[13] , b=in[13] , out=out[13] );
        Nand(a=in[14] , b=in[14] , out=out[14] );
        Nand(a=in[15] , b=in[15] , out=out[15] );    
}
```

```
|        in        |       out        |
| 0000000000000000 | 1111111111111111 |
| 1111111111111111 | 0000000000000000 |
| 1010101010101010 | 0101010101010101 |
| 0011110011000011 | 1100001100111100 |
| 0001001000110100 | 1110110111001011 |
```

**And16**: Multi Bit and is just an expansion of and over input arrays of 16 bits
```
CHIP And16 {
    IN a[16], b[16];
    OUT out[16];

    PARTS:
        Nand(a=a[0] , b=b[0] , out=not0 );
        Not(in=not0 , out=out[0]);
        Nand(a=a[1] , b=b[1] , out=not1 );
        Not(in=not1 , out=out[1]);
        Nand(a=a[2] , b=b[2] , out=not2 );
        Not(in=not2 , out=out[2]);
        Nand(a=a[3] , b=b[3] , out=not3 );
        Not(in=not3 , out=out[3]);
        Nand(a=a[4] , b=b[4] , out=not4 );
        Not(in=not4 , out=out[4]);
        Nand(a=a[5] , b=b[5] , out=not5 );
        Not(in=not5 , out=out[5]);
        Nand(a=a[6] , b=b[6] , out=not6 );
        Not(in=not6 , out=out[6]);
        Nand(a=a[7] , b=b[7] , out=not7 );    
        Not(in=not7 , out=out[7]);
        Nand(a=a[8] , b=b[8] , out=not8 );
        Not(in=not8 , out=out[8]);
        Nand(a=a[9] , b=b[9] , out=not9 );
        Not(in=not9 , out=out[9]);
        Nand(a=a[10] , b=b[10] , out=not10 );
        Not(in=not10 , out=out[10]);
        Nand(a=a[11] , b=b[11] , out=not11 );    
        Not(in=not11 , out=out[11]);
        Nand(a=a[12] , b=b[12] , out=not12 );
        Not(in=not12 , out=out[12]);
        Nand(a=a[13] , b=b[13] , out=not13 );
        Not(in=not13 , out=out[13]);
        Nand(a=a[14] , b=b[14] , out=not14 );
        Not(in=not14 , out=out[14]);
        Nand(a=a[15] , b=b[15] , out=not15 );    
        Not(in=not15 , out=out[15]);
}
```

```
|        a         |        b         |       out        |
| 0000000000000000 | 0000000000000000 | 0000000000000000 |
| 0000000000000000 | 1111111111111111 | 0000000000000000 |
| 1111111111111111 | 1111111111111111 | 1111111111111111 |
| 1010101010101010 | 0101010101010101 | 0000000000000000 |
| 0011110011000011 | 0000111111110000 | 0000110011000000 |
| 0001001000110100 | 1001100001110110 | 0001000000110100 |
```

**or16**: Multi bit Or across input arrays of 16 bits. Here I had a moment where I through we do not need to build the
gate out of primitive gates, as we already have an Or gate we can just apply iut to each array pair. The same can be done with
and pluss not.

```
CHIP Or16 {
    IN a[16], b[16];
    OUT out[16];

    PARTS:
        Or(a=a[0] , b=b[0] , out=out[0]);
        Or(a=a[1] , b=b[1] , out=out[1]);
        Or(a=a[2] , b=b[2] , out=out[2]);
        Or(a=a[3] , b=b[3] , out=out[3]);
        Or(a=a[4] , b=b[4] , out=out[4]);
        Or(a=a[5] , b=b[5] , out=out[5]);    
        Or(a=a[6] , b=b[6] , out=out[6]);
        Or(a=a[7] , b=b[7] , out=out[7]);
        Or(a=a[8] , b=b[8] , out=out[8]);
        Or(a=a[9] , b=b[9] , out=out[9]);
        Or(a=a[10] , b=b[10] , out=out[10]);
        Or(a=a[11] , b=b[11] , out=out[11]);
        Or(a=a[12] , b=b[12] , out=out[12]);
        Or(a=a[13] , b=b[13] , out=out[13]);
        Or(a=a[14] , b=b[14] , out=out[14]);
        Or(a=a[15] , b=b[15] , out=out[15]);
}
```

```
|        a         |        b         |       out        |
| 0000000000000000 | 0000000000000000 | 0000000000000000 |
| 0000000000000000 | 1111111111111111 | 1111111111111111 |
| 1111111111111111 | 1111111111111111 | 1111111111111111 |
| 1010101010101010 | 0101010101010101 | 1111111111111111 |
| 0011110011000011 | 0000111111110000 | 0011111111110011 |
| 0001001000110100 | 1001100001110110 | 1001101001110110 |
```
