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
gate out of primitive gates, as we already have an Or gate we can just apply it to each array pair. The same can be done with
and plus not.

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

**Mux16**: Mux 16 allows for a multiplex over 16 bit arrays using a single selector bit.

```
CHIP Mux16 {
    IN a[16], b[16], sel;
    OUT out[16];

    PARTS:
        Mux(a=a[0] , b=b[0] , sel=sel, out=out[0] );
        Mux(a=a[1] , b=b[1] , sel=sel, out=out[1] );
        Mux(a=a[2] , b=b[2] , sel=sel, out=out[2] );
        Mux(a=a[3] , b=b[3] , sel=sel, out=out[3] );
        Mux(a=a[4] , b=b[4] , sel=sel, out=out[4] );
        Mux(a=a[5] , b=b[5] , sel=sel, out=out[5] );
        Mux(a=a[6] , b=b[6] , sel=sel, out=out[6] );
        Mux(a=a[7] , b=b[7] , sel=sel, out=out[7] );
        Mux(a=a[8] , b=b[8] , sel=sel, out=out[8] );
        Mux(a=a[9] , b=b[9] , sel=sel, out=out[9] );
        Mux(a=a[10] , b=b[10] , sel=sel, out=out[10] );
        Mux(a=a[11] , b=b[11] , sel=sel, out=out[11] );
        Mux(a=a[12] , b=b[12] , sel=sel, out=out[12] );
        Mux(a=a[13] , b=b[13] , sel=sel, out=out[13] );
        Mux(a=a[14] , b=b[14] , sel=sel, out=out[14] );
        Mux(a=a[15] , b=b[15] , sel=sel, out=out[15] );
}
```

```
|        a         |        b         |sel|       out        |
| 0000000000000000 | 0000000000000000 | 0 | 0000000000000000 |
| 0000000000000000 | 0000000000000000 | 1 | 0000000000000000 |
| 0000000000000000 | 0001001000110100 | 0 | 0000000000000000 |
| 0000000000000000 | 0001001000110100 | 1 | 0001001000110100 |
| 1001100001110110 | 0000000000000000 | 0 | 1001100001110110 |
| 1001100001110110 | 0000000000000000 | 1 | 0000000000000000 |
| 1010101010101010 | 0101010101010101 | 0 | 1010101010101010 |
| 1010101010101010 | 0101010101010101 | 1 | 0101010101010101 |
```


### Multi Way Gates

**Or8Way**: allows for an array of 8 inputs to be evaluated and if any input in the array is on that value. Is passed to out.

```
CHIP Or8Way {
    IN in[8];
    OUT out;

    PARTS:
    Or(a=in[0] , b=in[1] , out=out1 );
    Or(a=in[2] , b=in[3] , out=out2 );
    Or(a=out1 , b=out2 , out=outl1_1 );


    Or(a=in[4] , b=in[5] , out=out3 );
    Or(a=in[6] , b=in[7] , out=out4 );
    Or(a=out3 , b=out4 , out=outl1_2 );

    Or(a=outl1_1 , b=outl1_2 , out=out );
}
```

```
|     in     |out|
|  00000000  | 0 |
|  11111111  | 1 |
|  00010000  | 1 |
|  00000001  | 1 |
|  00100110  | 1 |
```

**Mux4Way16**: Takes in 4 arrays all 16 bits in length then uses two selector bits to determine the input to output

