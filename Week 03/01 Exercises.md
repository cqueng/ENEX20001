At this point we have covered the basic header and `int main(void)` setup. If you are unsure at this point please revert back to weeks 1 and 2 for clarification.

# Exercise 1: Simplifying I/O Configuration

This task is to be done in conjuction with the tutial slide from Week 3.

### Original Setup
Initially, you may have been taught to configure each pin separately like this:

```c
DDRX |= (1 << PX7);  // Set PX7 as an output
DDRX &= ~(1 << PX6); // Set PX6 as an input
```

### Combined Configuration
Now, let's combine these into one line:

```c
DDRX = (DDRX | (1 << PX7)) & ~(1 << PX6);
```

### Breaking Down the Line

1. `DDRX | (1 << PX7)`: Here we're ensuring that pin PX7 on PORTX is set as an output.
   - `|` is the bitwise OR operator and is used to set a particular bit to `1`.
   - `1 << PX7` means we shift a `1` to the position of PX7, which sets only the PX7 bit to `1` and keeps the rest as they are.
   
2. `~(1 << PX6)`: This creates a bitmask to set PX6 as an input.
   - `~` is the bitwise NOT operator, which flips all the bits; `1` becomes `0` and `0` becomes `1`.
   - `1 << PX6` positions a `1` at the bit for PX6. After the NOT operation, this bit becomes `0`, while the rest become `1`.
   
3. `& ~(1 << PX6)`: Now we apply this mask to `DDRX`.
   - `&` is the bitwise AND operator. It will combine `DDRX` and our bitmask in such a way that only the bit for PX6 can be set to `0`.
   - By combining `DDRX | (1 << PX7)` with `& ~(1 << PX6)`, we ensure that PX7 is set as an output (bit is `1`) and PX6 as an input (bit is `0`).

### Reading Inputs and Toggling Outputs
To check if a button is pressed (input is high), we use `PINX & (1 << PX6)`. If the button is not pressed (input is low), we use `!(PINX & (1 << PX6))`. To turn on an LED (output high), we set the corresponding pin high with `PORTX |= (1 << PX7)`. Conversely, to turn off the LED (output low), we clear the pin with `PORTX &= ~(1 << PX7)`.


# Exercise 2: Managing Multiple Inputs and Outputs

In this exercise, we'll handle two buttons and two LEDs. We'll configure the buttons as inputs and LEDs as outputs, then write code to turn on an LED when its corresponding button is pressed.

### Grouping Inputs and Outputs
For readability, group the I/O configurations together. Recall from the previous example how to set pins as outputs and inputs:

```c
// Set PX7 and PX4 as outputs for the LEDs
DDRX |= (1 << PX7) | (1 << PX4);
// Set PX6 and PX5 as inputs for the buttons
DDRX &= ~((1 << PX6) | (1 << PX5));
```

### Explanation of Configuration Lines

- `DDRX |= (1 << PX7) | (1 << PX4);` is setting pins PX7 and PX4 as outputs by using the OR operator `|` to set their specific bits to `1`.
- `DDRX &= ~((1 << PX6) | (1 << PX5));` sets pins PX6 and PX5 as inputs. The NOT operator `~` inverts the bits, and the AND operator `&` ensures only the bits for PX6 and PX5 are changed to `0`.

### Reading Button States

Inside the infinite loop, we continuously read the button states:

```c
// Read the state of the buttons
int button1 = PINX & (1 << PX6);
int button2 = PINX & (1 << PX5);
```

- We define two integer variables `button1` and `button2` that hold the state of the buttons.
- `PINX & (1 << PX6)` reads the current state of button1. If the button is pressed, this will be a non-zero value.
- `PINX & (1 << PX5)` does the same for button2.

### Using Conditional Logic to Control LEDs

We then use a series of `if` and `else if` statements to control the LEDs:

