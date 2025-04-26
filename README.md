from machine import Pin, TouchPad, PWM
import neopixel
import time
import random

n = 0

pixy = neopixel.NeoPixel(Pin(2), 16)
t_pin = TouchPad(Pin(12))
serv_pin = Pin(4, Pin.OUT)
serv = PWM(serv_pin, freq=50)
serv2_pin = Pin(14, Pin.OUT)
serv2 = PWM(serv2_pin, freq=50)

th = 300

buzzer = PWM(Pin(15), freq=440, duty=0)


notes = {
    "B3": 247,
    "C4": 261,
    "D4": 294,
    "E4": 329,
    "F4": 349,
    "G4": 392,
    "A4": 440,
    "B4": 493,
    "C5": 523,
    "R": 0  # Rest
}

perry_theme = [
    ("E4", 0.3), ("F4", 0.1), ("G4", 0.3),
    ("R", 0.1),
    ("G4", 0.1), ("A4", 0.3),
    ("R", 0.1),
    ("G4", 0.1), ("F4", 0.3),
    ("E4", 0.3), ("C4", 0.5),
]


def set_serv_angle(angle):
    duty = int((angle / 180.0) * 102 + 26)
    serv.duty(duty)

def set_serv2_angle(angle):
    duty = int((angle / 180.0) * 102 + 26)
    serv2.duty(duty)

def play_note(note, duration):
    freq = notes.get(note, 0)
    if freq == 0:
        buzzer.duty(0)
    else:
        buzzer.freq(freq)
        buzzer.duty(512)
        time.sleep(duration)
        buzzer.duty(0)
        time.sleep(0.05)

def play_song(song):
    for note, duration in song:
        play_note(note, duration)

for i in range(16):
    pixy[i] = (0, 0, 0)
    pixy.write()
set_serv_angle(0)
set_serv2_angle(0)
time.sleep(1)

while (True) :
    t_value = t_pin.read()
    print(t_value)
    if (t_value < th) :
        print("Pin Touched")
        red = random.randint(0, 15)
        green = random.randint(0, 255)
        blue = random.randint(0, 255)
        for i in range(16):
            pixy[i] = (red, green, blue)
            n = n+1
            time.sleep(0.02)
            pixy.write()
        set_serv_angle(90)
        set_serv2_angle(80)
        play_song(perry_theme)
        set_serv_angle(0)
        set_serv2_angle(0)
        time.sleep(1)
    else:
        buzzer.duty(0)
        for i in range(16):
            pixy[i] = (0, 0, 0)
            pixy.write()
        time.sleep(0.5)
