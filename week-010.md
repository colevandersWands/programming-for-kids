# Chapter 10 - Week 10

```
day0: Basics of Basics
day1: Basics of Basics
day2: Basics of Basics
day3: Basics of Basics
day4: Basics of Basics
day5: Basics of Basics
day6: Basics of Basics
```


## [DAY-68] Basics of Basics

Shoot bullets and remove them from the list, play sound, and also level up after killing 3 snakes!


![game-68.png](./screenshots/game-68.png "game 68 screenshot")

```
import pgzrun
import random

HEIGHT = 200
WIDTH = 200

score = 0
speed = 1
elf = Actor("c1")
snake = Actor("snake")
beep = tone.create('A3', 0.5)

elf.x = WIDTH/2
elf.y = HEIGHT/2
bullets = []

def place_snake():
    snake.x = random.randint(10,WIDTH-10)
    snake.y = random.randint(10,HEIGHT-10)

place_snake()

def bullet_out_of_screen():
    # just delete the first bullet
    bullets.pop(0)

def on_key_down(key):
    if key == keys.SPACE:
        b = Actor("bullet2")
        b.x = elf.x + 5
        b.y = elf.y
        animate(b, pos=(WIDTH + 100, elf.y), tween='accelerate', on_finished=bullet_out_of_screen)
        bullets.append(b)

def update():
    global game_over, speed, score, bullets
    if keyboard.left:
        elf.x = elf.x-speed
    if keyboard.right:
        elf.x = elf.x+speed
    if keyboard.up:
        elf.y = elf.y-speed
    if keyboard.down:
        elf.y= elf.y+speed
    if keyboard.R:
        speed = 1
        score = 0
        elf.image = "c1"
        bullets = []

    for b in bullets:
        if b.colliderect(snake):
            score += 1
            speed += 1
            if score == 3:
                elf.image = "c2"
            beep.play()
            place_snake()

def draw():
    screen.fill('black')
    elf.draw()
    snake.draw()
    for b in bullets:
        b.draw()

    screen.draw.text("Score: "+ str(score), color="white", topleft=(10,10))

pgzrun.go()
```

You see how we are adding, bullets to a list every time you press 'space'. Now the problem is we need to clean up that list at some point, otherwise it will grow a lot, and every time we do `for b in bullets` it will bet slower and slower. Computers can only do so many things per second.

So when the animation finishes, it will call `bullets_out_of_screen`, and from there we just say `bullets.pop(0)` which will remove the first element in the list of bullets, which is the oldest bullet. So if you press space 5 times, the list will have 5 items, and each of them will finish and will remove the first element.

Lets say you pressed space 3 times, and shot 3 bullets, so the list, and how the bullets are traveling kindof looks like this:

```
0 -> bbbbbbbbbbbbbb
1 -> bbbbbbb
2 -> bbbb
```

So you see when 0 finishes because it is the oldest, when it does bullets.pop(0), it will actually delete itself.

pretty cool!

## [DAY-69] Basics of Basics

![game-69.png](./screenshots/game-69.png "game 69 screenshot")

Multiplayer!

```
import pgzrun
import socket
import threading
import sys

HEIGHT = 200
WIDTH = 200
MULTIPLAYER_PORT = 5025
OTHER_COMPUTER_IP = "192.168.0.10"

speed = 4
me = Actor("c1")
other = Actor("c2")

me.x = WIDTH/2
me.y = HEIGHT/2

socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
socket.bind(("0.0.0.0", MULTIPLAYER_PORT))

data = None
def multiplayer():
    global data
    while True:
        data, addr = socket.recvfrom(1024)

def update():
    if data != None:
        message = data.decode("utf8")
        (x,y) = message.split(":")

        other.x = float(x)
        other.y = float(y)


def on_key_down(key):
    if key == keys.LEFT:
        me.x = me.x-speed
    if key == keys.RIGHT:
        me.x = me.x+speed
    if key == keys.UP:
        me.y = me.y-speed
    if key == keys.DOWN:
        me.y= me.y+speed

    if key == keys.Q:
        sys.exit(0)

    message = bytearray(str(me.x) + ':' + str(me.y),"utf8")
    socket.sendto(message, (OTHER_COMPUTER_IP, MULTIPLAYER_PORT))

def draw():
    screen.fill('black')
    me.draw()
    other.draw()


threading.Thread(target=multiplayer).start()

pgzrun.go()
```