```c
// Turn on LED 1 if button 1 is pressed
if (!button1 && button2) {
    PORTX |= (1 << PX7);  // Turn on LED 1
    PORTX &= ~(1 << PX4); // Turn off LED 2
}
// Turn on LED 2 if button 2 is pressed
else if (button1 && !button2) {
    PORTX &= ~(1 << PX7); // Turn off LED 1
    PORTX |= (1 << PX4);  // Turn on LED 2
}
// Turn on both LEDs if both buttons are pressed
else if (!button1 && !button2) {
    PORTX |= (1 << PX7) | (1 << PX4); // Turn on both LEDs
}
// Turn off both LEDs if no buttons are pressed
else {
    PORTX &= ~((1 << PX7) | (1 << PX4)); // Turn off both LEDs
}
```

- `if (!button1 && button2)` checks if button1 is NOT pressed and button2 IS pressed, then sets PX7 high to turn on LED1 and clears PX4 to turn off LED2.
- `else if (button1 && !button2)` is the opposite; if button1 IS pressed and button2 is NOT pressed, then clear PX7 to turn off LED1 and set PX4 high to turn on LED2.
- `else if (!button1 && !button2)` checks if BOTH buttons are NOT pressed. If so, it sets both PX7 and PX4 high to turn both LEDs on.
- The `else` case handles when neither button is pressed; it clears both PX7 and PX4, turning both LEDs off.

This logical structure allows you to control each LED based on the combination of button presses. Remember to place the button state reading and the conditional logic inside an infinite loop so that it continuously checks and responds to button presses.


# Exercise 3: Button States and Switch Cases

This exercise continues from the setup in Exercise 2. We will handle the input from two buttons using a `switch` statement to control the output to two LEDs.

## Combining Button States

First, we need to create a single variable that holds the combined state of both buttons. Since the buttons are wired to ground (pull-down configuration), a pressed button reads as LOW (0), and an unpressed button as HIGH (1). We will invert this logic to make the code more intuitive:

```c
uint8_t buttonState = (!(PINX & (1 << PX6)) << 1) | (!(PINX & (1 << PX5)));
```

### Breakdown of Button State Logic

- `!(PINX & (1 << PX6))`: This checks if button 1 (connected to PX6) is pressed. The `!` operator inverts the logic so that a press (which reads as `0`) becomes `1` in our variable.
- `!(PINX & (1 << PX5))`: Similarly, this checks if button 2 (connected to PX5) is pressed.
- `<< 1`: This shifts the result for button 1 left by one bit, giving us space to represent button 2's state in the next lower bit.
- The OR operator `|` combines the two button states into a single byte, `buttonState`.

## Using a Switch Statement

The `switch` statement allows us to execute different code blocks based on `buttonState`:

```c
switch (buttonState) {
    // Different cases will be here
}
```

If you are unsure scroll to the bottom of this document for a detailed explanation of how the states work.

### Explanation of the Switch Statement

- We pass `buttonState` to the `switch` statement, which routes the program flow to the matching `case` based on the value of `buttonState`.

## Cases for Button Presses

In the `switch` statement, each `case` corresponds to a different combination of button presses:

```c
case (1 << 1): // Only Button 1 is pressed
    // Turn on LED 1, Turn off LED 2
    break;

case (1 << 0): // Only Button 2 is pressed
    // Turn off LED 1, Turn on LED 2
    break;

case (1 << 1) | (1 << 0): // Both Button 1 and 2 are pressed
    // Turn on both LEDs
    break;

default: // No buttons are pressed
    // Turn off both LEDs
    break;
```

### Breakdown of Cases

- `case (1 << 1)`: Matches when button 1's state is `1` (pressed) and button 2 is not pressed.
- `case (1 << 0)`: Matches when button 2's state is `1` (pressed) and button 1 is not pressed.
- `case (1 << 1) | (1 << 0)`: Matches when both buttons are pressed.
- `default`: Used when none of the cases match, which means neither button is pressed.

Each `case` executes its block of code and `break` ensures that the program exits the switch after a match is found and executed.

Understood, let's revise the snippet to use the generic `PORTX` and pin identifiers like `PX6` and `PX5`. This way, it aligns with the generalized approach we've been using:

---

### Alternative Approach for `switch` Statement

Here's an alternative way to structure the `switch` statement in Exercise 3, using direct port bit identifiers `(1 << PX6)` and `(1 << PX5)`:

