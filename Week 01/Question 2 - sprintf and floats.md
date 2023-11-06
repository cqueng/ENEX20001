## Question 2: Handling Floating-Point Numbers

### The Challenge

You might find that trying to print floating-point numbers (like 3.14) directly with `sprintf` using `%f` doesn't work as expected in Tinkercad or other AVR environments. That's because `sprintf` doesn't support floating-point formatting by default, to save space. Instead, we use a special function called `dtostrf` to turn the floating-point number into a string first.

### Pseudocode

Here's our plan:

1. Start USART communication.
2. Continuously:
   - Format an integer to a string and send it.
   - Try to format a float directly to a string (it won't work).
   - Convert a float to a string using `dtostrf`.
   - Send the formatted float string.
   - Wait for 5 seconds.

### Your Task

Modify the `main` function in the template code provided. Use `dtostrf` to handle floating-point numbers. What happens when we use sprintf directly with a float?

### Code Overview

Your code will look something like this:

```c
// ... [rest of the setup code]

int main(void) {
    // Initialize USART
    usart_setup();

    char buffer[50]; // Buffer to hold the formatted string

    while (1) {
        // Format the string using sprintf instead of printf
        sprintf(buffer, "Integer: %d\r\n", 10);
        usart_putstr(buffer);
		    // Using sprintf in tinkercad with a float direct
      	sprintf(buffer, "Float Direct: %f\n", 3.14);
      	usart_putstr(buffer);
        // For float, you may need to convert it to a string first
        // Since sprintf might not support %f, use dtostrf
        char floatBuffer[10];
        dtostrf(3.14, 6, 2, floatBuffer); // Convert float to string with 2 decimal places
        sprintf(buffer, "Float: %s\r\n", floatBuffer);
        usart_putstr(buffer);

        // Wait for 5 seconds before sending the strings again
        _delay_ms(5000);
    }
}
```

