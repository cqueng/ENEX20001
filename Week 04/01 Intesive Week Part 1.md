# Intesive Week Recap on the basics

## Introduction

This guide provides an introduction to programming the Arduino Uno microcontroller using AVR/Standard C libraries, offering a deeper insight into microcontroller programming.

## Basic Program Structure

### Data Types

- `int`: Integer, a whole number.
- `char`: Character, a single byte that represents a character in the ASCII table.
- `float`: Floating point, a number with a fractional part.
- `uint8_t`, `uint16_t`, etc.: Unsigned integers of specific bit sizes.

### Include Libraries

```c
#include <avr/io.h>
#include <util/delay.h>
```

### Main Function

```c
int main(void) {
    // Initial setup goes here
    // Main loop goes here
    return 0;
}
```

## Examples of basic structures 

### Digital Output with `PORTD`

Blink an LED on `PORTD0`.

```c
int main(void) {
    DDRD |= (1 << PD0); // Set PORTD0 as output

    while (1) {
        PORTD ^= (1 << PD0); // Toggle LED on PORTD0
        _delay_ms(500);
    }
    return 0;
}
```

### For Loop

Sequence LEDs on `PORTD0-3`.

```c
int main(void) {
    DDRD |= 0x0F; // Set PORTD0-3 as output

    while (1) {
        for (int i = 0; i < 4; i++) {
            PORTD |= (1 << i);
            _delay_ms(500);
            PORTD &= ~(1 << i);
        }
    }
    return 0;
}
```

### If Statements

Turn on an LED if a button is pressed.

```c
int main(void) {
    DDRD |= 0x0F; // Set PORTD0-3 as output

    while (1) {
        if (PIND & (1 << PD4)) {
            PORTD |= (1 << PD0);
        } else {
            PORTD &= ~(1 << PD0);
        }
    }
    return 0;
}
```

### While Loop

Keep an LED on while a button is pressed.

```c
int main(void) {
    DDRD |= 0x0F; // Set PORTD0-3 as output

    while (1) {
        while (PIND & (1 << PD4)) {
            PORTD |= (1 << PD0);
        }
        PORTD &= ~(1 << PD0);
    }
    return 0;
}
```

### Switch...Case Statement

Control LEDs based on a variable.

```c
int main(void) {
    DDRD |= 0x0F; // Set PORTD0-3 as output
    int controlVar = 2; // Manually change this variable to test

    while (1) {
        switch (controlVar) {
            case 1:
                PORTD = (1 << PD0);
                break;
            case 2:
                PORTD = (1 << PD1);
                break;
            case 3:
                PORTD = (1 << PD2);
                break;
            default:
                PORTD = 0;
                break;
        }
    }
    return 0;
}
```


### Subroutines (Functions) in C

#### Subroutine with No Return Value

- **Concept**: A function that performs a task but does not send any data back to the caller.
- **Example**:
  ```c
  #include <avr/io.h>

  // Function prototype
  void turnOnLED(void);

  int main(void) {
      DDRD |= 0x0F; // Set PORTD0-3 as output for LEDs

      while (1) {
          if (PIND & (1 << PD4)) {
              turnOnLED(); // Call subroutine
          }
      }
      return 0;
  }

  // Function definition
  void turnOnLED(void) {
      PORTD |= (1 << PD0); // Turn on LED at PORTD0
  }
  ```

#### Subroutine with a Return Value

- **Concept**: A function that performs a task and then returns a value to the caller.
- **Example**:
  ```c
  #include <avr/io.h>

  // Function prototype
  int readButtonState(void);

  int main(void) {
      DDRD |= 0x0F; // Set PORTD0-3 as output for LEDs

      while (1) {
          if (readButtonState()) {
              PORTD |= (1 << PD0); // Turn on LED if button is pressed
          } else {
              PORTD &= ~(1 << PD0); // Turn off LED otherwise
          }
      }
      return 0;
  }

  // Function definition
  int readButtonState(void) {
      return PIND & (1 << PD4); // Return the state of the button on PD4
  }
  ```

### Local and Global Variables in C

#### Global Variables

- **Concept**: Global variables are declared outside of all functions and can be accessed from anywhere in the program.
- **Example**:
  ```c
  #include <avr/io.h>

  int globalCounter = 0; // Global variable

  void incrementCounter(void) {
      globalCounter++; // Accessing global variable
  }

  int main(void) {
      while (1) {
          incrementCounter();
          // Use globalCounter here
      }
      return 0;
  }
  ```

#### Local Variables

- **Concept**: Local variables are declared within a function and can only be accessed within that function.
- **Example**:
  ```c
  #include <avr/io.h>

  void blinkLED(void) {
      int localCounter = 0; // Local variable
      while (localCounter < 5) {
          PORTD ^= (1 << PD0); // Toggle LED on PORTD0
          _delay_ms(500);
          localCounter++;
      }
  }

  int main(void) {
      DDRD |= (1 << PD0); // Set PORTD0 as output
      while (1) {
          blinkLED();
          // localCounter is not accessible here
      }
      return 0;
  }
  ```
