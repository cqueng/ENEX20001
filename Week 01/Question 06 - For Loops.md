## Exploring For Loops Through Examples

### Objective

To understand and practice `for` loops, we'll run through several examples that demonstrate counting up, counting down, and counting in multiples. These concepts are essential for controlling the flow of a program.

### For Loop Fundamentals

The `for` loop is a control structure that allows us to repeat a set of instructions until a specific condition is met. It's particularly useful for iterating a fixed number of times, such as processing items in an array or generating a sequence of numbers.

### Pseudocode Overview

We will explore three `for` loop patterns:

1. **Counting Up**: Starting at a low number and incrementing up to a higher number.
2. **Counting Down**: Starting at a high number and decrementing down to a lower number.
3. **Counting in Multiples**: Starting at a number and counting up in multiples (e.g., by 2's).

### Task Instructions

1. Implement a `for` loop that counts from 0 to 9, printing each number.
2. Implement a `for` loop that counts down from 9 to 0, printing each number.
3. Implement a `for` loop that counts from 0 to 18 in multiples of 2, printing each number.

### Sample Code Structure

Your code will have a structure similar to the following for each pattern - Add these to your main loop and print the result of i:

**Counting Up:**

```c
int main(void) {
    usart_setup(); // Initialize USART communication
    char buffer[50]; // Buffer to hold the formatted string

    while (1) {
        // Inform the user that counting up starts
        usart_putstr("Counting up from 0 to 9:\r\n");

        // For loop to count up from 0 to 9
        for (int i = 0; i < 10; i++) {
            sprintf(buffer, "Number: %d\r\n", i);
            usart_putstr(buffer);
            _delay_ms(500); // Add a small delay between numbers
        }

        // Delay for 5 seconds before restarting the count
        _delay_ms(5000);
    }

    return 0; // This return will never be reached
}
```

**Counting Down:**

```c
for (int i = 9; i >= 0; i--) {
    // Print i or perform other actions
}
```

**Counting in Multiples:**

```c
for (int i = 0; i <= 18; i += 2) {
    // Print i or perform other actions
}
```

### Experimenting With Loops

After understanding these patterns, try altering the start and end values, as well as the increment/decrement steps, to see how the behavior of the loop changes. This will give you a hands-on experience with the flexibility and power of `for` loops.
