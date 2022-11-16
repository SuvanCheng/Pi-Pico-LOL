设备：Pi Pico

平台：基于CircuitPython

显示器：1920*1080

系统：Windows11

程序：CircuitPython

英雄：琴女

功能：
- 自动释放加血
- 自动释放攻击
- 自动对线

用法：复制整个项目到Pi Pico盘下（包括lib、code.py）

```
import time
import usb_hid
import board
import digitalio
from adafruit_hid.mouse import Mouse
from adafruit_hid.keyboard import Keyboard
from adafruit_hid.keycode import Keycode

import circuitpython_schedule as schedule

mouse = Mouse(usb_hid.devices)
kbd = Keyboard(usb_hid.devices)

led = digitalio.DigitalInOut(board.GP25)
led.direction = digitalio.Direction.OUTPUT

j = 6
i = 1
led.value = False
# time.sleep(3)

# LED闪烁
def Blinking():
    led.value = True
    time.sleep(1)
    led.value = False

# 鼠标归位
def CursorCenter():
    mouse.move(x = -3000) # 左
    mouse.move(y = -3000) # 上
    mouse.move(x = 383)   # 右
    mouse.move(y = 259)   # 下

# 寻找对局
def Find_a_Match():
    CursorCenter()
    mouse.click(Mouse.LEFT_BUTTON) # 给队友点赞
    mouse.move(x = -55)
    mouse.move(y = 91)
    time.sleep(1)
    mouse.click(Mouse.LEFT_BUTTON) # xxx
    time.sleep(1)
    mouse.click(Mouse.LEFT_BUTTON) # 再来一局
    time.sleep(0.1)
    mouse.click(Mouse.LEFT_BUTTON) # 再来一局
    time.sleep(1)
    mouse.click(Mouse.LEFT_BUTTON) # 寻找对局
    time.sleep(0.1)
    mouse.click(Mouse.LEFT_BUTTON) # 寻找对局

# 加入战斗
def Join_the_battle():
    Find_a_Match()
    mouse.move(x = 50)
    mouse.move(y = -44)
    kk = 0
    while kk < 30:
        time.sleep(1)
        mouse.click(Mouse.LEFT_BUTTON) # 参加战斗
        kk += 1

# 选择英雄
def SelectSummoner():
    Join_the_battle()
    CursorCenter()
    mouse.move(x = 102)
    mouse.move(y = -19)
    mouse.click(Mouse.LEFT_BUTTON) # 选择
    time.sleep(0.5)
    mouse.click(Mouse.LEFT_BUTTON) # 选择
    time.sleep(0.5)
    mouse.click(Mouse.LEFT_BUTTON) # 选择
    time.sleep(1)
    mouse.move(x = -80)
    mouse.move(y = 85)
    time.sleep(1)
    mouse.click(Mouse.LEFT_BUTTON) # 锁定
    time.sleep(0.5)
    mouse.click(Mouse.LEFT_BUTTON) # 锁定
    time.sleep(0.5)
    CursorCenter()


# 购买装备
def Equipment():
    CursorCenter()
    time.sleep(0.2)
    kbd.send(Keycode.P)
    mouse.move(x = -300)
    mouse.move(y = 25)
    mouse.click(Mouse.LEFT_BUTTON)
    time.sleep(0.2)
    mouse.click(Mouse.LEFT_BUTTON)
    CursorCenter()
    mouse.move(x = -277)
    mouse.move(y = -65)
    time.sleep(0.3)
    mouse.click(Mouse.LEFT_BUTTON)
    mouse.click(Mouse.LEFT_BUTTON)
    time.sleep(0.3)
    mouse.move(y = 97)
    time.sleep(0.3)
    mouse.click(Mouse.LEFT_BUTTON)
    mouse.click(Mouse.LEFT_BUTTON)
    time.sleep(0.3)
    mouse.move(y = 53)
    time.sleep(0.3)
    mouse.click(Mouse.LEFT_BUTTON)
    mouse.click(Mouse.LEFT_BUTTON)
    time.sleep(0.3)
    kbd.send(Keycode.P)

# 跟随
def Follow():
    global i
    global j
    if i == 2:
        kbd.press(Keycode.F2)
    elif i == 3:
        kbd.press(Keycode.F4)
    elif i == 4:
        kbd.press(Keycode.F3)
    elif i == 5:
        kbd.press(Keycode.F5)
    else:
        kbd.press(Keycode.F5)
    CursorCenter()
    j = j*(-1)
    mouse.move(x = j)
    j = j*(-1)
    mouse.click(Mouse.LEFT_BUTTON)
    mouse.move(y = j)
    j = j*(-1)
    time.sleep(0.01)
    mouse.click(Mouse.RIGHT_BUTTON)
    time.sleep(0.01)
    kbd.release_all()

# 升级技能
def Upgrade():
    kbd.send(Keycode.CONTROL, Keycode.W)
    time.sleep(0.01)
    kbd.send(Keycode.CONTROL, Keycode.E)
    time.sleep(0.01)
    kbd.send(Keycode.CONTROL, Keycode.Q)
    time.sleep(0.01)
    kbd.send(Keycode.CONTROL, Keycode.R)
    time.sleep(0.01)

# 切换跟随
def Swith():
    global i
    i += 1

# 释放技能W
def ReleaseW():
    kbd.send(Keycode.W)

# 释放技能Q
def ReleaseE():
    kbd.send(Keycode.E)

# D
def TreatD():
    kbd.send(Keycode.D)

# F
def TreatF():
    kbd.send(Keycode.F)


# schedule every 1 seconds
schedule.every(0.9).seconds.do(Follow)
schedule.every(60).seconds.do(ReleaseW)
schedule.every(10).seconds.do(ReleaseE)
schedule.every(4).seconds.do(Upgrade)
schedule.every(10).seconds.do(TreatD)
schedule.every(10).seconds.do(TreatF)
schedule.every(60*5).seconds.do(Swith)
schedule.every(60*22).seconds.do(SelectSummoner)
# schedule.every(20).seconds.do(Equipment)


SelectSummoner()
while True:
    schedule.run_pending()
    time.sleep(0.1)

```

欢迎评论

![pico lol.jpg](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bc7f88488fe742a48817036fedc8ffcb~tplv-k3u1fbpfcp-watermark.image?)