import board
from kmk.kmk_keyboard import KMKKeyboard
from kmk.keys import KC
from kmk.modules.layers import Layers
from kmk.modules.macros import Macros

# Matrix scanner import (try common name; KMK builds may expose `matrix` scanner)
try:
    from kmk.scanners import matrix
    Matrix = matrix.Matrix
except Exception:
    # Fallback import path for some KMK layouts
    try:
        from kmk.scanners.matrix import Matrix
    except Exception:
        Matrix = None

keyboard = KMKKeyboard()

# Modules
layers = Layers()
macros = Macros()
keyboard.modules.append(layers)
keyboard.modules.append(macros)

# Pin mappings (confirmed)
COL_PINS = [board.GP26, board.GP27, board.GP28, board.GP29]
ROW_PINS = [board.GP6, board.GP7, board.GP0, board.GP1]

# NeoPixel
NEOPIXEL_PIN = board.GP3
NEOPIXEL_COUNT = 2

# Encoder pins
ENC_A = board.GP4
ENC_B = board.GP2
ENC_BTN = board.GP10

# Configure matrix scanner if available
if Matrix:
    keyboard.matrix = Matrix(
        row_pins=ROW_PINS,
        col_pins=COL_PINS,
        diode_orientation=matrix.DiodeOrientation.COL2ROW,
    )
else:
    # If Matrix scanner isn't available in this KMK build, try the legacy attributes
    keyboard.col_pins = COL_PINS
    keyboard.row_pins = ROW_PINS

# Example keymap: two layers (layer 0 and layer 1). Must match 4x4 matrix size (16).
# We'll leave one position as KC.NO (unused). Keys are laid out row-major.
keyboard.keymap = [
    # Layer 0: Function keys (F1-F12, etc.)
    [
        KC.ESC, KC.F1, KC.F2, KC.F3,
        KC.F4,  KC.F5, KC.F6, KC.F7,
        KC.F8,  KC.F9, KC.F10, KC.F11,
        KC.F12, KC.PSCREEN, KC.INSERT, KC.NO,
    ],
    # Layer 1: Numbers and symbols
    [
        KC.GRAVE, KC.ONE, KC.TWO, KC.THREE,
        KC.FOUR, KC.FIVE, KC.SIX, KC.SEVEN,
        KC.EIGHT, KC.NINE, KC.ZERO, KC.MINUS,
        KC.EQUAL, KC.BACKSPACE, KC.TAB, KC.NO,
    ],
]

# NeoPixel initialization (attempt to import `neopixel`)
pixels = None
BASE_COLOR = (0, 16, 0)  # Green base color

try:
    import neopixel
    pixels = neopixel.NeoPixel(NEOPIXEL_PIN, NEOPIXEL_COUNT, brightness=0.2, auto_write=True)
    pixels.fill(BASE_COLOR)
except Exception:
    pixels = None

# Volume tracking (local state). We keep an internal level so we can map it
# to NeoPixel brightness. The encoder will still send HID volume up/down
# via `enc.keymap` below; we rely on the encoder module to send those keys.
VOLUME_LEVEL = 5
MAX_VOLUME_LEVEL = 10

def _set_pixels_by_volume(level):
    """Scale the BASE_COLOR by level/MAX_VOLUME_LEVEL and write to pixels."""
    if pixels is None:
        return
    level = max(0, min(MAX_VOLUME_LEVEL, level))
    scale = level / float(MAX_VOLUME_LEVEL) if MAX_VOLUME_LEVEL else 0
    r, g, b = BASE_COLOR
    pixels.fill((int(r * scale), int(g * scale), int(b * scale)))

# Layer change blink feedback
def _blink_pixels_for_layer(layer_num):
    """Blink LEDs to indicate layer change. Layer 0: 1 blink, Layer 1: 2 blinks."""
    if pixels is None:
        return
    try:
        import time
        blink_count = 1 if layer_num == 0 else 2
        for _ in range(blink_count):
            pixels.fill((255, 255, 255))  # White blink
            time.sleep(0.15)
            # Return to volume-based color
            _set_pixels_by_volume(VOLUME_LEVEL)
            time.sleep(0.1)
    except Exception:
        pass

# initialize pixel brightness according to starting volume
_set_pixels_by_volume(VOLUME_LEVEL)

# Encoder setup (optional)
try:
    from kmk.modules.encoder import Encoder

    enc = Encoder(ENC_A, ENC_B, pin_btn=ENC_BTN, value_when_pressed=False)
    # Map rotation: (ccw_action, cw_action) - this sends HID volume keys
    enc.keymap = ((KC.VOLD, KC.VOLU),)

    # Encoder callback: handles rotation and button events.
    # Rotation updates the local VOLUME_LEVEL and scales NeoPixels.
    # Button press toggles between Layer 0 and Layer 1.
    def _enc_event(evt):
        global VOLUME_LEVEL
        try:
            # rotation event (some implementations pass an int delta)
            if isinstance(evt, int) or isinstance(evt, float):
                delta = 1 if evt > 0 else -1
                VOLUME_LEVEL = max(0, min(MAX_VOLUME_LEVEL, VOLUME_LEVEL + delta))
                _set_pixels_by_volume(VOLUME_LEVEL)
                return
            # button event (pressed True/False)
            if isinstance(evt, bool):
                if evt:
                    layers.toggle(1)
                    # Determine current layer after toggle and blink feedback
                    try:
                        current_layer = layers.current_layer
                    except Exception:
                        current_layer = 0
                    _blink_pixels_for_layer(current_layer)
                return
        except Exception:
            # Fallback: ignore if callback signature differs
            return

    enc.callback = _enc_event
    keyboard.modules.append(enc)
except Exception:
    # Encoder module not available in this build — ignore for now
    pass

# Example macros: attach a simple macro to a key (optional)
# Uncomment or modify to add text-based macros
# keyboard.keymap[0][2] = KC.MACRO("Hello from Rudra's macropad!")

if __name__ == '__main__':
    keyboard.go()
