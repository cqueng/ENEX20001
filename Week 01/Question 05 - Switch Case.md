## Question 5: Arithmetic Operations with Switch-Case

### What We're Doing

We'll read two numbers and an arithmetic operation from the user. Using a switch-case statement, the program will perform the selected operation on the numbers and send the result back over USART.

At this point, you will notice if we need a float to print it's not much harder; we just need to convert it first, i.e:
```c
dtostrf(result, 6, 2, floatBuffer); // Convert result to string
sprintf(buffer, "Result: %s\r\n", floatBuffer);
```

### Pseudocode

Follow these steps:

1. Set up USART communication.
2. Continuously:
   - Prompt for and get two numbers from USART.
   - Prompt for and get an arithmetic operation from USART.
   - Use switch-case to perform the operation on the numbers.
   - Send the result back over USART.
   - Pause for 5 seconds.

### Your Task

Inside the `while` loop of the `main` function, implement the logic to perform arithmetic operations using switch-case statements.

### Code Structure

Your `main` function will look something like this:

```c
int main(void) {
    // Initialize USART
    usart_setup();

    while (1) {
        // Read two numbers and an operation from USART
      usart_putstr("Enter a number: \r\n");  
      float num1 = (float)(usart_rx() - '0');  // Convert ASCII to integer and then to float
      usart_putstr("Enter a number: \r\n");  
      float num2 = (float)(usart_rx() - '0');  // Convert ASCII to integer and then to float
      usart_putstr("Enter either + - * or /:\r\n");
      char operation = usart_rx();             // Read the operation directly

        float result;
        char buffer[50];
        char floatBuffer[10];

        // Perform the operation using switch-case
        switch (operation) {
            case '+':
                result = num1 + num2;
                break;
            case '-':
                result = num1 - num2;
                break;
            case '*':
                result = num1 * num2;
                break;
            case '/':
                if (num2 != 0) {
                    result = num1 / num2;
                } else {
                    usart_putstr("Error: Division by zero.\r\n");
                    continue;
                }
                break;
            default:
                usart_putstr("Error: Invalid operation.\r\n");
                continue;
        }

        // Convert result to string and send
        dtostrf(result, 6, 2, floatBuffer); // Convert result to string
        sprintf(buffer, "Result: %s\r\n", floatBuffer);
        usart_putstr(buffer);

        // Delay for 5 seconds
        _delay_ms(5000);
    }
}

```
