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
