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

**mux8way16**: This chip looks complicated but its not once you have solved a multi way mux its about scaling the
solution with little added. This was a real ah ha moment for me. If I were to do this again I would create
abstract chips cush as and3way and or8way

```
CHIP Mux8Way16 {
    IN a[16], b[16], c[16], d[16],
       e[16], f[16], g[16], h[16],
       sel[3];
    OUT out[16];

    PARTS:

    Not(in=sel[0] , out=notSelZero );
    Not(in=sel[1] , out=notSelOne );
    Not(in=sel[2] , out=notSelTwo );

    // is it A? 000
    And(a=notSelZero , b=notSelOne , out=fourWayAndAout_0_1 );
    And(a=notSelTwo , b=fourWayAndAout_0_1 , out=fourWayAndAout_0_2 );
    And(a=a[0] , b=fourWayAndAout_0_2 , out=A_result_0 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndAout_1_1 );
    And(a=notSelTwo , b=fourWayAndAout_1_1 , out=fourWayAndAout_1_2 );
    And(a=a[1] , b=fourWayAndAout_1_2 , out=A_result_1 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndAout_2_1 );
    And(a=notSelTwo , b=fourWayAndAout_2_1 , out=fourWayAndAout_2_2 );
    And(a=a[2] , b=fourWayAndAout_2_2 , out=A_result_2 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndAout_3_1 );
    And(a=notSelTwo , b=fourWayAndAout_3_1 , out=fourWayAndAout_3_2 );
    And(a=a[3] , b=fourWayAndAout_3_2 , out=A_result_3 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndAout_4_1 );
    And(a=notSelTwo , b=fourWayAndAout_4_1 , out=fourWayAndAout_4_2 );
    And(a=a[4] , b=fourWayAndAout_4_2 , out=A_result_4 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndAout_5_1 );
    And(a=notSelTwo , b=fourWayAndAout_5_1 , out=fourWayAndAout_5_2 );
    And(a=a[5] , b=fourWayAndAout_5_2 , out=A_result_5 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndAout_6_1 );
    And(a=notSelTwo , b=fourWayAndAout_6_1 , out=fourWayAndAout_6_2 );
    And(a=a[6] , b=fourWayAndAout_6_2 , out=A_result_6 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndAout_7_1 );
    And(a=notSelTwo , b=fourWayAndAout_7_1 , out=fourWayAndAout_7_2 );
    And(a=a[7] , b=fourWayAndAout_7_2 , out=A_result_7 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndAout_8_1 );
    And(a=notSelTwo , b=fourWayAndAout_8_1 , out=fourWayAndAout_8_2 );
    And(a=a[8] , b=fourWayAndAout_8_2 , out=A_result_8 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndAout_9_1 );
    And(a=notSelTwo , b=fourWayAndAout_9_1 , out=fourWayAndAout_9_2 );
    And(a=a[9] , b=fourWayAndAout_9_2 , out=A_result_9 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndAout_10_1 );
    And(a=notSelTwo , b=fourWayAndAout_10_1 , out=fourWayAndAout_10_2 );
    And(a=a[10] , b=fourWayAndAout_10_2 , out=A_result_10 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndAout_11_1 );
    And(a=notSelTwo , b=fourWayAndAout_11_1 , out=fourWayAndAout_11_2 );
    And(a=a[11] , b=fourWayAndAout_11_2 , out=A_result_11 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndAout_12_1 );
    And(a=notSelTwo , b=fourWayAndAout_12_1 , out=fourWayAndAout_12_2 );
    And(a=a[12] , b=fourWayAndAout_12_2 , out=A_result_12 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndAout_13_1 );
    And(a=notSelTwo , b=fourWayAndAout_13_1 , out=fourWayAndAout_13_2 );
    And(a=a[13] , b=fourWayAndAout_13_2 , out=A_result_13 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndAout_14_1 );
    And(a=notSelTwo , b=fourWayAndAout_14_1 , out=fourWayAndAout_14_2 );
    And(a=a[14] , b=fourWayAndAout_14_2 , out=A_result_14 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndAout_15_1 );
    And(a=notSelTwo , b=fourWayAndAout_15_1 , out=fourWayAndAout_15_2 );
    And(a=a[14] , b=fourWayAndAout_15_2 , out=A_result_15 );

    // is it B? 001 
    And(a=sel[0] , b=notSelOne , out=fourWayAndBout_0_1 );
    And(a=notSelTwo , b=fourWayAndBout_0_1 , out=fourWayAndBout_0_2 );
    And(a=fourWayAndBout_0_2 , b=b[0] , out=B_result_0 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndBout_1_1 );
    And(a=notSelTwo , b=fourWayAndBout_1_1 , out=fourWayAndBout_1_2 );
    And(a=fourWayAndBout_1_2 , b=b[1] , out=B_result_1 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndBout_2_1 );
    And(a=notSelTwo , b=fourWayAndBout_2_1 , out=fourWayAndBout_2_2 );
    And(a=fourWayAndBout_2_2 , b=b[2] , out=B_result_2 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndBout_3_1 );
    And(a=notSelTwo , b=fourWayAndBout_3_1 , out=fourWayAndBout_3_2 );
    And(a=fourWayAndBout_3_2 , b=b[3] , out=B_result_3 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndBout_4_1 );
    And(a=notSelTwo , b=fourWayAndBout_4_1 , out=fourWayAndBout_4_2 );
    And(a=fourWayAndBout_4_2 , b=b[4] , out=B_result_4 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndBout_5_1 );
    And(a=notSelTwo , b=fourWayAndBout_5_1 , out=fourWayAndBout_5_2 );
    And(a=fourWayAndBout_5_2 , b=b[5] , out=B_result_5 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndBout_6_1 );
    And(a=notSelTwo , b=fourWayAndBout_6_1 , out=fourWayAndBout_6_2 );
    And(a=fourWayAndBout_6_2 , b=b[6] , out=B_result_6 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndBout_7_1 );
    And(a=notSelTwo , b=fourWayAndBout_7_1 , out=fourWayAndBout_7_2 );
    And(a=fourWayAndBout_7_2 , b=b[7] , out=B_result_7 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndBout_8_1 );
    And(a=notSelTwo , b=fourWayAndBout_8_1 , out=fourWayAndBout_8_2 );
    And(a=fourWayAndBout_8_2 , b=b[8] , out=B_result_8 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndBout_9_1 );
    And(a=notSelTwo , b=fourWayAndBout_9_1 , out=fourWayAndBout_9_2 );
    And(a=fourWayAndBout_9_2 , b=b[9] , out=B_result_9 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndBout_10_1 );
    And(a=notSelTwo , b=fourWayAndBout_10_1 , out=fourWayAndBout_10_2 );
    And(a=fourWayAndBout_10_2 , b=b[10] , out=B_result_10 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndBout_11_1 );
    And(a=notSelTwo , b=fourWayAndBout_11_1 , out=fourWayAndBout_11_2 );
    And(a=fourWayAndBout_11_2 , b=b[11] , out=B_result_11 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndBout_12_1 );
    And(a=notSelTwo , b=fourWayAndBout_12_1 , out=fourWayAndBout_12_2 );
    And(a=fourWayAndBout_12_2 , b=b[12] , out=B_result_12);

    And(a=sel[0] , b=notSelOne , out=fourWayAndBout_13_1 );
    And(a=notSelTwo , b=fourWayAndBout_13_1 , out=fourWayAndBout_13_2 );
    And(a=fourWayAndBout_13_2 , b=b[13] , out=B_result_13 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndBout_14_1 );
    And(a=notSelTwo , b=fourWayAndBout_14_1 , out=fourWayAndBout_14_2 );
    And(a=fourWayAndBout_14_2 , b=b[14] , out=B_result_14);

    And(a=sel[0] , b=notSelOne , out=fourWayAndBout_15_1 );
    And(a=notSelTwo , b=fourWayAndBout_15_1 , out=fourWayAndBout_15_2 );
    And(a=fourWayAndBout_15_2 , b=b[15] , out=B_result_15);

    // if it C?
    // 010
    And(a=notSelZero , b=sel[1] , out=fourWayAndCout_0_1 );
    And(a=notSelTwo , b=fourWayAndCout_0_1 , out=fourWayAndCout_0_2 );
    And(a=fourWayAndCout_0_2 , b=c[0] , out=C_result_0 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndCout_1_1 );
    And(a=notSelTwo , b=fourWayAndCout_1_1 , out=fourWayAndCout_1_2 );
    And(a=fourWayAndCout_1_2 , b=c[1] , out=C_result_1 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndCout_2_1 );
    And(a=notSelTwo , b=fourWayAndCout_2_1 , out=fourWayAndCout_2_2 );
    And(a=fourWayAndCout_2_2 , b=c[2] , out=C_result_2 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndCout_3_1 );
    And(a=notSelTwo , b=fourWayAndCout_3_1 , out=fourWayAndCout_3_2 );
    And(a=fourWayAndCout_3_2 , b=c[3] , out=C_result_3 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndCout_4_1 );
    And(a=notSelTwo , b=fourWayAndCout_4_1 , out=fourWayAndCout_4_2 );
    And(a=fourWayAndCout_4_2 , b=c[4] , out=C_result_4 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndCout_5_1 );
    And(a=notSelTwo , b=fourWayAndCout_5_1 , out=fourWayAndCout_5_2 );
    And(a=fourWayAndCout_5_2 , b=c[5] , out=C_result_5 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndCout_6_1 );
    And(a=notSelTwo , b=fourWayAndCout_6_1 , out=fourWayAndCout_6_2 );
    And(a=fourWayAndCout_6_2 , b=c[6] , out=C_result_6 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndCout_7_1 );
    And(a=notSelTwo , b=fourWayAndCout_7_1 , out=fourWayAndCout_7_2 );
    And(a=fourWayAndCout_7_2 , b=c[7] , out=C_result_7 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndCout_8_1 );
    And(a=notSelTwo , b=fourWayAndCout_8_1 , out=fourWayAndCout_8_2 );
    And(a=fourWayAndCout_8_2 , b=c[8] , out=C_result_8 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndCout_9_1 );
    And(a=notSelTwo , b=fourWayAndCout_9_1 , out=fourWayAndCout_9_2 );
    And(a=fourWayAndCout_9_2 , b=c[9] , out=C_result_9 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndCout_10_1 );
    And(a=notSelTwo , b=fourWayAndCout_10_1 , out=fourWayAndCout_10_2 );
    And(a=fourWayAndCout_10_2 , b=c[10] , out=C_result_10 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndCout_11_1 );
    And(a=notSelTwo , b=fourWayAndCout_11_1 , out=fourWayAndCout_11_2 );
    And(a=fourWayAndCout_11_2 , b=c[11] , out=C_result_11 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndCout_12_1 );
    And(a=notSelTwo , b=fourWayAndCout_12_1 , out=fourWayAndCout_12_2 );
    And(a=fourWayAndCout_12_2 , b=c[12] , out=C_result_12 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndCout_13_1 );
    And(a=notSelTwo , b=fourWayAndCout_13_1 , out=fourWayAndCout_13_2 );
    And(a=fourWayAndCout_13_2 , b=c[13] , out=C_result_13 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndCout_14_1 );
    And(a=notSelTwo , b=fourWayAndCout_14_1 , out=fourWayAndCout_14_2 );
    And(a=fourWayAndCout_14_2 , b=c[14] , out=C_result_14 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndCout_15_1 );
    And(a=notSelTwo , b=fourWayAndCout_14_1 , out=fourWayAndCout_15_2 );
    And(a=fourWayAndCout_15_2 , b=c[14] , out=C_result_15 );

    // is it D?
    // 001
    And(a=sel[0] , b=sel[1] , out=fourWayAndDout_0_1 );
    And(a=notSelTwo , b=fourWayAndDout_0_1 , out=fourWayAndDout_0_2 );
    And(a=fourWayAndDout_0_2 , b=d[0] , out=D_result_0 );

    And(a=sel[0] , b=sel[1] , out=fourWayAndDout_1_1 );
    And(a=notSelTwo , b=fourWayAndDout_1_1 , out=fourWayAndDout_1_2 );
    And(a=fourWayAndDout_1_2 , b=d[1] , out=D_result_1 );

    And(a=sel[0] , b=sel[1] , out=fourWayAndDout_2_1 );
    And(a=notSelTwo , b=fourWayAndDout_2_1 , out=fourWayAndDout_2_2 );
    And(a=fourWayAndDout_2_2 , b=d[2] , out=D_result_2 );

    And(a=sel[0] , b=sel[1] , out=fourWayAndDout_3_1 );
    And(a=notSelTwo , b=fourWayAndDout_3_1 , out=fourWayAndDout_3_2 );
    And(a=fourWayAndDout_3_2 , b=d[3] , out=D_result_3 );

    And(a=sel[0] , b=sel[1] , out=fourWayAndDout_4_1 );
    And(a=notSelTwo , b=fourWayAndDout_4_1 , out=fourWayAndDout_4_2 );
    And(a=fourWayAndDout_4_2 , b=d[4] , out=D_result_4 );

    And(a=sel[0] , b=sel[1] , out=fourWayAndDout_5_1 );
    And(a=notSelTwo , b=fourWayAndDout_5_1 , out=fourWayAndDout_5_2 );
    And(a=fourWayAndDout_5_2 , b=d[5] , out=D_result_5 );

    And(a=sel[0] , b=sel[1] , out=fourWayAndDout_6_1 );
    And(a=notSelTwo , b=fourWayAndDout_6_1 , out=fourWayAndDout_6_2 );
    And(a=fourWayAndDout_6_2 , b=d[6] , out=D_result_6 );

    And(a=sel[0] , b=sel[1] , out=fourWayAndDout_7_1 );
    And(a=notSelTwo , b=fourWayAndDout_7_1 , out=fourWayAndDout_7_2 );
    And(a=fourWayAndDout_7_2 , b=d[7] , out=D_result_7 );

    And(a=sel[0] , b=sel[1] , out=fourWayAndDout_8_1 );
    And(a=notSelTwo , b=fourWayAndDout_8_1 , out=fourWayAndDout_8_2 );
    And(a=fourWayAndDout_8_2 , b=d[8] , out=D_result_8 );

    And(a=sel[0] , b=sel[1] , out=fourWayAndDout_9_1 );
    And(a=notSelTwo , b=fourWayAndDout_9_1 , out=fourWayAndDout_9_2 );
    And(a=fourWayAndDout_9_2 , b=d[9] , out=D_result_9 );

    And(a=sel[0] , b=sel[1] , out=fourWayAndDout_10_1 );
    And(a=notSelTwo , b=fourWayAndDout_10_1 , out=fourWayAndDout_10_2 );
    And(a=fourWayAndDout_10_2 , b=d[10] , out=D_result_10 );

    And(a=sel[0] , b=sel[1] , out=fourWayAndDout_11_1 );
    And(a=notSelTwo , b=fourWayAndDout_11_1 , out=fourWayAndDout_11_2 );
    And(a=fourWayAndDout_11_2 , b=d[11] , out=D_result_11 );

    And(a=sel[0] , b=sel[1] , out=fourWayAndDout_12_1 );
    And(a=notSelTwo , b=fourWayAndDout_12_1 , out=fourWayAndDout_12_2 );
    And(a=fourWayAndDout_12_2 , b=d[12] , out=D_result_12 );

    And(a=sel[0] , b=sel[1] , out=fourWayAndDout_13_1 );
    And(a=notSelTwo , b=fourWayAndDout_13_1 , out=fourWayAndDout_13_2 );
    And(a=fourWayAndDout_13_2 , b=d[13] , out=D_result_13 );

    And(a=sel[0] , b=sel[1] , out=fourWayAndDout_14_1 );
    And(a=notSelTwo , b=fourWayAndDout_14_1 , out=fourWayAndDout_14_2 );
    And(a=fourWayAndDout_14_2 , b=d[14] , out=D_result_14);

    And(a=sel[0] , b=sel[1] , out=fourWayAndDout_15_1 );
    And(a=notSelTwo , b=fourWayAndDout_14_1 , out=fourWayAndDout_15_2 );
    And(a=fourWayAndDout_15_2 , b=d[15] , out=D_result_15 );

    // is it E?
    // 100
    And(a=notSelZero , b=notSelOne , out=fourWayAndEout_0_1 );
    And(a=sel[2] , b=fourWayAndEout_0_1 , out=fourWayAndEout_0_2 );
    And(a=fourWayAndEout_0_2 , b=e[0] , out=E_result_0 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndEout_1_1 );
    And(a=sel[2] , b=fourWayAndEout_1_1 , out=fourWayAndEout_1_2 );
    And(a=fourWayAndEout_1_2 , b=e[1] , out=E_result_1 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndEout_2_1 );
    And(a=sel[2] , b=fourWayAndEout_2_1 , out=fourWayAndEout_2_2 );
    And(a=fourWayAndEout_2_2 , b=e[2] , out=E_result_2 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndEout_3_1 );
    And(a=sel[2] , b=fourWayAndEout_3_1 , out=fourWayAndEout_3_2 );
    And(a=fourWayAndEout_3_2 , b=e[3] , out=E_result_3 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndEout_4_1 );
    And(a=sel[2] , b=fourWayAndEout_4_1 , out=fourWayAndEout_4_2 );
    And(a=fourWayAndEout_4_2 , b=e[4] , out=E_result_4 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndEout_5_1 );
    And(a=sel[2] , b=fourWayAndEout_5_1 , out=fourWayAndEout_5_2 );
    And(a=fourWayAndEout_5_2 , b=e[5] , out=E_result_5 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndEout_6_1 );
    And(a=sel[2] , b=fourWayAndEout_6_1 , out=fourWayAndEout_6_2 );
    And(a=fourWayAndEout_6_2 , b=e[6] , out=E_result_6 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndEout_7_1 );
    And(a=sel[2] , b=fourWayAndEout_7_1 , out=fourWayAndEout_7_2 );
    And(a=fourWayAndEout_7_2 , b=e[7] , out=E_result_7 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndEout_8_1 );
    And(a=sel[2] , b=fourWayAndEout_8_1 , out=fourWayAndEout_8_2 );
    And(a=fourWayAndEout_8_2 , b=e[8] , out=E_result_8 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndEout_9_1 );
    And(a=sel[2] , b=fourWayAndEout_9_1 , out=fourWayAndEout_9_2 );
    And(a=fourWayAndEout_9_2 , b=e[9] , out=E_result_9 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndEout_10_1 );
    And(a=sel[2] , b=fourWayAndEout_10_1 , out=fourWayAndEout_10_2 );
    And(a=fourWayAndEout_10_2 , b=e[10] , out=E_result_10 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndEout_11_1 );
    And(a=sel[2] , b=fourWayAndEout_11_1 , out=fourWayAndEout_11_2 );
    And(a=fourWayAndEout_11_2 , b=e[11] , out=E_result_11 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndEout_12_1 );
    And(a=sel[2] , b=fourWayAndEout_12_1 , out=fourWayAndEout_12_2 );
    And(a=fourWayAndEout_12_2 , b=e[12] , out=E_result_12 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndEout_13_1 );
    And(a=sel[2] , b=fourWayAndEout_13_1 , out=fourWayAndEout_13_2 );
    And(a=fourWayAndEout_13_2 , b=e[13] , out=E_result_13 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndEout_14_1 );
    And(a=sel[2] , b=fourWayAndEout_14_1 , out=fourWayAndEout_14_2 );
    And(a=fourWayAndEout_14_2 , b=e[14] , out=E_result_14 );

    And(a=notSelZero , b=notSelOne , out=fourWayAndEout_15_1 );
    And(a=sel[2] , b=fourWayAndEout_15_1 , out=fourWayAndEout_15_2 );
    And(a=fourWayAndEout_15_2 , b=e[15] , out=E_result_15 );
    
    // is it F?
    // 101
    And(a=sel[0] , b=notSelOne , out=fourWayAndFout_0_1 );
    And(a=sel[2] , b=fourWayAndFout_0_1 , out=fourWayAndFout_0_2 );
    And(a=fourWayAndFout_0_2 , b=f[0] , out=F_result_0 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndFout_1_1 );
    And(a=sel[2] , b=fourWayAndFout_1_1 , out=fourWayAndFout_1_2 );
    And(a=fourWayAndFout_1_2 , b=f[1] , out=F_result_1 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndFout_2_1 );
    And(a=sel[2] , b=fourWayAndFout_2_1 , out=fourWayAndFout_2_2 );
    And(a=fourWayAndFout_2_2 , b=f[2] , out=F_result_2 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndFout_3_1 );
    And(a=sel[2] , b=fourWayAndFout_3_1 , out=fourWayAndFout_3_2 );
    And(a=fourWayAndFout_3_2 , b=f[3] , out=F_result_3 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndFout_4_1 );
    And(a=sel[2] , b=fourWayAndFout_4_1 , out=fourWayAndFout_4_2 );
    And(a=fourWayAndFout_4_2 , b=f[4] , out=F_result_4 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndFout_5_1 );
    And(a=sel[2] , b=fourWayAndFout_5_1 , out=fourWayAndFout_5_2 );
    And(a=fourWayAndFout_5_2 , b=f[5] , out=F_result_5 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndFout_6_1 );
    And(a=sel[2] , b=fourWayAndFout_6_1 , out=fourWayAndFout_6_2 );
    And(a=fourWayAndFout_6_2 , b=f[6] , out=F_result_6 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndFout_7_1 );
    And(a=sel[2] , b=fourWayAndFout_7_1 , out=fourWayAndFout_7_2 );
    And(a=fourWayAndFout_7_2 , b=f[7] , out=F_result_7 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndFout_8_1 );
    And(a=sel[2] , b=fourWayAndFout_8_1 , out=fourWayAndFout_8_2 );
    And(a=fourWayAndFout_8_2 , b=f[8] , out=F_result_8 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndFout_9_1 );
    And(a=sel[2] , b=fourWayAndFout_9_1 , out=fourWayAndFout_9_2 );
    And(a=fourWayAndFout_9_2 , b=f[9] , out=F_result_9 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndFout_10_1 );
    And(a=sel[2] , b=fourWayAndFout_10_1 , out=fourWayAndFout_10_2 );
    And(a=fourWayAndFout_10_2 , b=f[10] , out=F_result_10 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndFout_11_1 );
    And(a=sel[2] , b=fourWayAndFout_11_1 , out=fourWayAndFout_11_2 );
    And(a=fourWayAndFout_11_2 , b=f[11] , out=F_result_11 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndFout_12_1 );
    And(a=sel[2] , b=fourWayAndFout_12_1 , out=fourWayAndFout_12_2 );
    And(a=fourWayAndFout_12_2 , b=f[12] , out=F_result_12 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndFout_13_1 );
    And(a=sel[2] , b=fourWayAndFout_13_1 , out=fourWayAndFout_13_2 );
    And(a=fourWayAndFout_13_2 , b=f[13] , out=F_result_13 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndFout_14_1 );
    And(a=sel[2] , b=fourWayAndFout_14_1 , out=fourWayAndFout_14_2 );
    And(a=fourWayAndFout_14_2 , b=f[14] , out=F_result_14 );

    And(a=sel[0] , b=notSelOne , out=fourWayAndFout_15_1 );
    And(a=sel[2] , b=fourWayAndFout_15_1 , out=fourWayAndFout_15_2 );
    And(a=fourWayAndFout_15_2 , b=f[15] , out=F_result_15 );

    // is it G?
    // 110
    And(a=notSelZero , b=sel[1] , out=fourWayAndGout_0_1 );
    And(a=sel[2] , b=fourWayAndGout_0_1 , out=fourWayAndGout_0_2 );
    And(a=g[0] , b=fourWayAndGout_0_2 , out=G_result_0 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndGout_1_1 );
    And(a=sel[2] , b=fourWayAndGout_1_1 , out=fourWayAndGout_1_2 );
    And(a=g[1] , b=fourWayAndGout_1_2 , out=G_result_1 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndGout_2_1 );
    And(a=sel[2] , b=fourWayAndGout_2_1 , out=fourWayAndGout_2_2 );
    And(a=g[2] , b=fourWayAndGout_2_2 , out=G_result_2 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndGout_3_1 );
    And(a=sel[2] , b=fourWayAndGout_3_1 , out=fourWayAndGout_3_2 );
    And(a=g[3] , b=fourWayAndGout_3_2 , out=G_result_3 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndGout_4_1 );
    And(a=sel[2] , b=fourWayAndGout_4_1 , out=fourWayAndGout_4_2 );
    And(a=g[4] , b=fourWayAndGout_4_2 , out=G_result_4 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndGout_5_1 );
    And(a=sel[2] , b=fourWayAndGout_5_1 , out=fourWayAndGout_5_2 );
    And(a=g[5] , b=fourWayAndGout_5_2 , out=G_result_5 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndGout_6_1 );
    And(a=sel[2] , b=fourWayAndGout_6_1 , out=fourWayAndGout_6_2 );
    And(a=g[6] , b=fourWayAndGout_6_2 , out=G_result_6 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndGout_7_1 );
    And(a=sel[2] , b=fourWayAndGout_7_1 , out=fourWayAndGout_7_2 );
    And(a=g[7] , b=fourWayAndGout_7_2 , out=G_result_7 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndGout_8_1 );
    And(a=sel[2] , b=fourWayAndGout_8_1 , out=fourWayAndGout_8_2 );
    And(a=g[8] , b=fourWayAndGout_8_2 , out=G_result_8 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndGout_9_1 );
    And(a=sel[2] , b=fourWayAndGout_9_1 , out=fourWayAndGout_9_2 );
    And(a=g[9] , b=fourWayAndGout_9_2 , out=G_result_9 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndGout_10_1 );
    And(a=sel[2] , b=fourWayAndGout_10_1 , out=fourWayAndGout_10_2 );
    And(a=g[10] , b=fourWayAndGout_10_2 , out=G_result_10 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndGout_11_1 );
    And(a=sel[2] , b=fourWayAndGout_11_1 , out=fourWayAndGout_11_2 );
    And(a=g[11] , b=fourWayAndGout_11_2 , out=G_result_11 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndGout_12_1 );
    And(a=sel[2] , b=fourWayAndGout_12_1 , out=fourWayAndGout_12_2 );
    And(a=g[12] , b=fourWayAndGout_12_2 , out=G_result_12 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndGout_13_1 );
    And(a=sel[2] , b=fourWayAndGout_13_1 , out=fourWayAndGout_13_2 );
    And(a=g[13] , b=fourWayAndGout_13_2 , out=G_result_13 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndGout_14_1 );
    And(a=sel[2] , b=fourWayAndGout_14_1 , out=fourWayAndGout_14_2 );
    And(a=g[14] , b=fourWayAndGout_14_2 , out=G_result_14 );

    And(a=notSelZero , b=sel[1] , out=fourWayAndGout_15_1 );
    And(a=sel[2] , b=fourWayAndGout_15_1 , out=fourWayAndGout_15_2 );
    And(a=g[15] , b=fourWayAndGout_15_2 , out=G_result_15 );

    // is it H?
    // 111
    And(a=sel[1] , b=sel[0] , out=fourWayAndHout_0_1 );
    And(a=sel[2] , b=fourWayAndHout_0_1 , out=fourWayAndHout_0_2 );
    And(a=h[0] , b=fourWayAndHout_0_2 , out=H_result_0 );

    And(a=sel[1] , b=sel[0] , out=fourWayAndHout_1_1 );
    And(a=sel[2] , b=fourWayAndHout_1_1 , out=fourWayAndHout_1_2 );
    And(a=h[1] , b=fourWayAndHout_1_2 , out=H_result_1 );

    And(a=sel[1] , b=sel[0] , out=fourWayAndHout_2_1 );
    And(a=sel[2] , b=fourWayAndHout_2_1 , out=fourWayAndHout_2_2 );
    And(a=h[2] , b=fourWayAndHout_2_2 , out=H_result_2 );

    And(a=sel[1] , b=sel[0] , out=fourWayAndHout_3_1 );
    And(a=sel[2] , b=fourWayAndHout_3_1 , out=fourWayAndHout_3_2 );
    And(a=h[3] , b=fourWayAndHout_3_2 , out=H_result_3 );

    And(a=sel[1] , b=sel[0] , out=fourWayAndHout_4_1 );
    And(a=sel[2] , b=fourWayAndHout_4_1 , out=fourWayAndHout_4_2 );
    And(a=h[4] , b=fourWayAndHout_4_2 , out=H_result_4 );

    And(a=sel[1] , b=sel[0] , out=fourWayAndHout_5_1 );
    And(a=sel[2] , b=fourWayAndHout_5_1 , out=fourWayAndHout_5_2 );
    And(a=h[5] , b=fourWayAndHout_5_2 , out=H_result_5 );

    And(a=sel[1] , b=sel[0] , out=fourWayAndHout_6_1 );
    And(a=sel[2] , b=fourWayAndHout_6_1 , out=fourWayAndHout_6_2 );
    And(a=h[6] , b=fourWayAndHout_6_2 , out=H_result_6 );

    And(a=sel[1] , b=sel[0] , out=fourWayAndHout_7_1 );
    And(a=sel[2] , b=fourWayAndHout_7_1 , out=fourWayAndHout_7_2 );
    And(a=h[7] , b=fourWayAndHout_7_2 , out=H_result_7 );

    And(a=sel[1] , b=sel[0] , out=fourWayAndHout_8_1 );
    And(a=sel[2] , b=fourWayAndHout_8_1 , out=fourWayAndHout_8_2 );
    And(a=h[8] , b=fourWayAndHout_8_2 , out=H_result_8 );

    And(a=sel[1] , b=sel[0] , out=fourWayAndHout_9_1 );
    And(a=sel[2] , b=fourWayAndHout_9_1 , out=fourWayAndHout_9_2 );
    And(a=h[9] , b=fourWayAndHout_9_2 , out=H_result_9 );

    And(a=sel[1] , b=sel[0] , out=fourWayAndHout_10_1 );
    And(a=sel[2] , b=fourWayAndHout_10_1 , out=fourWayAndHout_10_2 );
    And(a=h[10] , b=fourWayAndHout_10_2 , out=H_result_10 );

    And(a=sel[1] , b=sel[0] , out=fourWayAndHout_11_1 );
    And(a=sel[2] , b=fourWayAndHout_11_1 , out=fourWayAndHout_11_2 );
    And(a=h[11] , b=fourWayAndHout_11_2 , out=H_result_11);

    And(a=sel[1] , b=sel[0] , out=fourWayAndHout_12_1 );
    And(a=sel[2] , b=fourWayAndHout_12_1 , out=fourWayAndHout_12_2 );
    And(a=h[12] , b=fourWayAndHout_12_2 , out=H_result_12 );

    And(a=sel[1] , b=sel[0] , out=fourWayAndHout_13_1 );
    And(a=sel[2] , b=fourWayAndHout_13_1 , out=fourWayAndHout_13_2 );
    And(a=h[13] , b=fourWayAndHout_13_2 , out=H_result_13 );

    And(a=sel[1] , b=sel[0] , out=fourWayAndHout_14_1 );
    And(a=sel[2] , b=fourWayAndHout_14_1 , out=fourWayAndHout_14_2 );
    And(a=h[14] , b=fourWayAndHout_14_2 , out=H_result_14 );

    And(a=sel[1] , b=sel[0] , out=fourWayAndHout_15_1 );
    And(a=sel[2] , b=fourWayAndHout_15_1 , out=fourWayAndHout_15_2 );
    And(a=h[15] , b=fourWayAndHout_15_2 , out=H_result_15 );

    Or(a=A_result_0 , b=B_result_0 , out=AB_out_1_0 );
    Or(a=C_result_0 , b=D_result_0 , out=CD_out_1_0 );
    Or(a=E_result_0 , b=F_result_0 , out=EF_out_1_0 );
    Or(a=G_result_0 , b=H_result_0 , out=GH_out_1_0 );

    Or(a=AB_out_1_0 , b=CD_out_1_0 , out=ABCD_out_2_0 );
    Or(a=EF_out_1_0 , b=GH_out_1_0 , out=EFGH_out_2_0 );

    Or(a=ABCD_out_2_0 , b=EFGH_out_2_0 , out=out[0] );

    Or(a=A_result_1 , b=B_result_1 , out=AB_out_1_1 );
    Or(a=C_result_1 , b=D_result_1 , out=CD_out_1_1 );
    Or(a=E_result_1 , b=F_result_1 , out=EF_out_1_1 );
    Or(a=G_result_1 , b=H_result_1 , out=GH_out_1_1 );

    Or(a=AB_out_1_1 , b=CD_out_1_1 , out=ABCD_out_2_1 );
    Or(a=EF_out_1_1 , b=GH_out_1_1 , out=EFGH_out_2_1 );

    Or(a=ABCD_out_2_1 , b=EFGH_out_2_1 , out=out[1] );

    Or(a=A_result_2 , b=B_result_2 , out=AB_out_1_2 );
    Or(a=C_result_2 , b=D_result_2 , out=CD_out_1_2 );
    Or(a=E_result_2 , b=F_result_2 , out=EF_out_1_2 );
    Or(a=G_result_2 , b=H_result_2 , out=GH_out_1_2 );

    Or(a=AB_out_1_2 , b=CD_out_1_2 , out=ABCD_out_2_2 );
    Or(a=EF_out_1_2 , b=GH_out_1_2 , out=EFGH_out_2_2 );

    Or(a=ABCD_out_2_2 , b=EFGH_out_2_2 , out=out[2] );

    Or(a=A_result_3 , b=B_result_3 , out=AB_out_1_3 );
    Or(a=C_result_3 , b=D_result_3 , out=CD_out_1_3 );
    Or(a=E_result_3 , b=F_result_3 , out=EF_out_1_3 );
    Or(a=G_result_3 , b=H_result_3 , out=GH_out_1_3 );

    Or(a=AB_out_1_3 , b=CD_out_1_3 , out=ABCD_out_2_3 );
    Or(a=EF_out_1_3 , b=GH_out_1_3 , out=EFGH_out_2_3 );

    Or(a=ABCD_out_2_3 , b=EFGH_out_2_3 , out=out[3] );

    Or(a=A_result_4 , b=B_result_4 , out=AB_out_1_4 );
    Or(a=C_result_4 , b=D_result_4 , out=CD_out_1_4 );
    Or(a=E_result_4 , b=F_result_4 , out=EF_out_1_4 );
    Or(a=G_result_4 , b=H_result_4 , out=GH_out_1_4 );

    Or(a=AB_out_1_4 , b=CD_out_1_4 , out=ABCD_out_2_4 );
    Or(a=EF_out_1_4 , b=GH_out_1_4 , out=EFGH_out_2_4 );

    Or(a=ABCD_out_2_4 , b=EFGH_out_2_4 , out=out[4] );

    Or(a=A_result_5 , b=B_result_5 , out=AB_out_1_5 );
    Or(a=C_result_5 , b=D_result_5 , out=CD_out_1_5 );
    Or(a=E_result_5 , b=F_result_5 , out=EF_out_1_5 );
    Or(a=G_result_5 , b=H_result_5 , out=GH_out_1_5 );

    Or(a=AB_out_1_5 , b=CD_out_1_5 , out=ABCD_out_2_5 );
    Or(a=EF_out_1_5 , b=GH_out_1_5 , out=EFGH_out_2_5 );

    Or(a=ABCD_out_2_5 , b=EFGH_out_2_5 , out=out[5] );

    Or(a=A_result_6 , b=B_result_6 , out=AB_out_1_6 );
    Or(a=C_result_6 , b=D_result_6 , out=CD_out_1_6 );
    Or(a=E_result_6 , b=F_result_6 , out=EF_out_1_6 );
    Or(a=G_result_6 , b=H_result_6 , out=GH_out_1_6 );

    Or(a=AB_out_1_6 , b=CD_out_1_6 , out=ABCD_out_2_6 );
    Or(a=EF_out_1_6 , b=GH_out_1_6 , out=EFGH_out_2_6 );

    Or(a=ABCD_out_2_6 , b=EFGH_out_2_6 , out=out[6] );

    Or(a=A_result_7 , b=B_result_7 , out=AB_out_1_7 );
    Or(a=C_result_7 , b=D_result_7 , out=CD_out_1_7 );
    Or(a=E_result_7 , b=F_result_7 , out=EF_out_1_7 );
    Or(a=G_result_7 , b=H_result_7 , out=GH_out_1_7 );

    Or(a=AB_out_1_7 , b=CD_out_1_7 , out=ABCD_out_2_7 );
    Or(a=EF_out_1_7 , b=GH_out_1_7 , out=EFGH_out_2_7 );

    Or(a=ABCD_out_2_7 , b=EFGH_out_2_7 , out=out[7] );

    Or(a=A_result_8 , b=B_result_8 , out=AB_out_1_8 );
    Or(a=C_result_8 , b=D_result_8 , out=CD_out_1_8 );
    Or(a=E_result_8 , b=F_result_8 , out=EF_out_1_8 );
    Or(a=G_result_8 , b=H_result_8 , out=GH_out_1_8 );

    Or(a=AB_out_1_8 , b=CD_out_1_8 , out=ABCD_out_2_8 );
    Or(a=EF_out_1_8 , b=GH_out_1_8 , out=EFGH_out_2_8 );

    Or(a=ABCD_out_2_8 , b=EFGH_out_2_8 , out=out[8] );

    Or(a=A_result_9 , b=B_result_9 , out=AB_out_1_9 );
    Or(a=C_result_9 , b=D_result_9 , out=CD_out_1_9 );
    Or(a=E_result_9 , b=F_result_9 , out=EF_out_1_9 );
    Or(a=G_result_9 , b=H_result_9 , out=GH_out_1_9 );

    Or(a=AB_out_1_9 , b=CD_out_1_9 , out=ABCD_out_2_9 );
    Or(a=EF_out_1_9 , b=GH_out_1_9 , out=EFGH_out_2_9 );

    Or(a=ABCD_out_2_9 , b=EFGH_out_2_9 , out=out[9] );

    Or(a=A_result_10 , b=B_result_10 , out=AB_out_1_10 );
    Or(a=C_result_10 , b=D_result_10 , out=CD_out_1_10 );
    Or(a=E_result_10 , b=F_result_10 , out=EF_out_1_10 );
    Or(a=G_result_10 , b=H_result_10 , out=GH_out_1_10 );

    Or(a=AB_out_1_10 , b=CD_out_1_10 , out=ABCD_out_2_10 );
    Or(a=EF_out_1_10 , b=GH_out_1_10 , out=EFGH_out_2_10 );

    Or(a=ABCD_out_2_10 , b=EFGH_out_2_10 , out=out[10] );

    Or(a=A_result_11 , b=B_result_11 , out=AB_out_1_11 );
    Or(a=C_result_11 , b=D_result_11 , out=CD_out_1_11 );
    Or(a=E_result_11 , b=F_result_11 , out=EF_out_1_11 );
    Or(a=G_result_11 , b=H_result_11 , out=GH_out_1_11 );

    Or(a=AB_out_1_11 , b=CD_out_1_11 , out=ABCD_out_2_11 );
    Or(a=EF_out_1_11 , b=GH_out_1_11 , out=EFGH_out_2_11 );

    Or(a=ABCD_out_2_11 , b=EFGH_out_2_11 , out=out[11] );

    Or(a=A_result_12 , b=B_result_12 , out=AB_out_1_12 );
    Or(a=C_result_12 , b=D_result_12 , out=CD_out_1_12 );
    Or(a=E_result_12 , b=F_result_12 , out=EF_out_1_12 );
    Or(a=G_result_12 , b=H_result_12 , out=GH_out_1_12 );

    Or(a=AB_out_1_12 , b=CD_out_1_12 , out=ABCD_out_2_12 );
    Or(a=EF_out_1_12 , b=GH_out_1_12 , out=EFGH_out_2_12 );

    Or(a=ABCD_out_2_12 , b=EFGH_out_2_12 , out=out[12] );

    Or(a=A_result_13 , b=B_result_13 , out=AB_out_1_13 );
    Or(a=C_result_13 , b=D_result_13 , out=CD_out_1_13 );
    Or(a=E_result_13 , b=F_result_13 , out=EF_out_1_13 );
    Or(a=G_result_13 , b=H_result_13 , out=GH_out_1_13 );

    Or(a=AB_out_1_13 , b=CD_out_1_13 , out=ABCD_out_2_13 );
    Or(a=EF_out_1_13 , b=GH_out_1_13 , out=EFGH_out_2_13 );

    Or(a=ABCD_out_2_13 , b=EFGH_out_2_13 , out=out[13] );

    Or(a=A_result_14 , b=B_result_14 , out=AB_out_1_14 );
    Or(a=C_result_14 , b=D_result_14 , out=CD_out_1_14 );
    Or(a=E_result_14 , b=F_result_14 , out=EF_out_1_14 );
    Or(a=G_result_14 , b=H_result_14 , out=GH_out_1_14 );

    Or(a=AB_out_1_14 , b=CD_out_1_14 , out=ABCD_out_2_14 );
    Or(a=EF_out_1_14 , b=GH_out_1_14 , out=EFGH_out_2_14 );

    Or(a=ABCD_out_2_14 , b=EFGH_out_2_14 , out=out[14] );

    Or(a=A_result_15 , b=B_result_15 , out=AB_out_1_15 );
    Or(a=C_result_15 , b=D_result_15 , out=CD_out_1_15 );
    Or(a=E_result_15 , b=F_result_15 , out=EF_out_1_15 );
    Or(a=G_result_15 , b=H_result_15 , out=GH_out_1_15 );

    Or(a=AB_out_1_15 , b=CD_out_1_15 , out=ABCD_out_2_15 );
    Or(a=EF_out_1_15 , b=GH_out_1_15 , out=EFGH_out_2_15 );

    Or(a=ABCD_out_2_15 , b=EFGH_out_2_15 , out=out[15] );
}
```

