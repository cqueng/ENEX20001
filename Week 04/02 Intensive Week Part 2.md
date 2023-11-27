## Core Programming Strategies: States, Events, and Interrupts in Microcontroller Programming

### State Machine Programming

#### Description
- **Concept**: A state machine has a set of states and transitions based on inputs/conditions. It's like a flowchart for decision-making.
- **Pros**: Easy to debug and scale; clear logic flow.
- **Cons**: Can become complex with many states; not always efficient for handling asynchronous events.

#### Example
```c
#include <avr/io.h>
#include <util/delay.h>

int main(void) {
    DDRD |= 0x0F; // Set PORTD0-3 as output for LEDs
    int currentState = 0;

    while (1) {
        switch (currentState) {
            case 0:
                PORTD = (1 << PD0); // Turn on LED0
                _delay_ms(1000);
                currentState = 1;
                break;
            case 1:
                PORTD = (1 << PD1); // Turn on LED1
                _delay_ms(1000);
                currentState = 2;
                break;
            case 2:
                PORTD = (1 << PD2); // Turn on LED2
                _delay_ms(1000);
                currentState = 3;
                break;
            case 3:
                PORTD = (1 << PD3); // Turn on LED3
                _delay_ms(1000);
                currentState = 0;
                break;
        }
    }
    return 0;
}
```
- **`int currentState = 0;`**: This variable keeps track of the current state. We start at state 0.
- **Switch Statement**: The `switch` statement checks the `currentState` and executes the code block corresponding to that state.
- **State Actions**: In each case (state), a different LED is turned on, and a delay of 1 second is added before moving to the next state.
- **State Transition**: After executing the code for each state, `currentState` is updated to the next state. After state 3, it loops back to state 0.


### Event-Driven Programming

#### Description
- **Concept**: The program flow is determined by events (e.g., sensor inputs, timer ticks). Execution of code blocks occurs in response to these events.
- **Pros**: Efficient, responsive; good for UI/UX, sensor data handling.
- **Cons**: Can be hard to debug; program flow is not linear.

#### Example
```c
int main(void) {
    DDRD |= 0x0F; // Set PORTD0-3 as output for LEDs

    while (1) {
        if (PIND & (1 << PD4)) { // Event: button press
            PORTD |= (1 << PD0); // Action: turn on LED
        } else {
            PORTD &= ~(1 << PD0); // Action: turn off LED
        }
    }
    return 0;
}
```

### Interrupt-Driven Programming

#### Description
- **Concept**: The main program runs continuously, and certain functions (interrupt service routines) are executed when specific events (interrupts) occur.
- **Pros**: Real-time response; efficient for handling specific events.
- **Cons**: Complexity in managing multiple interrupts; potential for priority inversion.

#### Example
```c
#include <avr/interrupt.h>

ISR(INT0_vect) {
    PORTD ^= (1 << PD0); // Toggle LED on interrupt
}

int main(void) {
    DDRD |= 0x0F; // Set PORTD0-3 as output for LEDs
    EIMSK |= (1 << INT0); // Enable external interrupt INT0
    sei(); // Enable global interrupts

    while (1) {
        // Main loop
    }
    return 0;
}
```







### Additional concepts
The above details basic concepts and techniques. however, these can be expanded. the below is one such example that expands the state machine concept to include the use of enum.

### State Machine Programming With enum Example

```c
typedef enum { STATE_OFF, STATE_ON } state_t;

int main(void) {
    state_t state = STATE_OFF;
    DDRD |= 0x0F; // Set PORTD0-3 as output for LEDs

    while (1) {
        switch (state) {
            case STATE_OFF:
                if (PIND & (1 << PD4)) state = STATE_ON;
                break;
            case STATE_ON:
                PORTD |= (1 << PD0);
                if (!(PIND & (1 << PD4))) state = STATE_OFF;
                break;
        }
    }
    return 0;
}
```

#### Understanding `typedef enum`, `state_t`, `STATE_OFF`, and `STATE_ON`

##### What is `typedef enum`?

- **`enum`**: Short for enumeration. In C programming, an `enum` is a way to give more meaningful names to numbers. It's like creating a custom list of options.
- **`typedef`**: This is used to give a new name to an existing type. It's like creating a nickname or alias for a type to make it easier to use.

##### What is `state_t`?

- **`state_t`**: Here, `state_t` is a new name given to our `enum`. Instead of using the standard `enum` type, we use `state_t` to refer to our specific set of states. It's like saying, "Whenever I talk about `state_t`, I'm referring to this particular group of states I've defined."

##### Why `STATE_OFF` and `STATE_ON`?

- **`STATE_OFF` and `STATE_ON`**: These are the names we give to the states in our `enum`. `STATE_OFF` might represent a state where the program is not actively doing something, like an LED being turned off. `STATE_ON` could represent an active state, like an LED being turned on. It’s a way to label these states so that they're easier to remember and use in our code.

##### Example Explained

```c
typedef enum { STATE_OFF, STATE_ON } state_t;
```

- **What This Line Does**: This line creates a new type (`state_t`) that can be either `STATE_OFF` or `STATE_ON`. Whenever you create a variable of type `state_t`, it can only hold one of these two values.

##### Using `state_t` in Code

- **How to Use**: When you write your program, you can create variables of type `state_t` and assign them the values `STATE_OFF` or `STATE_ON`. The program can then check these variables to decide what to do next. It's like having a signpost in your code that tells the program which path to follow.