```
CHIP Mux4Way16 {
    IN a[16], b[16], c[16], d[16], sel[2];
    OUT out[16];
    
    PARTS:
        Not(in=sel[0] , out=notSelZero);
        Not(in=sel[1], out=notSelOne);

        // Is the input A?
        And(a=a[0] , b=notSelZero , out=threeWayAndA_0);
        And(a=notSelOne , b=threeWayAndA_0 , out=threeWayAndAOut_0 );

        And(a=a[1], b=notSelZero, out=threeWayAndA_1);
        And(a=notSelOne, b=threeWayAndA_1, out=threeWayAndAOut_1);

        And(a=a[2], b=notSelZero, out=threeWayAndA_2);
        And(a=notSelOne, b=threeWayAndA_2, out=threeWayAndAOut_2);

        And(a=a[3], b=notSelZero, out=threeWayAndA_3);
        And(a=notSelOne, b=threeWayAndA_3, out=threeWayAndAOut_3);

        And(a=a[4], b=notSelZero, out=threeWayAndA_4);
        And(a=notSelOne, b=threeWayAndA_4, out=threeWayAndAOut_4);

        And(a=a[5], b=notSelZero, out=threeWayAndA_5);
        And(a=notSelOne, b=threeWayAndA_5, out=threeWayAndAOut_5);

        And(a=a[6], b=notSelZero, out=threeWayAndAZero_6);
        And(a=notSelOne, b=threeWayAndAZero_6, out=threeWayAndAOut_6);

        And(a=a[7], b=notSelZero, out=threeWayAndA_7);
        And(a=notSelOne, b=threeWayAndA_7, out=threeWayAndAOut_7);

        And(a=a[8], b=notSelZero, out=threeWayAndA_8);
        And(a=notSelOne, b=threeWayAndA_8, out=threeWayAndAOut_8);

        And(a=a[9], b=notSelZero, out=threeWayAndA_9);
        And(a=notSelOne, b=threeWayAndA_9, out=threeWayAndAOut_9);

        And(a=a[10], b=notSelZero, out=threeWayAndA_10);
        And(a=notSelOne, b=threeWayAndA_10, out=threeWayAndAOut_10);

        And(a=a[11], b=notSelZero, out=threeWayAndA_11);
        And(a=notSelOne, b=threeWayAndA_11, out=threeWayAndAOut_11);

        And(a=a[12], b=notSelZero, out=threeWayAndA_12);
        And(a=notSelOne, b=threeWayAndA_12, out=threeWayAndAOut_12);

        And(a=a[13], b=notSelZero, out=threeWayAndA_13);
        And(a=notSelOne, b=threeWayAndA_13, out=threeWayAndAOut_13);

        And(a=a[14], b=notSelZero, out=threeWayAndA_14);
        And(a=notSelOne, b=threeWayAndA_14, out=threeWayAndAOut_14);

        And(a=a[15], b=notSelZero, out=threeWayAndA_15);
        And(a=notSelOne, b=threeWayAndA_15, out=threeWayAndAOut_15);

        // Is the input B ??
        And(a=b[0], b=notSelOne, out=threeWayAndB_0);
        And(a=sel[0], b=threeWayAndB_0, out=threeWayAndBOut_0);

        And(a=b[1], b=notSelOne, out=threeWayAndB_1);
        And(a=sel[0], b=threeWayAndB_1, out=threeWayAndBOut_1);

        And(a=b[2], b=notSelOne, out=threeWayAndB_2);
        And(a=sel[0], b=threeWayAndB_2, out=threeWayAndBOut_2);

        And(a=b[3], b=notSelOne, out=threeWayAndB_3);
        And(a=sel[0], b=threeWayAndB_3, out=threeWayAndBOut_3);

        And(a=b[4], b=notSelOne, out=threeWayAndB_4);
        And(a=sel[0], b=threeWayAndB_4, out=threeWayAndBOut_4);

        And(a=b[5], b=notSelOne, out=threeWayAndB_5);
        And(a=sel[0], b=threeWayAndB_5, out=threeWayAndBOut_5);

        And(a=b[6], b=notSelOne, out=threeWayAndB_6);
        And(a=sel[0], b=threeWayAndB_6, out=threeWayAndBOut_6);

        And(a=b[7], b=notSelOne, out=threeWayAndB_7);
        And(a=sel[0], b=threeWayAndB_7, out=threeWayAndBOut_7);

        And(a=b[8], b=notSelOne, out=threeWayAndB_8);
        And(a=sel[0], b=threeWayAndB_8, out=threeWayAndBOut_8);

        And(a=b[9], b=notSelOne, out=threeWayAndB_9);
        And(a=sel[0], b=threeWayAndB_9, out=threeWayAndBOut_9);

        And(a=b[10], b=notSelOne, out=threeWayAndB_10);
        And(a=sel[0], b=threeWayAndB_10, out=threeWayAndBOut_10);

        And(a=b[11], b=notSelOne, out=threeWayAndB_11);
        And(a=sel[0], b=threeWayAndB_11, out=threeWayAndBOut_11);

        And(a=b[12], b=notSelOne, out=threeWayAndB_12);
        And(a=sel[0], b=threeWayAndB_12, out=threeWayAndBOut_12);

        And(a=b[13], b=notSelOne, out=threeWayAndB_13);
        And(a=sel[0], b=threeWayAndB_13, out=threeWayAndBOut_13);

        And(a=b[14], b=notSelOne, out=threeWayAndB_14);
        And(a=sel[0], b=threeWayAndB_14, out=threeWayAndBOut_14);

        And(a=b[15], b=notSelOne, out=threeWayAndB_15);
        And(a=sel[0], b=threeWayAndB_15, out=threeWayAndBOut_15);

        // Is the selector C??
        And(a=c[0], b=notSelZero, out=threeWayAndC_0);
        And(a=sel[1], b=threeWayAndC_0, out=threeWayAndCOut_0);

        And(a=c[1], b=notSelZero, out=threeWayAndC_1);
        And(a=sel[1], b=threeWayAndC_1, out=threeWayAndCOut_1);

        And(a=c[2], b=notSelZero, out=threeWayAndC_2);
        And(a=sel[1], b=threeWayAndC_2, out=threeWayAndCOut_2);

        And(a=c[3], b=notSelZero, out=threeWayAndC_3);
        And(a=sel[1], b=threeWayAndC_3, out=threeWayAndCOut_3);

        And(a=c[4], b=notSelZero, out=threeWayAndC_4);
        And(a=sel[1], b=threeWayAndC_4, out=threeWayAndCOut_4);

        And(a=c[5], b=notSelZero, out=threeWayAndC_5);
        And(a=sel[1], b=threeWayAndC_5, out=threeWayAndCOut_5);

        And(a=c[6], b=notSelZero, out=threeWayAndC_6);
        And(a=sel[1], b=threeWayAndC_6, out=threeWayAndCOut_6);

        And(a=c[7], b=notSelZero, out=threeWayAndC_7);
        And(a=sel[1], b=threeWayAndC_7, out=threeWayAndCOut_7);

        And(a=c[8], b=notSelZero, out=threeWayAndC_8);
        And(a=sel[1], b=threeWayAndC_8, out=threeWayAndCOut_8);

        And(a=c[9], b=notSelZero, out=threeWayAndC_9);
        And(a=sel[1], b=threeWayAndC_9, out=threeWayAndCOut_9);

        And(a=c[10], b=notSelZero, out=threeWayAndC_10);
        And(a=sel[1], b=threeWayAndC_10, out=threeWayAndCOut_10);

        And(a=c[11], b=notSelZero, out=threeWayAndC_11);
        And(a=sel[1], b=threeWayAndC_11, out=threeWayAndCOut_11);

        And(a=c[12], b=notSelZero, out=threeWayAndC_12);
        And(a=sel[1], b=threeWayAndC_12, out=threeWayAndCOut_12);

        And(a=c[13], b=notSelZero, out=threeWayAndC_13);
        And(a=sel[1], b=threeWayAndC_13, out=threeWayAndCOut_13);

        And(a=c[14], b=notSelZero, out=threeWayAndC_14);
        And(a=sel[1], b=threeWayAndC_14, out=threeWayAndCOut_14);

        And(a=c[15], b=notSelZero, out=threeWayAndC_15);
        And(a=sel[1], b=threeWayAndC_15, out=threeWayAndCOut_15);

        // Is the input D
        And(a=sel[0], b=sel[1], out=threeWayAndD_0);
        And(a=d[0], b=threeWayAndD_0, out=threeWayAndDOut_0);

        And(a=sel[0], b=sel[1], out=threeWayAndD_1);
        And(a=d[1], b=threeWayAndD_1, out=threeWayAndDOut_1);

        And(a=sel[0], b=sel[1], out=threeWayAndD_2);
        And(a=d[2], b=threeWayAndD_2, out=threeWayAndDOut_2);

        And(a=sel[0], b=sel[1], out=threeWayAndD_3);
        And(a=d[3], b=threeWayAndD_3, out=threeWayAndDOut_3);

        And(a=sel[0], b=sel[1], out=threeWayAndD_4);
        And(a=d[4], b=threeWayAndD_4, out=threeWayAndDOut_4);

        And(a=sel[0], b=sel[1], out=threeWayAndD_5);
        And(a=d[5], b=threeWayAndD_5, out=threeWayAndDOut_5);

        And(a=sel[0], b=sel[1], out=threeWayAndD_6);
        And(a=d[6], b=threeWayAndD_6, out=threeWayAndDOut_6);

        And(a=sel[0], b=sel[1], out=threeWayAndD_7);
        And(a=d[7], b=threeWayAndD_7, out=threeWayAndDOut_7);

        And(a=sel[0], b=sel[1], out=threeWayAndD_8);
        And(a=d[8], b=threeWayAndD_8, out=threeWayAndDOut_8);

        And(a=sel[0], b=sel[1], out=threeWayAndD_9);
        And(a=d[9], b=threeWayAndD_9, out=threeWayAndDOut_9);

        And(a=sel[0], b=sel[1], out=threeWayAndD_10);
        And(a=d[10], b=threeWayAndD_10, out=threeWayAndDOut_10);

        And(a=sel[0], b=sel[1], out=threeWayAndD_11);
        And(a=d[11], b=threeWayAndD_11, out=threeWayAndDOut_11);

        And(a=sel[0], b=sel[1], out=threeWayAndD_12);
        And(a=d[12], b=threeWayAndD_12, out=threeWayAndDOut_12);

        And(a=sel[0], b=sel[1], out=threeWayAndD_13);
        And(a=d[13], b=threeWayAndD_13, out=threeWayAndDOut_13);

        And(a=sel[0], b=sel[1], out=threeWayAndD_14);
        And(a=d[14], b=threeWayAndD_14, out=threeWayAndDOut_14);

        And(a=sel[0], b=sel[1], out=threeWayAndD_15);
        And(a=d[15], b=threeWayAndD_15, out=threeWayAndDOut_15);


        Or(a=threeWayAndAOut_0, b=threeWayAndBOut_0, out=orAB_0);
        Or(a=threeWayAndCOut_0, b=threeWayAndDOut_0, out=orCD_0);
        Or(a=orAB_0, b=orCD_0, out=out[0]);

        Or(a=threeWayAndAOut_1, b=threeWayAndBOut_1, out=orAB_1);
        Or(a=threeWayAndCOut_1, b=threeWayAndDOut_1, out=orCD_1);
        Or(a=orAB_1, b=orCD_1, out=out[1]);

        Or(a=threeWayAndAOut_2, b=threeWayAndBOut_2, out=orAB_2);
        Or(a=threeWayAndCOut_2, b=threeWayAndDOut_2, out=orCD_2);
        Or(a=orAB_2, b=orCD_2, out=out[2]);

        Or(a=threeWayAndAOut_3, b=threeWayAndBOut_3, out=orAB_3);
        Or(a=threeWayAndCOut_3, b=threeWayAndDOut_3, out=orCD_3);
        Or(a=orAB_3, b=orCD_3, out=out[3]);

        Or(a=threeWayAndAOut_4, b=threeWayAndBOut_4, out=orAB_4);
        Or(a=threeWayAndCOut_4, b=threeWayAndDOut_4, out=orCD_4);
        Or(a=orAB_4, b=orCD_4, out=out[4]);

        Or(a=threeWayAndAOut_5, b=threeWayAndBOut_5, out=orAB_5);
        Or(a=threeWayAndCOut_5, b=threeWayAndDOut_5, out=orCD_5);
        Or(a=orAB_5, b=orCD_5, out=out[5]);

        Or(a=threeWayAndAOut_6, b=threeWayAndBOut_6, out=orAB_6);
        Or(a=threeWayAndCOut_6, b=threeWayAndDOut_6, out=orCD_6);
        Or(a=orAB_6, b=orCD_6, out=out[6]);

        Or(a=threeWayAndAOut_7, b=threeWayAndBOut_7, out=orAB_7);
        Or(a=threeWayAndCOut_7, b=threeWayAndDOut_7, out=orCD_7);
        Or(a=orAB_7, b=orCD_7, out=out[7]);

        Or(a=threeWayAndAOut_8, b=threeWayAndBOut_8, out=orAB_8);
        Or(a=threeWayAndCOut_8, b=threeWayAndDOut_8, out=orCD_8);
        Or(a=orAB_8, b=orCD_8, out=out[8]);

        Or(a=threeWayAndAOut_9, b=threeWayAndBOut_9, out=orAB_9);
        Or(a=threeWayAndCOut_9, b=threeWayAndDOut_9, out=orCD_9);
        Or(a=orAB_9, b=orCD_9, out=out[9]);

        Or(a=threeWayAndAOut_10, b=threeWayAndBOut_10, out=orAB_10);
        Or(a=threeWayAndCOut_10, b=threeWayAndDOut_10, out=orCD_10);
        Or(a=orAB_10, b=orCD_10, out=out[10]);

        Or(a=threeWayAndAOut_11, b=threeWayAndBOut_11, out=orAB_11);
        Or(a=threeWayAndCOut_11, b=threeWayAndDOut_11, out=orCD_11);
        Or(a=orAB_11, b=orCD_11, out=out[11]);

        Or(a=threeWayAndAOut_12, b=threeWayAndBOut_12, out=orAB_12);
        Or(a=threeWayAndCOut_12, b=threeWayAndDOut_12, out=orCD_12);
        Or(a=orAB_12, b=orCD_12, out=out[12]);

        Or(a=threeWayAndAOut_13, b=threeWayAndBOut_13, out=orAB_13);
        Or(a=threeWayAndCOut_13, b=threeWayAndDOut_13, out=orCD_13);
        Or(a=orAB_13, b=orCD_13, out=out[13]);

        Or(a=threeWayAndAOut_14, b=threeWayAndBOut_14, out=orAB_14);
        Or(a=threeWayAndCOut_14, b=threeWayAndDOut_14, out=orCD_14);
        Or(a=orAB_14, b=orCD_14, out=out[14]);

        Or(a=threeWayAndAOut_15, b=threeWayAndBOut_15, out=orAB_15);
        Or(a=threeWayAndCOut_15, b=threeWayAndDOut_15, out=orCD_15);
        Or(a=orAB_15, b=orCD_15, out=out[15]);
}
```

```
|        a         |        b         |        c         |        d         | sel  |       out        |
| 0000000000000000 | 0000000000000000 | 0000000000000000 | 0000000000000000 |  00  | 0000000000000000 |
| 0000000000000000 | 0000000000000000 | 0000000000000000 | 0000000000000000 |  01  | 0000000000000000 |
| 0000000000000000 | 0000000000000000 | 0000000000000000 | 0000000000000000 |  10  | 0000000000000000 |
| 0000000000000000 | 0000000000000000 | 0000000000000000 | 0000000000000000 |  11  | 0000000000000000 |
| 0001001000110100 | 1001100001110110 | 1010101010101010 | 0101010101010101 |  00  | 0001001000110100 |
| 0001001000110100 | 1001100001110110 | 1010101010101010 | 0101010101010101 |  01  | 1001100001110110 |
| 0001001000110100 | 1001100001110110 | 1010101010101010 | 0101010101010101 |  10  | 1010101010101010 |
| 0001001000110100 | 1001100001110110 | 1010101010101010 | 0101010101010101 |  11  | 0101010101010101 |
```