**DMux4Way**: This chip allows two selector bits to define the signal to carry to one of four outputs. 

```
CHIP DMux4Way {
    IN in, sel[2];
    OUT a, b, c, d;

    PARTS:
    Not(in=sel[0] , out=notSelZero );
    Not(in=sel[1] , out=notSelOne );

    And(a=notSelZero , b=notSelOne , out=out_a_1 );
    And(a=out_a_1 , b=in , out=a );

    And(a=sel[0] , b=notSelOne , out=out_b_1 );
    And(a=out_b_1 , b=in , out=b );

    And(a=sel[1] , b=notSelZero , out=out_c_1 );
    And(a=out_c_1 , b=in, out=c );

    And(a=sel[0] , b=sel[1] , out=out_d_1 );
    And(a=out_d_1 , b=in , out=d );
}
```

```
|in | sel  | a | b | c | d |
| 0 |  00  | 0 | 0 | 0 | 0 |
| 0 |  01  | 0 | 0 | 0 | 0 |
| 0 |  10  | 0 | 0 | 0 | 0 |
| 0 |  11  | 0 | 0 | 0 | 0 |
| 1 |  00  | 1 | 0 | 0 | 0 |
| 1 |  01  | 0 | 1 | 0 | 0 |
| 1 |  10  | 0 | 0 | 1 | 0 |
| 1 |  11  | 0 | 0 | 0 | 1 |
```

