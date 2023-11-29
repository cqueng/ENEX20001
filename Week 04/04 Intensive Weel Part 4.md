# Reading Analog Input and Transmitting Over USART with AVR/Standard C Libraries

## Introduction
- Overview of interfacing an ADC and USART with AVR microcontrollers.
- Application in real-world scenarios.

## Setup and Read ADC
- **Function `ADC_setup`**: Configures the ADC for reading an analog input.
  - `ADMUX` and `ADCSRA` settings for reference voltage, input channel, and clock prescaler.
- **Function `read_ADC`**: Reads a value from the ADC.
  - Waits for the conversion to complete and returns the ADC value.

### ADC Code Snippet
```c
void ADC_setup (void){
    ADMUX |= (1<<REFS0)|(1<<MUX0);
    ADCSRA |= (1<<ADEN)|(1<<ADPS2)|(1<<ADPS1)|(1<<ADPS0);
}

uint16_t read_ADC (void){
    ADCSRA |= (1<<ADSC);
    while ((ADCSRA & (1<<ADSC)) == 0) {};
    return (ADC);
}
```

**Note - You must specify the Analog Pin being measured. You can get this information from the analog section of the datasheet**

1. **Change the Analog Input Pin:**
   - The Arduino Uno has multiple analog input pins (A0, A1, A2, etc.).
   - To read from a different analog pin, you set the `ADMUX` register accordingly.
   - Each analog pin corresponds to a specific setting of the MUX bits in the ADMUX register.
   - For example:
     - To select A0, you set `ADMUX` to `(1<<REFS0)`.
     - To select A1, set `ADMUX` to `(1<<REFS0)|(1<<MUX0)`.
     - For A2, set `ADMUX` to `(1<<REFS0)|(1<<MUX1)`.
   - The `REFS0` bit sets the reference voltage to AVcc (5V).

2. **Converting ADC Value to Voltage:**
   - The Arduino Uno has a 10-bit ADC, so the ADC values range from 0 to 1023.
   - To convert an ADC value to a voltage, you use the formula:
     \[ \text{Voltage} = \left( \text{ADC Value} \times \text{Reference Voltage} \right) / \text{ADC Resolution} \]
   - Assuming a reference voltage of 5V, the formula becomes:
     \[ \text{Voltage} = \left( \text{ADC Value} \times 5V \right) / 1024 \]
   - This will give you the voltage corresponding to the ADC value.


## Setup and Use USART for Communication
- **Function `usart_setup`**: Configures the USART for communication.
  - Settings for baud rate, frame format, and enabling transmitter and receiver.
- **Function `usart_TX`**: Transmits a single character over USART.
- **Function `usart_putstr`**: Transmits a string over USART.

### USART Code Snippet
```c
void usart_setup(void) {
    UCSR0B = (1<<RXEN0)|(1<<TXEN0);
    UCSR0C = (1<<UCSZ01)|(1<<UCSZ00);
    UBRR0H = 0;
    UBRR0L = 103;
}

void usart_TX(unsigned char TXData){
    while((UCSR0A & (1 << UDRE0)) == 0) {};
    UDR0 = TXData;
}

void usart_putstr(char *data){
    while(*data){
        usart_TX(*data++);
    }
}
```

## Main Program
- **Reading ADC Value**: The main loop repeatedly reads the ADC value.
- **Transmitting ADC Value**: Converts the ADC reading to a string and transmits it over USART.

### Main Program Snippet
```c
int main (void){
    usart_setup();
    ADC_setup();
    char adcStr[10];
    uint16_t adcValue;
  
    while(1){
        adcValue = read_ADC();
        itoa(adcValue, adcStr, 10);
        usart_putstr("ADC Value:");
        usart_putstr(adcStr);
        usart_putstr("\n");
        _delay_ms(100);
    }
    return(0);
}
```

# Implementing PWM Output with AVR/Standard C Libraries

## Introduction to PWM
- Explanation of PWM and its applications in controlling devices like LEDs and motors.

## Setting Up PWM
- **Function `PWM_setup`**: Configures Timer/Counter for PWM output.
  - Settings for the Timer/Counter control registers `TCCR0A` and `TCCR0B`.
- **PWM Output Pin**: Defines which pin will output the PWM signal (in this case, PD5).

### PWM Setup Code Snippet
```c
#include <avr/io.h>
#include <util/delay.h>

void PWM_setup() {
    TCCR0A |= (1<<COM0B1);
    TCCR0A |= (1<<WGM00) | (1<<WGM01);
    TCCR0B = (1<<CS00) | (1<<CS01);
}

int main() {
    DDRD |= (1<<PD5); // Set PD5 as output for PWM
    PWM_setup();

    while(1) {
        OCR0B = 128; // Set PWM duty cycle (50%)
    }
    return 0;
}
```

#### Explanation
- **Timer/Counter Configuration**: The setup involves configuring the Timer/Counter control registers for Fast PWM mode and setting the prescaler.
- **Duty Cycle**: `OCR0B = 128;` sets the duty cycle to 50%. Changing this value adjusts the brightness of the LED or the speed of a motor.

## Main Program
- The main loop maintains the PWM output with a 50% duty cycle. This value can be adjusted to see different levels of brightness in an LED or different speeds in a motor.

## Experimenting with PWM
- Experiment by changing `OCR0B` values to see different PWM effects.



# Interfacing with an Ultrasonic Sensor using AVR/Standard C Libraries

**Please note this example is to be used in conjuction with the workshop slides**

## Introduction to Ultrasonic Sensors
- Overview of how ultrasonic sensors work.
- Common applications in distance measurement and obstacle detection.

## Setting Up the Ultrasonic Sensor
- **Pin Configuration**: Defining trigger and echo pins.
- **Initialisation**: Setting up I/O and Timer for the ultrasonic sensor.
  
### Initialisation Code Snippet
```c
#define ULTRASONIC_TRIGGER_PIN PD3
#define ULTRASONIC_ECHO_PIN PD2

void initIO() {
    DDRD |= (1 << ULTRASONIC_TRIGGER_PIN); // Set trigger pin as output
    DDRD &= ~(1 << ULTRASONIC_ECHO_PIN); // Set echo pin as input
}

void initTimer2() {
    // Timer2 setup code
}

void enableTimer2Interrupt() {
    // Enable Timer2 interrupt code
}

void disableTimer2Interrupt() {
    // Disable Timer2 interrupt code
}
```

## Handling Ultrasonic Sensor Data
- **Sending Trigger Signal**: Generating the trigger signal for the ultrasonic sensor.
- **Reading Echo Signal**: Using Timer Interrupt to measure the pulse width of the echo signal.

### Ultrasonic Sensor Handling Code Snippet
```c
ISR(TIMER2_COMPA_vect) {
    // Interrupt service routine for Timer2
}

void sendUltrasonicTrigger() {
    // Code to send trigger signal
}

int main() {
    // Main loop to repeatedly measure distance
}
```

#### Explanation
- **Timer Interrupt**: The Timer2 interrupt is used to accurately measure the time between the trigger and echo, which corresponds to the distance.
- **Distance Calculation**: The pulse width is used to calculate the distance to the nearest obstacle.

## Transmitting Distance Data Over USART
- **USART Setup**: Configuring USART for serial communication.
- **Transmitting Data**: Sending the calculated distance over USART.

### USART Communication Code Snippet
```c
void usart_setup() {
    // USART setup code
}

void usart_TX(unsigned char TXData) {
    // Code to transmit data over USART
}

void usart_putstr(char *data) {
    // Code to send strings over USART
}

int main() {
    // Using USART functions in the main loop
}
```
