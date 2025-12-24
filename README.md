# IOT-Attack-Detection-
IOT Attack Detection By using ML in Real-Time 
pip install pillow

from PIL import Image, ImageDraw, ImageFont, ImageFilter
import math

# ----- Config -----
TEXT = "IOT Attack Detection"
WIDTH, HEIGHT = 1200, 300
FRAMES = 40
FPS = 20   # playback hint
FONT_PATH = "Roboto-Bold.ttf"  # तुमचा bold TTF फॉन्ट पाथ
FONT_SIZE = 120
BG_COLOR = (10, 10, 12)        # dark background
GLOW = True

def rgb_cycle(t):
    # t in [0,1] -> smooth RGB cycle
    r = int(127 * (1 + math.sin(2*math.pi*(t + 0/3))))
    g = int(127 * (1 + math.sin(2*math.pi*(t + 1/3))))
    b = int(127 * (1 + math.sin(2*math.pi*(t + 2/3))))
    return (r, g, b)

def make_frame(i):
    t = i / FRAMES
    img = Image.new("RGB", (WIDTH, HEIGHT), BG_COLOR)
    draw = ImageDraw.Draw(img)

    # Load font
    font = ImageFont.truetype(FONT_PATH, FONT_SIZE)

    # Measure text and center it
    bbox = draw.textbbox((0,0), TEXT, font=font)
    tw, th = bbox[2]-bbox[0], bbox[3]-bbox[1]
    x = (WIDTH - tw) // 2
    y = (HEIGHT - th) // 2

    # Animated color
    color = rgb_cycle(t)

    # Optional glow layer
    if GLOW:
        glow = Image.new("RGBA", (WIDTH, HEIGHT), (0,0,0,0))
        gdraw = ImageDraw.Draw(glow)
        gdraw.text((x, y), TEXT, font=font, fill=color + (255,))
        glow = glow.filter(ImageFilter.GaussianBlur(radius=8))
        img = Image.alpha_composite(img.convert("RGBA"), glow).convert("RGB")

    # Main crisp text
    draw = ImageDraw.Draw(img)
    draw.text((x, y), TEXT, font=font, fill=color)

    # Subtle bottom highlight bar (optional)
    bar = Image.new("RGB", (WIDTH, 6), (max(color[0]-40,0), max(color[1]-40,0), max(color[2]-40,0)))
    img.paste(bar, (0, HEIGHT-12))

    return img

frames = [make_frame(i) for i in range(FRAMES)]

# Save animated GIF
frames[0].save(
    "assets/iot_title.gif",
    save_all=True,
    append_images=frames[1:],
    duration=int(1000/FPS),   # ms per frame
    loop=0,
    disposal=2
)

print("Saved -> assets/iot_title.gif")

python make_iot_title_gif.py
git add assets/iot_title.gif
git commit -m "Add animated RGB title for README"
git push

![IOT Attack Detection](assets/iot_title.gif)
