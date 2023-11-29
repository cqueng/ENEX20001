# Reading Analog Input, PWM, and Ultrasonic Sensors.

# Analog
## Setup and Read ADC
- **Function `ADC_setup`**: Configures the ADC for reading an analog input.
  - `ADMUX` and `ADCSRA` settings for reference voltage, input channel, and clock prescaler.
- **Function `read_ADC`**: Reads a value from the ADC.
  - Waits for the conversion to complete and returns the ADC value.

### ADC Code Snippet
```c
void ADC_setup (void){
    // Configure the ADMUX register:
    // - (1<<REFS0) sets the reference voltage to AVcc (typically 5V).
    // - (1<<MUX0) selects the ADC1 channel (corresponding to analog input A1 on Arduino Uno).
    ADMUX |= (1<<REFS0)|(1<<MUX0);

    // Configure the ADCSRA register:
    // - (1<<ADEN) enables the ADC.
    // - (1<<ADPS2)|(1<<ADPS1)|(1<<ADPS0) sets the ADC prescaler to 128, 
    //   which determines the speed of the ADC conversion.
    ADCSRA |= (1<<ADEN)|(1<<ADPS2)|(1<<ADPS1)|(1<<ADPS0);
}

uint16_t read_ADC (void){
    // Start the ADC conversion by setting the ADSC bit in ADCSRA.
    ADCSRA |= (1<<ADSC);

    // Wait in a loop until the ADSC bit has been cleared, indicating 
    // the ADC conversion is complete.
    while ((ADCSRA & (1<<ADSC)) == 0) {};

    // Return the ADC conversion result. The ADC register holds the 
    // result of the last conversion.
    return (ADC);
}

```

**Note - You must specify the Analog Pin being measured. You can get this information from the analog section of the datasheet**

1. **Change the Analog Input Pin:**
   - The Arduino Uno has multiple analog input pins (A0, A1, A2, etc.).
   - To read from a different analog pin, you set the `ADMUX` register accordingly.
   - Each analog pin corresponds to a specific setting of the MUX bits in the ADMUX register.
   - For example:
     - To select A0, you set `ADMUX` to `(1<<REFS0)` or `(1<<REFS0)| 0`.
     - To select A1, set `ADMUX` to `(1<<REFS0)|(1<<MUX0)` or `(1<<REFS0)| 1`.
     - For A2, set `ADMUX` to `(1<<REFS0)|(1<<MUX1)` and so on...
   - The `REFS0` bit sets the reference voltage to AVcc (5V).

2. **Converting ADC Value to Voltage:**
   - The Arduino Uno has a 10-bit ADC, so the ADC values range from 0 to 1023.
   - To convert an ADC value to a voltage, you use the formula:
     ```
     Voltage = (ADC Value × Reference Voltage) / ADC Resolution
     ```
   - Assuming a reference voltage of 5V, the formula becomes:
     ```
     Voltage = (ADC Value × 5V) / 1024
     ```
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

# PWM

## Setting Up PWM
- **Function `PWM_setup`**: Configures Timer/Counter for PWM output.
  - Settings for the Timer/Counter control registers `TCCR0A` and `TCCR0B`.
- **PWM Output Pin**: Defines which pin will output the PWM signal (in this case, PD5).

### PWM Setup Code Snippet
```c
#include <avr/io.h>
#include <util/delay.h>

void PWM_setup() {
    // Configure Timer 0 for PWM on pin PD5 (OC0B)
    // COM0B1: Set to enable PWM on OC0B (PD5)
    // WGM00 and WGM01: Set to enable Fast PWM mode
    TCCR0A |= (1<<COM0B1) | (1<<WGM00) | (1<<WGM01);

    // Set the prescaler for Timer 0 to divide the clock by 64
    // CS00 and CS01: Set these to divide the input clock by 64, 
    // balancing frequency and resolution
    TCCR0B = (1<<CS00) | (1<<CS01);
}

int main() {
    DDRD |= (1<<PD5); // Set PD5 as output for PWM
    // Uncomment line below if using TinkerCAD
    //init();
    PWM_setup();

    while(1) {
        // Set the PWM duty cycle for PD5
        // OCR0B is the Output Compare Register for Timer 0 channel B
        // A value of 128 represents a 50% duty cycle (128/256 ≈ 50%)
        // because Timer 0 is 8-bit (ranges from 0 to 255)
        OCR0B = 128;
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
- Experiment by changing `OCR0B` values to see different PWM effects. you can also set PD6 to work with timer 0 at the same time by adding it `TCCR0A` by including `(1<<COM0A1)`. then you can set the duty cycle by using `OCR0A`

# Ultrasonic Sensor

**Please note this example is to be used in conjunction with the workshop slides**
We will need the following headers and variables:
```
#include <avr/io.h>
#include <util/delay.h>
#include <avr/interrupt.h>
#include <stdlib.h>

