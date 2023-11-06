## Question 3: Multiplying Numbers via USART

### The Objective

Now we'll receive numbers from the user, multiply them, and send back the result. This will be your introduction to receiving data (RX) as well as sending it (TX).

### Pseudocode

Here's what we'll do:

1. Start USART communication.
2. Ask the user for two numbers.
3. Read the numbers from USART.
4. Multiply them using a special function.
5. Send back the result.
6. Wait a bit and repeat.

### Your Task

Add a multiplication function to your code and update the `main` function to use it.

### Code Snippet

Here's the additional function and the updated `main` function:

```c
int multiply(int a, int b); // Multiply two numbers - Add this to top of code

int main(void) {
    usart_setup(); // Initialize USART communication

    while (1) {
        int num1 = usart_rx() - '0';  // Convert ASCII to integer
        int num2 = usart_rx() - '0';  // Convert ASCII to integer

        int result = multiply(num1, num2);
        char buffer[50];
        sprintf(buffer, "Product: %d\r\n", result);
        usart_putstr(buffer);

        _delay_ms(5000); // Delay for some time
    }
}


// Returns a int of two numbers multiplied 
int multiply(int a, int b) {
    return a * b; // Returns the product of a and b
}
```
