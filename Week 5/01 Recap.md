## Key Registers: UCSR0A, UCSR0B, and UCSR0C

When dealing with Arduino Uno, three important registers you'll encounter are UCSR0A, UCSR0B, and UCSR0C. These are part of the USART (Universal Synchronous and Asynchronous serial Receiver and Transmitter) interface, which is crucial for serial communication.

### UCSR0A: USART Control and Status Register A

- **Purpose**: UCSR0A is used to control various USART features and monitor their status.
- **Key Bits**:
  - RXC0: USART Receive Complete
  - TXC0: USART Transmit Complete
  - UDRE0: USART Data Register Empty
  - FE0: Frame Error
  - DOR0: Data OverRun
  - UPE0: USART Parity Error
  - U2X0: Double the USART Transmission Speed
  - MPCM0: Multi-processor Communication Mode

### UCSR0B: USART Control and Status Register B

- **Purpose**: UCSR0B is used for controlling the USART's Transmit and Receive capabilities.
- **Key Bits**:
  - RXCIE0: RX Complete Interrupt Enable
  - TXCIE0: TX Complete Interrupt Enable
  - UDRIE0: USART Data Register Empty Interrupt Enable
  - RXEN0: Receiver Enable
  - TXEN0: Transmitter Enable
  - UCSZ02: Character Size
  - RXB80: Receive Data Bit 8
  - TXB80: Transmit Data Bit 8

### UCSR0C: USART Control and Status Register C

- **Purpose**: UCSR0C provides control over framing features like stop bits, parity mode, and character size.
- **Key Bits**:
  - UMSEL01 and UMSEL00: USART Mode Select
  - UPM01 and UPM00: Parity Mode
  - USBS0: Stop Bit Select
  - UCSZ01 and UCSZ00: Character Size
  - UCPOL0: Clock Polarity



## UBRR0L and UBRR0H: USART Baud Rate Registers

The baud rate of your USART is controlled by two registers: UBRR0L (USART Baud Rate Register Low) and UBRR0H (USART Baud Rate Register High). These registers together set the baud rate for the USART communication.

### Purpose

- **UBRR0L and UBRR0H**: These registers are combined to set the baud rate for the USART. UBRR0L contains the lower 8 bits, and UBRR0H contains the upper 4 bits.

### Calculating Baud Rate

The baud rate for the USART is set based on the clock frequency of the microcontroller and the desired baud rate for communication. The formula to calculate the value to load into the UBRR0 registers is:

$$ UBRR0 = \frac{F_{OSC}}{16 \times \text{BAUD}} - 1 $$

Where:
- \( F_{OSC} \) is the clock frequency (in Hz).
- BAUD is the desired baud rate.

For example, if you have a 16 MHz clock and you want to set a baud rate of 9600, the calculation would be:

$$ UBRR0 = \frac{16,000,000}{16 \times 9600} - 1 \approx 103.17 $$

So, you would set the UBRR0H and UBRR0L registers to have the combined value of 103 (rounded).


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