#define ULTRASONIC_TRIGGER_PIN PD4
#define ULTRASONIC_ECHO_PIN PD2

volatile uint8_t echo_received = 0;
volatile uint16_t echo_signal_time_out = 0;

volatile uint16_t pulse_width = 0;
volatile uint16_t count = 0;
uint8_t reload = 100;

```
  
### Initialisation Code Snippet
```c
void initIO(){
    // Set the ultrasonic trigger pin as output
    DDRD |= (1 << ULTRASONIC_TRIGGER_PIN);
    // Set the ultrasonic echo pin as input
    DDRD &= ~(1 << ULTRASONIC_ECHO_PIN);
}

void initTimer2(){
    cli(); // Disable global interrupts
    TCCR0B = 0; // Stop Timer 0 to avoid conflict
    // Set Timer 2 in CTC (Clear Timer on Compare Match) mode
    // WGM21: Waveform Generation Mode bit
    TCCR2A = (1<<WGM21);
    // Set Timer 2 prescaler to 8 (CS21: Clock Select)
    // This controls the timer speed
    TCCR2B = (1<<CS21);
    sei(); // Enable global interrupts
}

void enableTimer2Interrupt(){
    cli(); // Disable global interrupts
    // Set the compare match value for Timer 2
    OCR2A = reload;
    // Enable Timer 2 Compare Match A Interrupt
    TIMSK2 = (1<<OCIE2A);
    sei(); // Enable global interrupts
}

void disableTimer2Interrupt(){
    cli(); // Disable global interrupts
    // Disable Timer 2 Compare Match A Interrupt
    TIMSK2 &= ~(1<<OCIE2A);
    sei(); // Enable global interrupts
}
```

## Handling Ultrasonic Sensor Data
- **Sending Trigger Signal**: Generating the trigger signal for the ultrasonic sensor.
- **Reading Echo Signal**: Using Timer Interrupt to measure the pulse width of the echo signal.

### Ultrasonic Sensor Handling Code Snippet
```c
ISR(TIMER2_COMPA_vect){
    // This ISR is called when Timer 2 matches OCR2A value
    echo_signal_time_out++;
    // Reload the compare match value
    OCR2A = reload;
    // Check if echo is received
    if(PIND & (1 << ULTRASONIC_ECHO_PIN)){
        echo_received = 1;
        count++;
    }
    // If echo has ended, calculate the pulse width
    if(((PIND &(1 << ULTRASONIC_ECHO_PIN))== 0) && echo_received){
        pulse_width = count;
        count = 0;
        echo_signal_time_out = 0;
    }
}

void sendUltrasonicTrigger(){
    // Send a 10ms pulse to the ultrasonic trigger pin
    PORTD |= (1 << ULTRASONIC_TRIGGER_PIN);
    _delay_ms(10);
    PORTD &= ~(1 << ULTRASONIC_TRIGGER_PIN);
}

int main() {
    // Main loop to repeatedly measure distance
    // Initial setup
    usart_setup();
    initTimer2();
    initIO();
    while(1)  {

        // Trigger the ultrasonic sensor
        sendUltrasonicTrigger();
        enableTimer2Interrupt();
        
        // Wait for the echo or timeout
        while (pulse_width == 0 && echo_signal_time_out < 400);
        
        // Calculate distance based on pulse width
        float distance = ((pulse_width)*50)/58;

        // Do something with it i.e. print to serial

        // Reset variables for next reading
        pulse_width = 0;
        echo_received = 0;
        disableTimer2Interrupt();

        // wait a bit before next reading
        _delay_ms(500);
}
}
```

