Write C expressions that evaluate to 1 when the following conditions are true and to 0 when they are false. Assume x is of type int.

A. Any bit of x equals 1:

```c
return !!x;
```

B. Any bit of x equals 0:

```c
return !!(~x);
```

C. Any bit in the least significant byte of x equals 1:

```c
return !!(x & 0xFF);
```

D. Any bit in the most significant byte of x equals 0:

```c
return !!(~(x >> (w - 1)) & 0xFF);
```

2. 

```c
long decode2(long x, long y, long z)
{
    long t1 = y - z;
    long t2 = t1 >> 63;
    long t3 = t1 * x;
    long t4 = t2 ^ t3;
    return t4;
}
```