There are two important parts here, first is the `socket` and then the `Thread`. For now we will leave them a mystery. But basically every time you press the UP/DOWN/LEFT/RIGHT key your computer sends a message "currentX:currentY" to the other computer, and when the other computer receives it, it stores it in the global `data` variable, then in the `update` function it splits the message by `:` and extract the `x` and `y` and sets it to the `other` actor.

The `multiplayer()` function runs in a separate thread, or you can think of a separate process, so we are doing two things in the same time, the game loop (which is one `while True`) and the multiplayer loop which is also `while True`.


## [DAY-70] Basics of Basics

print numbers in a list

```
data = [1,2,3,4,5]
for item in data:
    print(e)
```


```
list_of_lists = [[1,2,3,4,5], [1,2,3], [4,5,6]]
for l in list_of_lists:
    for item in l:
        print(item)
```


```
list_of_list_of_lists = [[[1,2,3,4,5], [1,2,3], [4,5,6]], [[1,2,3,4,5], [1,2,3], [4,5,6]]]
for list_of_lists in list_of_list_of_lists:
    for l in list_of_lists:
        for item in l:
            print(item)
```


```
list_of_list_of_list_of_lists = [
    [
        [
            [1,2,3,4,5],
            [1,2,3],
            [4,5,6]
        ],
        [
            [1,2,3,4,5],
            [1,2,3],
            [4,5,6]
        ]
    ],
    [
        [
            [1,2,3,4,5],
            [1,2,3],
            [4,5,6]
        ],
        [
            [1,2,3,4,5],
            [1,2,3],
            [4,5,6]
        ]
    ],
]

for list_of_list_of_lists in list_of_list_of_list_of_lists:
    for list_of_lists in list_of_list_of_lists:
        for l in list_of_lists:
            for item in l:
                print(item)
```


```
def sum(x):
    r = 0
    for item in x:
        r += item
    return r

data = [1,2,3,4,5]
print(sum(data))
```


```
def sum(x):
    r = 0
    for l in x:
        for item in l:
            r += item
    return r

data = [[1,2,3,4,5], [1,2,3,4,5]]
print(sum(data))
```

## [DAY-71] Basics of Basics
make a calculator

```
while True:
    n1 = int(input("number 1: "))
    n2 = int(input("number 2: "))
    op = input("operation +/-* : ")
    r = 0

    if op == "quit":
        break
    elif op == "+":
        r = n1 + n2
    elif op == "-":
        r = n1 - n2
    elif op == "*":
        r = n1 * n2
    else:
        print("i dont know " + op)
        continue

    print(n1, op, n2, '=',r)

```

sum numbers from input

```
def sum(x):
    r = 0
    for item in x:
        r += item
    return r

x = []
while True:
    k = input("enter number: ")
    x.append(int(k))
    print(x)
    print(sum(x))

```
## [DAY-72] Basics of Basics

Reading code is even more important than writing it, and it is more difficult than reading a book, because the story does not develop top to bottom. You have to see the important pieces, and how they are linked together. Where are things assigned and changed and how are those pieces related. For example in the touch typing program, you have a couple of functions, you can study them on their own, and know what they do, and then see how they are used and make sense of the whole program, without even running it.

We will go over a couple of programs we wrote before, the rock paper scissors game, the bullet shooting game, and one of the touch typing programs.

![day-72-0.jpg](./reading/day-72-0.jpg "day 72 example 0")

![day-72-1.jpg](./reading/day-72-1.jpg "day 72 example 1")

![day-72-2.jpg](./reading/day-72-2.jpg "day 72 example 2")


Those are just examples, you have to print the programs and go over them with your parent.

## [DAY-73] Basics of Basics

More reading today, those are a couple of examples from the earlier days, the love tester, the fight function from the text game, the hangman game and first touch typing program.


![day-73-0.jpg](./reading/day-73-0.jpg "day 73 example 0")

![day-73-1.jpg](./reading/day-73-1.jpg "day 73 example 1")

![day-73-2.jpg](./reading/day-73-2.jpg "day 73 example 2")

![day-73-3.jpg](./reading/day-73-3-0.jpg "day 73 example 3")

Again you can use something else as well, those are just the ones I picked. Be messy with the pencil, connect things together!

## [DAY-74] Basics of Basics
