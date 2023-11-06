## A recap of different functions, operators, variables etc..

```c
#include <avr/io.h>
#include <util/delay.h>
#include <stdio.h>

// Function prototypes
void usart_setup(void);
unsigned char usart_rx(void);
void usart_tx(unsigned char data);
void usart_putstr(char *str);
void printNumber(int num);
int add(int a, int b);


int main(void) {
    // USART initialization
    usart_setup();

    // If statement example
    int x = 10;
    if (x > 5) {
        usart_putstr("X is greater than 5.\r\n");
    }

    // While loop example
    int y = 0;
    while (y < 5) {
        printNumber(y);
        y++;
    }

    // For loop example
    for (int i = 0; i < 5; i++) {
        printNumber(i);
    }

    // Array example
    int numbers[5] = {1, 2, 3, 4, 5};
    for (int i = 0; i < 5; i++) {
        printNumber(numbers[i]);
    }

    // Function call example
    int sum = add(10, 20);
    printNumber(sum);

    // Pointer example
    int *ptr = &x;
    printNumber(*ptr);

    // Struct example
    struct Point {
        int x;
        int y;
    };
    struct Point p1 = {0, 0};
    printNumber(p1.x);
    printNumber(p1.y);

    // Main loop
    while (1) {
        // Do other stuff
        // ...
        _delay_ms(5000); // Wait for 5 seconds before repeating the actions
    }
}

// Function implementations
void printNumber(int num) {
    char buffer[10];
    snprintf(buffer, sizeof(buffer), "%d\r\n", num);
    usart_putstr(buffer);
}

int add(int a, int b) {
    return a + b;
}

// Initialize USART for communication
void usart_setup(void) {
    // Set baud rate
    UBRR0H = 0;
    UBRR0L = 103;  // for 9600 bps with 16MHz clock

    // Enable receiver and transmitter
    UCSR0B = (1 << RXEN0) | (1 << TXEN0);

    // Set frame format: 8 data bits, 1 stop bit, no parity
    UCSR0C = (1 << UCSZ01) | (1 << UCSZ00);
}
// Recieve data
unsigned char usart_rx(void) {
  //do nothing until data is received and is ready to be read from UDR0; wait for USART RX Complete flag
  while ((UCSR0A & (1 << RXC0)) == 0) {};
  //when flag is set read data from USART UDR0 register and return it
  return (UDR0);
}

// Transmit a single character via USART
void usart_tx(unsigned char data) {
    // Wait for empty transmit buffer
    while (!(UCSR0A & (1 << UDRE0)));

    // Put data into buffer, sends the data
    UDR0 = data;
}

// Print a string via USART
void usart_putstr(char *data) {
    while (*data) {
        usart_tx(*data++);
    }
}
```
