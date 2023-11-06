## Question 4: Even or Odd and Factorial

### What We're Doing

We'll read a number from the user and tell them if it's even or odd. Then, we'll calculate the factorial of that number.

### Pseudocode

Follow these steps:

1. Set up USART communication.
2. Continuously:
   - Get a number from USART.
   - Check if it's even or odd, and tell the user.
   - Calculate the factorial of the number.
   - Send the result.
   - Pause for 5 seconds.

### Your Task

Work on the logic inside the `while` loop of the `main` function to perform the even/odd check and factorial calculation.

### Code Structure

Your `main` function will look something like this:

```c
int main(void) {
    // Initialize USART
    usart_setup();

    while (1) {
        // Receive number from USART
        int number = usart_rx() - '0';  // Convert ASCII to integer
        char buffer[50];

        // Check if the number is even or odd
        if (number % 2 == 0) {
            sprintf(buffer, "The number is even.\r\n");
        } else {
            sprintf(buffer, "The number is odd.\r\n");
        }
        usart_putstr(buffer);

        // Calculate factorial
        unsigned long factorial = 1;
        for (int i = 1; i <= number; ++i) {
            factorial *= i;
        }

        // Send factorial result
        sprintf(buffer, "The factorial is: %lu\r\n", factorial);
        usart_putstr(buffer);

        // Delay for 5 seconds
        _delay_ms(5000);
    }
}
```
