Below is a general overview of what has been discussed for those who missed out. these are approaches to moving the robot around - note I highly recommend not using delays and using the non-blocking code example.

```c
#include <avr/io.h>
#include <util/delay.h>

#define DELAY_MS 10
#define FOR_CYCLES 10
#define ARRAY_SIZE 3

// Assuming these functions are defined elsewhere
extern void turn_right();
extern void move_fwd();
extern void stop();
extern int line_detected(); // Function to check if a line is detected

int main() {
    int distance[ARRAY_SIZE] = {25, 100, 100}; // Example distances
    int object_side = 0; // Initialise with 0
    int object_dis = 9999; // Create value over max distance
    int mode = 0; // Initialise with 0

    // Find the smallest value in the distance array
    for (int i = 0; i < ARRAY_SIZE; i++) {
        if (distance[i] < object_dis) {
            object_side = i;
            object_dis = distance[i];
        }
    }

    // The robot will sit there if no object is detected within 30cm
    // This means it will move until no longer turn and stop
    if (object_dis < 30) {
        // Object too close, move
        if (object_side == 0 && mode == 0) {
            // Object in front
            turn_right();
            mode = 1;
        }

        if (mode == 1) {
            for (int i = 0; i < FOR_CYCLES; i++) { //these for loops give me the chance to at least check if somethings happened since starting
                _delay_ms(DELAY_MS);
                if (line_detected() == 1) {
                    mode = 20;
                    break;
                }
                if (i > (FOR_CYCLES - 1)) {
                    mode = 2; // Left this inside so that if it breaks it doesn't overwrite the above
                }
            }
        }

        if (mode == 2) {
            stop();
            move_fwd();
            mode = 3;
        }

        if (mode == 3) {
            for (int i = 0; i < FOR_CYCLES; i++) {
                _delay_ms(DELAY_MS);
                if (line_detected() == 1) {
                    mode = 20;
                    break;
                }
                if (i > (FOR_CYCLES - 1)) {
                    mode = 4; // Left this inside so that if it breaks, it doesn't overwrite the above
                }
            }
        }
    }
    // you might want a else mode = 0 here? again, it's up to you and your flow.

    // Add additional actions based on the mode values here

    return 0;
}
```

### Non-blocking code could look like:
See week 4 to see timer setups for this to work

```
#define IDLE 0
#define TURNING_LEFT 1
#define MOVING_FORWARD 2

volatile uint32_t milliseconds = 0;
volatile int currentState = IDLE;
volatile uint32_t actionStartTime = 0;

#define TURN_DURATION_MS 1000  // Time it takes to turn left
#define MOVE_DURATION_MS 2000  // Time to move forward after turning

extern void turn_left();  // Function to turn the robot left
extern void move_fwd();   // Function to move the robot forward
extern void stop_robot(); // Function to stop the robot

void start_turning_left() {
    turn_left();
    actionStartTime = millis();
    currentState = TURNING_LEFT;
}

void start_moving_forward() {
    move_fwd();
    actionStartTime = millis();
    currentState = MOVING_FORWARD;
}

void update_robot_state() {
    if (currentState == TURNING_LEFT && (millis() - actionStartTime) >= TURN_DURATION_MS) {
        start_moving_forward();
    } else if (currentState == MOVING_FORWARD && (millis() - actionStartTime) >= MOVE_DURATION_MS) {
        stop_robot();
        currentState = IDLE;
    }
}

int main(void) {
    init_millis();

    while (1) {
        if (currentState == IDLE) {
            start_turning_left();
        }
        update_robot_state();

        // Other non-blocking code can go here
    }
}


```

Note these are not complete codes. You would need to fill in the blanks. Rather, these are general concepts you could use to suit your needs.