**DMux8Way**: This is the same as a DMux4way but instead we can select from 8 values to output to based on a 3 bit selector

```
CHIP DMux8Way {
    IN in, sel[3];
    OUT a, b, c, d, e, f, g, h;

    PARTS:
    Not(in=sel[0] , out=notSelZero );
    Not(in=sel[1] , out=notSelOne );
    Not(in=sel[2] , out=notSelTwo );

    And(a=notSelZero , b=notSelOne , out=out_a_1 );
    And(a=out_a_1 , b=notSelTwo , out=out_a_2 );
    And(a=out_a_2 , b=in , out=a );

    And(a=sel[0] , b=notSelOne , out=out_b_1 );
    And(a=notSelTwo , b=out_b_1 , out=out_b_2 );
    And(a=out_b_2 , b=in , out=b );

    And(a=sel[1] , b=notSelZero , out=out_c_1 );
    And(a=out_c_1 , b=notSelTwo , out=out_c_2 );
    And(a=out_c_2 , b=in, out=c );

    And(a=notSelTwo , b=sel[1] , out=out_d_1 );
    And(a=sel[0] , b=out_d_1 , out=out_d_2 );
    And(a=out_d_2 , b=in , out=d );

    And(a=sel[2] , b=notSelOne , out=out_e_1 );
    And(a=notSelZero , b=out_e_1 , out=out_e_2 );
    And(a=in , b=out_e_2 , out=e );

    And(a=sel[2] , b=sel[0] , out=out_f_1 );
    And(a=out_f_1 , b=notSelOne , out=out_f_2 );
    And(a=out_f_2 , b=in , out=f );

    And(a=sel[2] , b=sel[1] , out=out_g_1 );
    And(a=out_g_1 , b=notSelZero , out=out_g_2 );
    And(a=in , b=out_g_2 , out=g );

    And(a=sel[0] , b=sel[1] , out=out_h_1 );
    And(a=sel[2] , b=out_h_1 , out=out_h_2 );
    And(a=in , b=out_h_2 , out=h );
}
```

```
|in |  sel  | a | b | c | d | e | f | g | h |
| 0 |  000  | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 0 |  001  | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 0 |  010  | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 0 |  011  | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 0 |  100  | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 0 |  101  | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 0 |  110  | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 0 |  111  | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 1 |  000  | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 1 |  001  | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 |
| 1 |  010  | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 |
| 1 |  011  | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 |
| 1 |  100  | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 |
| 1 |  101  | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 |
| 1 |  110  | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 |
| 1 |  111  | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 |
```

