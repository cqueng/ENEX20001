# Logic Development for Beginners in Embedded Systems

Creating code can seem hard. but if you break it down, into blocks you will find the basic structures a lot easier, which in turn makes complex tasks achievable.

## Example 1: One Push Button, One LED

### Flowchart
- Description: Flowchart showing the logic of turning an LED on and off using a push button.
- Flowchart Steps:
  1. Start
  2. Read button state
  3. If button is pressed, turn LED on
  4. If button is released, turn LED off
  5. Repeat

### Pseudocode
```plaintext
START
LOOP FOREVER
    READ buttonState
    IF buttonState IS pressed
        SET LEDState TO on
    ELSE
        SET LEDState TO off
    ENDIF
    UPDATE LED with LEDState
END LOOP
END
```

### C Code
```c
#include <avr/io.h>

int main() {
    DDRD = 0xFF; // Set PortD as output for LED
    DDRB = 0x00; // Set PortB as input for button

    while(1) {
        if (PINB & 0x01) { // Check if button is pressed
            PORTD = 0xFF; // Turn on LED
        } else {
            PORTD = 0x00; // Turn off LED
        }
    }
}
```

## Example 2: One Push Button, Counter, and 3 LEDs

### Flowchart
- Description: Flowchart for a program where pressing a button increments a counter. Depending on the counter value, different combinations of three LEDs are turned on.
- Flowchart Steps:
  1. Start
  2. Initialize counter to 0
  3. Read button state
  4. If button is pressed, increment counter
  5. Based on counter value, turn on specific LEDs
  6. Repeat

### Pseudocode
```plaintext
START
SET counter TO 0
LOOP FOREVER
    READ buttonState
    IF buttonState IS pressed
        INCREMENT counter
        SWITCH counter
            CASE 1: TURN ON LED1
            CASE 2: TURN ON LED2
            CASE 3: TURN ON LED3
            DEFAULT: TURN OFF all LEDs
        ENDSWITCH
    ENDIF
END LOOP
END
```

### C Code
```c
#include <avr/io.h>

int main() {
    DDRD = 0xFF; // Set PortD as output for LEDs
    DDRB = 0x00; // Set PortB as input for button
    int counter = 0;

    while(1) {
        if (PINB & 0x01) { // Check if button is pressed
            counter++;
            switch (counter) {
                case 1:
                    PORTD = 0x01; // Turn on LED1
                    break;
                case 2:
                    PORTD = 0x02; // Turn on LED2
                    break;
                case 3:
                    PORTD = 0x04; // Turn on LED3
                    break;
                default:
                    PORTD = 0x00; // Turn off all LEDs
                    counter = 0;
                    break;
            }
        }
    }
}
```

## Example 3: Smart Traffic Light System

### Flowchart
- **Description**: A traffic light changes from green to red if a car is detected on the cross street.
- **Flowchart Steps**:
  1. Start
  2. Set main street light to green and cross street light to red
  3. Check car sensor on cross street
  4. If a car is detected, change main street light to red and cross street light to green
  5. Set a timer for the green light duration
  6. After the timer expires, switch lights back
  7. Repeat

### Pseudocode
```plaintext
START
SET mainStreetLight TO green
SET crossStreetLight TO red
LOOP FOREVER
    IF carDetected on crossStreet
        SET mainStreetLight TO red
        SET crossStreetLight TO green
        WAIT for greenLightDuration
        SET mainStreetLight TO green
        SET crossStreetLight TO red
    ENDIF
END LOOP
END
```

### C Code
```c
#include <avr/io.h>
#include <util/delay.h>

#define GREEN_LIGHT_DURATION 5000 // Duration of green light in milliseconds

int main() {
    DDRD = 0xFF; // Configure PortD for traffic lights
    DDRB = 0x00; // Configure PortB for car sensor

    while(1) {
        if (PINB & 0x01) { // Check if car is detected
            PORTD = 0x01; // Set main street light to red, cross street to green
            _delay_ms(GREEN_LIGHT_DURATION);
            PORTD = 0x02; // Set main street light to green, cross street to red
        }
    }
}
```

## Example 4: Vehicle Speed Control Based on Distance

### Flowchart
- **Description**: A vehicle automatically adjusts its speed based on the distance to the object in front.
- **Flowchart Steps**:
  1. Start
  2. Read distance sensor
  3. If distance is below a minimum threshold, decrease speed
  4. If distance is above a maximum threshold, increase speed
  5. Maintain current speed if within thresholds
  6. Repeat

### Pseudocode
```plaintext
START
LOOP FOREVER
    READ distance from distanceSensor
    IF distance < MIN_THRESHOLD
        DECREASE speed
    ELSE IF distance > MAX_THRESHOLD
        INCREASE speed
    ELSE
        MAINTAIN speed
    ENDIF
END LOOP
END
```

### C Code
```c
#include <avr/io.h>

#define MIN_THRESHOLD 10 // Minimum distance threshold
#define MAX_THRESHOLD 30 // Maximum distance threshold

int main() {
    DDRD = 0xFF; // Configure PortD for motor control
    int distance, speed = 0;

    while(1) {
        distance = readDistanceSensor(); // Assume function to read distance sensor

        if (distance < MIN_THRESHOLD) {
            speed--; // Decrease speed
        } else if (distance > MAX_THRESHOLD) {
            speed++; // Increase speed
        }
        updateMotorSpeed(speed); // Assume function to update motor speed
    }
}
```
