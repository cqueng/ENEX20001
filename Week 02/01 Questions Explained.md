# Recap of converting

## Converting Decimal to Binary

Binary numbers are the foundation of computer systems, using only `0` and `1` to represent all values. Here's a step-by-step guide to convert a decimal number to a binary number, specifically focusing on 4-bit binary numbers for simplicity.

### Steps to Convert Decimal to Binary:

1. Divide the decimal number by 2.
2. Write down the remainder (0 if the result is even, 1 if odd).
3. Divide the quotient by 2.
4. Repeat step 2, writing down the remainder.
5. Continue the process until the quotient is 0.
6. The binary number is the remainders read from the last to the first.

#### Example:

Convert decimal `10` to binary:

- 10 ÷ 2 = 5, remainder 0
- 5 ÷ 2 = 2, remainder 1
- 2 ÷ 2 = 1, remainder 0
- 1 ÷ 2 = 0, remainder 1

Read the remainders in reverse (bottom to top): `1010` (4-bit binary).

## Converting Between Binary Numbers

Bitwise operations such as AND, OR, NOT, and XOR are used to manipulate individual bits in a binary number. Below is an explanation of each operation and examples of their use.

### Bitwise Operations:

- **AND (`&`)**: Compares each corresponding bit of two binary numbers. The result is `1` if both are `1`; otherwise, it's `0`.
- **OR (`|`)**: Compares each corresponding bit of two binary numbers. The result is `1` if at least one bit is `1`.
- **NOT (`~`)**: Flips all the bits of a binary number. If a bit is `0`, it becomes `1`, and vice versa.
- **XOR (`^`)**: Compares each corresponding bit of two binary numbers. The result is `1` if the bits are different; otherwise, it's `0`.

### Examples:

**Convert binary `1010` to `1100` (4 bits):**

- Set the third bit to `1` (from left): `1010 | 0100` → `1110`.
- Clear the second bit to `0`: `1110 & 1101` → `1100`.

**Using NOT and XOR:**

**Invert all bits in `1010`:**

- Apply NOT: `~1010` → `0101` (assuming a 4-bit number).

**Toggle bits using XOR:**

- Toggle the second and third bits of `1100`: `1100 ^ 0110` → `1010`.
  - `1 XOR 1` gives `0`, `1 XOR 0` gives `1`.

Remember, the NOT operation in most programming languages will consider the full width of the type, so for an 8-bit number, `~1010` would actually give `11110101`. Make sure to limit the operation to the required number of bits.


## Exercise 1

The goal is to toggle a single LED connected to PORTB.

### Including Headers

We include the necessary headers for input/output definitions and delay functions:

```c
#include <avr/io.h>
#include <util/delay.h>
```

### Main Function

The `main` function is declared as the entry point of the program:

```c
int main(void) {
  // ... main function code ...
}
```

### Configuring I/O

Set up the data direction register for PORTB to configure pin 0 as an output:

```c
DDRB = 0b00000001;
```

### Infinite Loop

We use an infinite `while` loop to continuously run our LED toggle logic:

```c
while (1) {
  // ... loop content ...
}
```

### LED Toggle

Within the loop, we turn the LED on and off with a delay between each state:

```c
PORTB = 0b00000001; // Turn on the LED
_delay_ms(1000);    // Delay for 1 second

PORTB = 0b00000000; // Turn off the LED
_delay_ms(1000);    // Delay for 1 second
```

## Exercise 2

This exercise also toggles a single LED on PORTB but uses bitwise operations for control.

### Including Headers

Headers remain the same as in Exercise 1.

### Main Function

The structure of the `main` function is similar to Exercise 1:

```c
int main(void) {
  // ... main function code ...
}
```

### Configuring I/O

We use bitwise OR to set pin 0 of PORTB as output:

```c
DDRB |= 1 << 0;
```

### Infinite Loop

The infinite loop logic is consistent with previous exercises:

```c
while (1) {
  // ... loop content ...
}
```

### LED Toggle with Bitwise Operations

Bitwise operations are used to toggle the LED:

```c
PORTB |= 1 << 0;      // Turn on the LED using bitwise OR
_delay_ms(500);       // Delay for 0.5 seconds

PORTB &= ~(1 << 0);   // Turn off the LED using bitwise AND and NOT
_delay_ms(1000);      // Delay for 1 second
```

## Exercise 3

This exercise involves toggling multiple LEDs connected to PORTD in an alternating pattern.

### Including Headers

The headers are the same as in the previous exercises.

### Main Function

We declare the `main` function as usual:

```c
int main(void) {
  // ... main function code ...
}
```

### Configuring I/O

All pins of PORTD are set as outputs:

```c
DDRD = 0b11111111;
```

### Infinite Loop

An infinite loop is used to create the LED toggle pattern:

```c
while (1) {
  // ... loop content ...
}
```

### Alternating LED Pattern

We alternate the pattern of LEDs being turned on and off:

```c
PORTD = 0b10101010;  // Turn on alternate LEDs
_delay_ms(1000);     // Delay for 1 second

PORTD = 0b01010101;  // Turn off alternate LEDs
_delay_ms(1000);     // Delay for 1 second
```

