---
layout: post
title: "Represent 64-bit Unsigned Integer in PHP"
---

Recently, while implementing a CRC-64 checksum for cloud file existence checks
(to avoid redundant uploads), I encountered an interesting issue with large
hexadecimal values in PHP. My usual approach involves a precomputed table for
faster calculations, and the polynomial result is represented in hexadecimal.
However, I noticed unexpected behavior when dealing with specific values.

For example, the hexadecimal value `0x995DC9BBDF1939FA` should correspond to
the decimal value `11051210869376104954`. However, when dumped in PHP, it's
displayed as a floating-point number:

```bash
% php -r "var_dump(0x995DC9BBDF1939FA);"
float(1.1051210869376104E+19)
```

This is unexpected and undesirable for checksum calculations. Upon
investigation, I confirmed that the value is within the bounds of a
64-bit integer:

```
2^64 > 11051210869376104954
True
```

The issue arises because, despite running on a 64-bit system, PHP's maximum
signed integer value `PHP_INT_MAX` is smaller:

```bash
% php -r "var_dump(PHP_INT_MAX);"
int(9223372036854775807)
```

This limitation stems from PHP's lack of an unsigned integer data type. It uses
`int` (signed) and `float` for numerical representation.

To understand this better, let's examine `PHP_INT_MAX` in binary:

```
01111111_11111111_11111111_11111111_11111111_11111111_11111111_11111111 (2)
 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 63 bits
```

The most significant bit (MSB) represents the sign (0 for positive, 1 for
negative). In `PHP_INT_MAX`, the MSB is 0, and the remaining bits are set,
effectively representing `2^63`. When a number exceeds `PHP_INT_MAX`, PHP
automatically converts it to a float.

So, how can we accurately represent this large number in PHP?

## Solutions

### Handling High and Low Bits Separately

We can split the 64-bit number into two 32-bit parts (high and low) and
reconstruct it during runtime:

```
(hex) 0x995DC9BBDF1939FA
(dec) 10011001_01011101_11001001_10111011_11011111_00011001_00111001_11111010
      ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      high bits                           low bits
```

To extract the high bits, we right-shift the number by 32 bits. For the low
bits, we apply a bitmask `0xFFFFFFFF` using the bitwise AND operator:

```php
<?php

$high = 0x995DC9BB;
$low = 0xDF1939FA;
```

We can then reconstruct the original value by left-shifting the high bits by
32 bits and combining them with the low bits using the bitwise OR operator:

```php
<?php

$number = $high << 32 | $low; // 0x995DC9BBDF1939FA
```

#### Illustration

```
(H) 10011001_01011101_11001001_10111011                                      = 0x995DC9BB
 << 32 bits
  = -----------------------------------------------------------------------
    10011001_01011101_11001001_10111011_00000000_00000000_00000000_00000000
 OR
(L)                                     11011111_00011001_00111001_11111010  = 0xDF1939FA
  = -----------------------------------------------------------------------
    10011001_01011101_11001001_10111011_11011111_00011001_00111001_11111010  = 0x995DC9BBDF1939FA
```

### Manual Hexadecimal to Decimal Conversion

Another approach involves manually converting the hexadecimal string to
decimal, bypassing PHP's implicit conversion. We can achieve this using
the `pack` and `unpack` functions:

```php
<?php

// 0x995DC9BBDF1939FA
$number = unpack(
    'J', pack('H*', '995DC9BBDF1939FA')
);
```

We use pack with the format `H*` to encode the hexadecimal string into a binary
string. Then, unpack with format `J` decodes it as a 64-bit unsigned integer.
Refer to the [PHP documentation](https://www.php.net/manual/en/function.pack.php)
for a complete description of format codes.

## Performance Comparison

A quick benchmark comparing the two methods over 1 million iterations reveals
a significant performance difference:

```php
<?php

// helper
// ===================================================

function benchmark(callable $callback): int
{
    $start = (int) (microtime(true) * 1_000);

    $callback();

    $end = (int) (microtime(true) * 1_000);

    return $end - $start;
}

// pack && unpack
// ===================================================

$elapsed = benchmark(function () {
    for ($i = 0; $i < 1_000_000; $i++) {
        unpack(
            'J', pack('H*', '995DC9BBDF1939FA')
        );
    }
});

printf('pack & unpack: %dms'.PHP_EOL, $elapsed);

// bitwise
// ===================================================

$elapsed = benchmark(function () {
    for ($i = 0; $i < 1_000_000; $i++) {
        0x995DC9BB << 32 | 0xDF1939FA;
    }
});

printf('bitwise      : %dms'.PHP_EOL, $elapsed);
```

On my 2019 Intel iMac, the bitwise approach took 5ms, while the `pack`/`unpack`
method took 133ms – a 26x difference. This difference in performance is not
surprising. The functions involve several layers of overhead,
[format checking](https://github.com/php/php-src/blob/a9a1ac2f8db80f1718d09f0f733aee388272e929/ext/standard/pack.c#L255-L375),
[parsing the hexadecimal string](https://github.com/php/php-src/blob/a9a1ac2f8db80f1718d09f0f733aee388272e929/ext/standard/pack.c#L486-L527),
and ultimately, they also rely on bitwise operations
[internally](https://github.com/php/php-src/blob/a9a1ac2f8db80f1718d09f0f733aee388272e929/ext/standard/pack.c#L109-L115)
to represent the unsigned integer. These extra steps contribute to the increased execution time.

Given that I'm dealing with some files around 50MiB, performance is crucial,
making the bitwise approach the preferred choice.
