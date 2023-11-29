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
