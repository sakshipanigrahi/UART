# Lab 4 — Lab 3 LED Blink Program, Controlled over UART

Board: TI TM4C123GH6PM (Tiva C LaunchPad) + EduARM4 add-on board

## 1. What This Program Does (in plain words)


This is the same LED blink program from Lab 3 — same LED, same 7-segment display, same two buttons — with one thing added: you can now also control it by **typing on the laptop and pressing Enter**, instead of only pressing the physical buttons.

The laptop and the board talk to each other over a wire called **UART**. Think of it like a walkie-talkie: the laptop can send letters to the board, and the board can send letters back so you can read them on the laptop screen.

| If you... | Then... |
|---|---|
| Press **SW1** alone (while running) | LED blinks faster or slower (8 speed levels) |
| Press **SW2** alone (while running) | LED changes colour (8 colours, one of them "off") |
| Press **SW1 AND SW2 together** | LED freezes (pause) or starts again (run) |
| Type **`rate`** and press Enter | Same as pressing SW1 |
| Type **`color`** or **`colour`** and press Enter | Same as pressing SW2 |
| Type **`t`** and press Enter | Same as pressing both buttons together |
| Type **`h`** and press Enter | Shows the list of commands again |

The display shows the same status as Lab 3:

- Digit 1 (leftmost) → running or paused
- Digit 2 → current colour number (0–7)
- Digit 3 → current speed number (1–8)
- Digit 4 (rightmost) → the letter `S`

## 2. Hardware Map

| Port Pin | Job |
|---|---|
| PF1, PF2, PF3 | Drive Red, Blue, Green parts of the LED |
| PF4 | SW1 button input |
| PF0 | SW2 button input |
| PB0–PB7 | Turn on/off the 7 segments of whichever digit is lit |
| PA4–PA7 | Choose which one of the 4 digit modules is lit |
| PA0 | UART receive — the board listens to the laptop here |
| PA1 | UART transmit — the board talks to the laptop here |

## 3. How UART Was Set Up

UART is a pair of wires used for sending letters back and forth, one letter at a time. Both sides — the laptop and the board — must agree on how fast to talk, or the letters come out as garbage. This agreed speed is called the **baud rate**, and here it is set to **115200**.

Setting it up meant four things:

1. **Turn on the power** for the UART hardware and for Port A (PA0, PA1 are the two UART wires).
2. **Tell Port A that PA0 and PA1 belong to UART**, not to normal on/off pins.
3. **Set the speed** (115200) using two numbers the hardware needs: a whole-number part and a fraction part, worked out from the board's 16 MHz clock.
4. **Turn UART on**, with sending and receiving both switched on.

Once this is done, the board can send text with a "write" function (one letter goes out) and read text with a "read" function (one letter comes in), the same way you would print something or read a key press.

## 4. The Menu Shown on the Console

When the board is reset, it prints this on the laptop screen:

```
============================
           LAB 4
============================
SW1       : SPEED
SW2       : COLOUR
SW1+SW2   : RUN / PAUSE

UART COMMANDS (type then Enter):
color / colour = next colour
rate           = next speed
t              = BLINK / CONTINUOUS
h              = HELP
============================
```

This tells you what the buttons do, and what words to type. You type a full word and press Enter — the board waits for the whole word, not single letters, so typing "rate" only does something once you finish typing it and press Enter.

### What each command does, with what the board replies

**`color` or `colour`** — moves to the next colour (after 7, it goes back to 0)

```
colour
Colour incremented
```

**`rate`** — moves to the next speed (after 8, it goes back to 1)

```
rate
Rate incremented
```

**`t`** — switches between blinking and staying on steady

```
t
LED: CONTINUOUS ON
```

Typing `t` again switches it back:

```
t
LED: BLINKING
```

**`h`** — shows the command list again

```
h
============================
LAB 4 COMMANDS (type then Enter)
============================
color / colour = next colour
rate           = next speed
t              = BLINK/CONTINUOUS
h              = HELP
============================
```

**Anything else you type** — nothing changes, and the board tells you it didn't understand

```
abc
Unknown command
```

## 5. How to Run It

1. Build the program and load it onto the board, the same way you did for Lab 3.
2. Connect to the board's COM port.
3. Set the speed to 115200, with 8 data bits, no parity, 1 stop bit.
4. Press reset on the board. The menu above will appear on the screen.
5. You can now use the buttons, or type the words above, to control the LED.

## 6. Things Worth Knowing

- Typing `color`/`colour` or `rate` works even while the LED is paused (steady on). The physical buttons SW1/SW2 only work while it's blinking.
- Commands must be typed in lowercase exactly as shown — `Rate` or `RATE` will not work.
- If you press Backspace while typing, it is not removed properly, so the word will not match and you'll get "Unknown command".
