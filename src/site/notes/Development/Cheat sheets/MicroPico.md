---
{"dg-publish":true,"dg-path":"Cheat sheets/MicroPico.md","permalink":"/cheat-sheets/micro-pico/","tags":["language/python","tech/tufty"]}
---


# Using Thonny

- Go to *View > Files* to browse the file system
- *View > Plotter* to view `print`ed numbers on a graph

# Using MicroPico for VSCode

- `manualComDevice` setting should look something like `/dev/cu.usbmodem101`, can get it from Thonny

# Change screen brightness

```python
from picographics import PicoGraphics, DISPLAY_TUFTY_2040
display = PicoGraphics(display=DISPLAY_TUFTY_2040)

display.set_backlight(0.5) # 0.35 - 1.0
```

## Auto adjust brightness

- call this in a loop

```python
from machine import ADC, Pin

lux_pwr = Pin(27, Pin.OUT)
lux_pwr.value(1)

lux = ADC(26)

def adjust_brightness():
    reading = lux.read_u16()
    brightness = max(0.5, min(1.0, reading / 1000))
    display.set_backlight(brightness)
```

# Get display size

```python
WIDTH, HEIGHT = display.get_bounds()
```

# Drawing

## Fill or clear the screen

```python
from picographics import PicoGraphics, DISPLAY_TUFTY_2040
display = PicoGraphics(display=DISPLAY_TUFTY_2040)

BLACK = display.create_pen(0, 0, 0) # or any other color
display.set_pen(BLACK)
display.clear()
```

## Text

```python
from picographics import PicoGraphics, DISPLAY_TUFTY_2040
display = PicoGraphics(display=DISPLAY_TUFTY_2040)

WHITE = display.create_pen(255, 255, 255) # RGB color
display.set_pen(WHITE)
display.set_font('bitmap8')

# first two numbers are X, Y of top left of text
# third number is when the text starts wrapping (320 is the screen width, so this will span the screen - use -1 to disable text wrapping)
# fourth number is text size
display.text("Hello Tufty", 0, 0, 320, 4)

display.update() # needs to be called to flush the screen buffer
```

### Fonts

- Bitmap fonts:
    - `bitmap6`: all caps
    - `bitmap8`: mixed case with more symbols
    - `bitmap14_outline`: outlined

- Vector fonts:
    - `sans`
    - `gothic`
    - `cursive`
    - `serif_italic`
    - `serif`
- Vector fonts are aligned vertically to their midline
    - at scale 1 the top edge of uppercase letters is 10px above the midline, and the baseline is 10px below
- Use `display.set_thickness(n)` to adjust the thickness of vector fonts

## Shapes

```python
display.line(x1, y1, x2, y2, thickness)
display.circle(x, y, radius)
display.rectangle(x, y, w, h)
display.triangle(x1, y1, x2, y2, x3, y3)

display.polygon([
  (0, 10),
  (20, 10),
  (20, 0),
  (30, 20),
  (20, 30),
  (20, 20),
  (0, 20),
])

display.pixel(x, y)
display.pixel_span(x, y, length) # horizontal span of pixels
```

## Images

> [!tip]
> The Tufty's screen resolution is 320x240. JPGs must **not** be progressive (uncheck in Advanced Options in GIMP when exporting)

```python
from picographics import PicoGraphics, DISPLAY_TUFTY_2040
from jpegdec import JPEG, JPEG_SCALE_FULL

display = PicoGraphics(display=DISPLAY_TUFTY_2040)
j = JPEG(display)

j.open_file("jorts.jpg")
# X, Y, scale, dither
j.decode(0, 0, JPEG_SCALE_FULL, dither=True)

# you can draw multiple images
j.open_file("maru.jpg")
# all arguments are optional
j.decode(0, 100)

# don't forget this!
display.update()
```

- available scales are `JPEG_SCALE_FULL`, `JPEG_SCALE_HALF`, `JPEG_SCALE_QUARTER` or `JPEG_SCALE_EIGHTH`: changes the size of the image

## QR codes

```python
import qrcode

WIDTH, HEIGHT = display.get_bounds()

QR_TEXT = "https://linktr.ee/fennyflametail"
QR_BACKGROUND = display.create_pen(46, 46, 46)
QR_FILL = display.create_pen(255, 255, 255)
QR_SQUARES = display.create_pen(0, 0, 0)

def measure_qr_code(size, code):
    w, h = code.get_size()
    module_size = int(size / w)
    return module_size * w, module_size


def draw_qr_code(ox, oy, size, code):
    size, module_size = measure_qr_code(size, code)
    display.set_pen(QR_FILL)
    display.rectangle(ox, oy, size, size)
    display.set_pen(QR_SQUARES)
    for x in range(size):
        for y in range(size):
            if code.get_module(x, y):
                display.rectangle(ox + x * module_size, oy + y * module_size, module_size, module_size)

def show_qr():
    display.set_pen(QR_BACKGROUND)
    display.clear()

    code = qrcode.QRCode()
    code.set_text(QR_TEXT)

    size, module_size = measure_qr_code(HEIGHT, code)
    left = int((WIDTH // 2) - (size // 2))
    top = int((HEIGHT // 2) - (size // 2))
    draw_qr_code(left, top, HEIGHT, code)
```

# Detect button presses

- Buttons are updated every 100ms

```python
from pimoroni import Button

button_a = Button(7, invert=False)
button_b = Button(8, invert=False)
button_c = Button(9, invert=False)
button_up = Button(22, invert=False)
button_down = Button(6, invert=False)

while True:
    if button_a.is_pressed:
        # do stuff
```

# See also

- [[Tech/Clipped/Getting Started with Tufty 2040\|Getting Started with Tufty 2040]]
- [[Tech/Clipped/Getting Started with Plasma 2040 (modified)\|Getting Started with Plasma 2040 (modified)]]
- [[Tech/Clipped/Pico Graphics README\|Pico Graphics README]]
- [[Development/Cheat sheets/Python\|Python]]
