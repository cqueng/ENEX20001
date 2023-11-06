# Tutorial 1 - ENEX20001: Introduction to AVR Programming and Serial Communication

## Welcome to the World of Microcontrollers!

In this first week, we will learn the basics of embedded programming using AVR libraries, which are different from the Arduino libraries you might be more familiar with. Unlike Arduino's `Serial` functions, AVR programming requires us to understand and set up the underlying hardware features directly. But don't worry, we'll guide you through it!

## Understanding the Task

Your mission is to create a simple program that introduces you to the microcontroller by saying "Hello, World!" This task will help you learn about serial communication, which is how a microcontroller talks to the computer.

### The Basics

In the world of AVR, we use USART (Universal Synchronous and Asynchronous serial Receiver and Transmitter) for serial communication. It's a way for devices to exchange data, one bit at a time.

### Pseudocode Overview

Before diving into the C code, let's think about what we want to do in simple steps:

1. Start the USART communication.
2. Keep doing the following without stopping:
   - Send "Hello, CQU!" using USART.
   - Send your name using USART.
   - Wait for a short time (5 seconds).

### Writing the Code

You'll be using a few functions to control USART communication. We've provided a template with these functions that you can use to start writing your program.

Here's a quick explanation of what each part does:

- `usart_setup`: Prepares the USART to send and receive messages.
- `usart_rx`: Waits for incoming data and reads it.
- `usart_tx`: Sends a single character.
- `usart_putstr`: Sends a whole string of characters.

### Code Template

Below is the boilerplate code you'll start with. It includes everything you need to set up and send messages over USART.

```c
#include <avr/io.h>
#include <util/delay.h>
#include <stdio.h>

// Function prototypes
void usart_setup(void);
unsigned char usart_rx(void);
void usart_tx(unsigned char data);
void usart_putstr(char *str);

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

Then to the bottom of this we can add our main loop. Note we will go into more detail about the above boiler plate code in the coming weeks. But to get you started and able to debug, we think its worth having from the start.
```c
// Main program
int main(void) {
  // Your code will go here...
  return 0;
}
```

### Your Task

Add your code to the `main` function. You'll need to call `usart_setup` to start the USART and then use `usart_putstr` to send messages. Replace "Your Name" with your actual name.

### How to Use This Code

1. Insert the template into your IDE or Tinker CAD.
2. Modify the `main` function as instructed.
3. Compile your code to make sure it translates correctly for the microcontroller.
4. Upload the compiled code to your microcontroller.
5. Watch the serial monitor for your messages!

## Conclusion

By completing this tutorial, you'll understand the basics of setting up and using USART for serial communication in AVR programming. It's a first step towards mastering the control of microcontrollers for more complex tasks. Keep experimenting and exploring, and remember to have fun!
