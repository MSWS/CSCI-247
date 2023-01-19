1. Given a floating-point format with a k-bit exponent and an n-bit fraction, write formulas for the exponent E, the significand M, the fraction f, and the value V for the quantities that follow. In addition, describe the bit representation.

    ## A. The number 7.0
    Keeping in mind the general formula: V = (-1)^s * M * 2^E,
    Where V is the value, M is the mantissa, and E is the exponent
    7(10) = 111(2) = 1.11 * 2^2
    
    M = 1.11
    f = 11
    Bias = 2^(k - 1) - 1
    E = 2 + Bias

    V = (-1) ^ 0 * (1.)11 * 2^(2 + Bias)
        = 1 * 1.11 * 2^(2 + Bias)
        = 7

    Potential bit representation (let K = 4, M = 3)

    7 is positive (sign = 0)
    M = 1.11
    f = 11 (implicit leading 1)
    Bias = 2^(4 - 1) - 1 = 7
    E = 2 + Bias = 9 -> 1001

    Resulting in:
    
    | Sign | Exp  | Frac |
    | ---- | ---- | ---- |
    | 0    | 1001 | 011  |

    ## B. Largest odd integer that can be represented exactly
    - Off the bat we can say that the sign should be 0
    - Mantissa would be all 1's for largest possible value.
    - We want our exponent to be the largest possible odd number
        - Logically, if we multiply the mantissa beyond its specification ( > 23 bytes in the case of M = 23), we would "lose" the accuracy of the least significant bit.
        - Thus, our exponent should be M.

    For example (k = 4, M = 3):
    | Sign | Exp  | Frac | Result |
    | ---- | ---- | ---- | ------ |
    | 0    | 1010 | 111  | 15     |
    Results in 15, which is odd.

    Whereas:
    | Sign | Exp  | Frac | Result |
    | ---- | ---- | ---- | ------ |
    | 0    | 1011 | 111  | 30     |

    Results in 30, which is even.<br>
    Note that the exponent value is 10 and 11 respectively due to the bias of + 7.<br>
    (Meaning the underlying 2^k value is actually 3 and 4).
   
    Summarizing this logic gets us:
      - Sign = 0
      - Bias = 2^(k - 1) - 1
      - Exp = M + Bias
      - M = 2^M - 1

    An example of k = 5, M = 4:
      - Sign = 0
      - Bias = 2^(5 - 1) - 1 = 15
      - Exp = 4 + 15 = 19 -> 10011
      - M = 2^(4 - 1) = 15 -> 1111
    
    Resulting in
    | Sign | Exp   | Frac | Result |
    | ---- | ----- | ---- | ------ |
    | 0    | 10010 | 1111 | 31     |

    ## C. The reciprocal of the smallest positive normalized value
    1. In order to find the reciprocal of any number, we can simply "flip" the numerator and denominator.
     - This can be achieved in floating point representation by multiplying the exponent by -1.
    2. The smallest possible value given k exponent bits and M fraction bits is:
     - When the mantissa is 0
     - M = 0
     - When the exponent is as small as possible, multiplied by -1
     - Exp - Bias = E
     - Bias = 2^(k - 1) - 1
     - We can't have an Exp of 0 (special case), so set it to 1:
     - E = (1 - Bias) * -1
    3. Giving an example of k = 5, M = 4:
     - M = 0
     - Bias = 2^(5 - 1) - 1 = 15
     - E = (1 - 15) * -1 = (-14) * -1 = 14
     - Exp = 14 + 15 = 29 -> 11101
    
    Resulting in:
    | Sign | Exp   | Frac | Result |
    | ---- | ----- | ---- | ------ |
    | 0    | 11101 | 0000 | 16384  |


2. You have been assigned the task of writing a C function to compute a floating-point representation of 2x . You decide that the best way to do this is to directly construct the IEEE  single-precision representation of the result. When x is too small, your routine will return 0.0. When x is too large, it will return +∞ . Fill in the blank portions of the code that follows to compute the correct result. Assume the function u2f returns a floating point value having an identical bit representation as its unsigned argument.


```c
/* Compute 2**x */
float fpwr2(int x) {
    unsigned exp, frac;
    unsigned u;
    if (x <  -149) {
        /* Too small. Return 0.0 */
        exp = 0;
        frac = 0;
    } else if (x < -126) {
        /* Denormalized result */
        exp = 1;
        frac = (1 << (x + 149));
    } else if (x < 128) {
        /* Normalized result. */
        exp = x + 127;
        frac = 0;
    } else {
        /* Too big.  Return +oo */
        exp = 255;
        frac = 0;
    }
    u = exp << 23 | frac;
    return u2f(u);
}
```