```c
// Adjust the logic to directly use the pin numbers in the case labels
switch ((PINX & (1 << PX6)) | (PINX & (1 << PX5))) {
    case (1 << PX6): // Only Button 1 (connected to PX6) pressed
        // Turn on LED 1 (connected to PX7), Turn off LED 2 (connected to PX4)
        break;
    case (1 << PX5): // Only Button 2 (connected to PX5) pressed
        // Turn off LED 1 (connected to PX7), Turn on LED 2 (connected to PX4)
        break;
    case (1 << PX6) | (1 << PX5): // Both buttons pressed
        // Turn on both LEDs (connected to PX7 and PX4)
        break;
    default: // No buttons pressed
        // Turn off both LEDs
        break;
}
```

In this version:

- The `switch` statement evaluates the direct button states from `PINX`.
- `case (1 << PX6)`: Activates if only the button connected to PX6 is pressed.
- `case (1 << PX5)`: Activates if only the button connected to PX5 is pressed.
- `case (1 << PX6) | (1 << PX5)`: Activates if both buttons are pressed.
- The `default` case handles when no buttons are pressed.


#### In fact, you could further simplify all of this and just call the simple numeric representation of the combined button states, where each number corresponds to a specific combination of button presses.

Here's how the `switch` statement would look with this approach:

```c
switch (buttonState) {
    case 1: // Button 1 (PX6) pressed
        // Turn on LED 1 (PX7), Turn off LED 2 (PX4)
        break;

    case 2: // Button 2 (PX5) pressed
        // Turn off LED 1 (PX7), Turn on LED 2 (PX4)
        break;

    case 3: // Both buttons (PX6 and PX5) pressed
        // Turn on both LEDs (PX7 and PX4)
        break;

    default: // No buttons pressed
        // Turn off both LEDs
        break;
}
```
Where:
- `case 1`: Corresponds to `buttonState` being `1`, which happens when only Button 1 (PX6) is pressed.
- `case 2`: Corresponds to `buttonState` being `2`, which happens when only Button 2 (PX5) is pressed.
- `case 3`: Corresponds to `buttonState` being `3`, which happens when both buttons are pressed.
- `default`: Handles the scenario when no buttons are pressed (`buttonState` is `0`).


# Understanding `buttonState` Variable Construction

In the context of controlling two buttons, we represent their states using two separate bits in the `buttonState` variable. This approach simplifies understanding the combined state of both buttons:

- **Bit 0:** Represents the state of Button 1 (connected to PX6).
- **Bit 1:** Represents the state of Button 2 (connected to PX5).

These bits combine to represent the state of both buttons in a binary form:

- **00:** Neither button is pressed.
- **01:** Only Button 1 is pressed.
- **10:** Only Button 2 is pressed.
- **11:** Both buttons are pressed.

### How `buttonState` is Constructed

1. **Read Button States:** We check the state of each button using `PINX & (1 << PXx)`. Since the buttons are in a pull-down configuration, we invert the result to make `0` represent a press and `1` represent no press.

2. **Adjust the Bits:** We then position these inverted results in the `buttonState` variable:
   - Button 1's inverted result is placed in the least significant bit of `buttonState`.
   - Button 2's inverted result is shifted left by one bit and then placed in `buttonState`. This separates the states of the two buttons.

3. **Combine Button States:** The bitwise OR operator (`|`) combines these two bits into the single `buttonState` variable.

### Practical Example

- **Both Buttons Not Pressed:** `PINX` reads HIGH for both, resulting in `00` after inversion and shifting, so `buttonState` is `0`.
- **Button 1 Pressed:** `PINX` reads LOW for PX6, inversion gives `01`, and `buttonState` becomes `1`.
- **Button 2 Pressed:** `PINX` reads LOW for PX5, inversion and shifting give `10`, making `buttonState` `2`.
- **Both Pressed:** `PINX` reads LOW for both, inversion and combining give `11`, so `buttonState` is `3`.

The `switch-case` in the code then uses `buttonState` to determine which LEDs to control based on these combinations.

