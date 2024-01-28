Creating a millisecond counter using standard AVR libraries and C can be achieved by using one of the timers in the ATmega328P (the microcontroller on an Arduino Uno). The `_delay_ms()` function is blocking, meaning it halts the execution of other code while the delay is happening, which could indeed interfere with other functionalities like PWM.

Here's a simple subroutine using Timer 1 to create a millisecond counter - note this is a basic setup only:

1. **Define Global Variables:**
   - Define a volatile global variable to keep track of the milliseconds.

2. **Initialise Timer 1:**
   - Set Timer 1 to CTC (Clear Timer on Compare Match) mode.
   - Set the compare value to match the clock frequency and prescaler to get a 1ms interval.

3. **Enable Timer 1 Compare Interrupt:**
   - Enable the interrupt to trigger on compare match.

4. **Write the ISR (Interrupt Service Routine):**
   - Increment the millisecond counter in the ISR.

5. **Create a Function to Return the Milliseconds:**
   - Return the value of the millisecond counter.

Putting it together:

```c
#include <avr/io.h>
#include <avr/interrupt.h>

volatile uint32_t milliseconds = 0;

void init_millis() {
    // Set up Timer 1
    TCCR1A = 0; // Set entire TCCR1A register to 0
    TCCR1B = 0; // Same for TCCR1B
    // Set compare match register for 1ms increments
    OCR1A = 16000; // = 16000000 / (1 * 1000) - 1 (must be <65536)
    // Turn on CTC mode
    TCCR1B |= (1 << WGM12);
    // Set CS10 bit for 1 prescaler
    TCCR1B |= (1 << CS10); 
    // Enable timer compare interrupt
    TIMSK1 |= (1 << OCIE1A);
    // Enable global interrupts
    sei();
}

ISR(TIMER1_COMPA_vect) {
    milliseconds++; // Increment milliseconds count on each interrupt
}

uint32_t millis() {
    return milliseconds;
}

int main(void) {
    init_millis();

    while(1) {
        // Your main loop code here
        // Use millis() to get the current milliseconds count
    }
}
```


### Using this in code
Based on this, we can start to use this to run tasks or check things based on the millis value i.e:
```
volatile uint32_t milliseconds = 0;

...
Setup as we just covered
...

uint32_t millis() {
    return milliseconds;
}

void runTaskEvery10Seconds() {
    // This is where you put the code that needs to run every 10 seconds i.e.
    PORTD ^= (1 << PD0); // Toggle LED on PORTD0

}

int main(void) {
    init_millis();

    uint32_t lastRun = 0; // Variable to store the last time the task was run

    while(1) {
        if ((millis() - lastRun) >= 10000) { // Check if 10 seconds have passed
            runTaskEvery10Seconds(); // Run your specific task
            lastRun = millis(); // Update the last run time
        }
        // Other non-blocking code can be placed here
    }
}
```

#### Another example based on a basic code that used two delays

Consider our basic on-off LED code, which used two `_delay_ms(500);` This code blocks PWM and ultra sonic reading. This creates issues when you're developing a responsive robot.
```
    while (1) {
         PORTD |= (1 << i);
         _delay_ms(500);
         PORTD &= ~(1 << i);
         _delay_ms(500);
        }
```

To do this, we need to create a few registers to keep track of our time and states, in addition to the global variable `volatile uint32_t milliseconds = 0;`.
```
   uint32_t lastTime = 0;
   int currentLed = 0;
   int state = 0; // State 0 for LED off, 1 for LED on
```

After setting up our timer1, we can use `millis()` to track the milliseconds:
```
uint32_t millis() {
    return milliseconds;
}
```

Inside of our while loop, we wrap the command around the millis so every 500ms it will execute the command once, then skip until the next 500ms and so on.
```
    while (1) {
        if ((millis() - lastTime) >= 500) { // Check if 500 ms have passed
            if (state == 0) {
                PORTD &= ~(1 << currentLed); // Turn off the current LED
                currentLed = (currentLed + 1) % 4; // Move to the next LED
                state = 1; // Set the state to turn the LED on next
            } else {
                PORTD |= (1 << currentLed); // Turn on the current LED
                state = 0; // Set the state to turn the LED off next
            }
            lastTime = millis(); // Update the last time the state changed
        }
        // Other non-blocking code can be placed here
    }

```
You can add multiples of these inside your while loop. However, you will need to create unique `lastTime` variables/states and so on.

For example, let's include two separate non-blocking functions:

1. **Pressing Button 2 while the LED is on will turn it off immediately.**
2. **Pressing Button 1 again while the LED is on will reset the timer, keeping the LED on for another 4 seconds.**

Here's the updated code, we are using the same functions as above:

```c
#include <avr/io.h>
#include <avr/interrupt.h>

#define TOGGLE_LED_PIN PD4
#define CONTROLLED_LED_PIN PD5
#define BUTTON1_PIN PD2
#define BUTTON2_PIN PD3

volatile uint32_t milliseconds = 0;

void init_millis() {
    // Timer1 initialisation code for 1ms increment...
    // ...
}

ISR(TIMER1_COMPA_vect) {
    milliseconds++;
}

void setup_pins() {
    // Set LED pins as output
    DDRD |= (1 << TOGGLE_LED_PIN) | (1 << CONTROLLED_LED_PIN);

    // Set button pins as input with pull-up enabled
    DDRD &= ~((1 << BUTTON1_PIN) | (1 << BUTTON2_PIN));
    PORTD |= (1 << BUTTON1_PIN) | (1 << BUTTON2_PIN);
}

void toggle_led_every_2_seconds() {
    static uint32_t lastToggleTime = 0;
    if ((millis() - lastToggleTime) >= 2000) {
        PORTD ^= (1 << TOGGLE_LED_PIN); // Toggle LED
        lastToggleTime = millis();
    }
}

void control_led_with_buttons() {
    static uint32_t ledOnTime = 0;
    static uint8_t ledState = 0;

    // Turn on LED or reset timer when Button 1 is pressed
    if (!(PIND & (1 << BUTTON1_PIN))) {
        PORTD |= (1 << CONTROLLED_LED_PIN);
        ledOnTime = millis();
        ledState = 1;
    }

    // Turn off LED early if Button 2 is pressed, or after 4 seconds
    if (ledState && ((millis() - ledOnTime) >= 4000 || !(PIND & (1 << BUTTON2_PIN)))) {
        PORTD &= ~(1 << CONTROLLED_LED_PIN);
        ledState = 0;
    }
}

int main(void) {
    init_millis();
    setup_pins();

    while(1) {
        toggle_led_every_2_seconds();  // Non-blocking LED toggle
        control_led_with_buttons();    // Non-blocking LED control with buttons
        // Other non-blocking tasks can be added here
    }
}
```

From this we can see:
- **`toggle_led_every_2_seconds`:** Continues to toggle an LED on PD4 every 2 seconds.
- **`control_led_with_buttons`:**
    - When Button 1 (PD2) is pressed, the LED on PD5 turns on. If the LED is already on, pressing Button 1 again resets the 4-second timer.
    - Pressing Button 2 (PD3) while the LED is on turns it off immediately, overriding the 4-second duration.
