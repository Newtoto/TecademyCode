```
import pygame, random
from time import sleep
from gpiozero import Motor, RGBLED
import os

pygame.init()
submitted = False
correctness = False
playerans = ""
keyans = 0
numbers = (0, 0)
total = 0
screenDims = (800, 600)
WHITE = (255,255,255)
BLACK = (0, 0, 0)
totalMessage = ""

motorA = Motor(7, 8)
led = RGBLED(red=2, green=3, blue=4)
rCorrect, gCorrect, bCorrect = 0, 1, 0
rIncorrect, gIncorrect, bIncorrect = 1, 0, 0

screen = pygame.display.set_mode(screenDims)

def correct():
    os.system("mpg321 /home/pi/Desktop/cheer.mp3 -q &")
    led.color = (rCorrect, gCorrect, bCorrect)
    motorA.forward()
    sleep(2)
    motorA.stop()
    led.off()
    return

def incorrect():
    os.system("mpg321 /home/pi/Desktop/incorrect.mp3 -q &")
    led.color = (rIncorrect, gIncorrect, bIncorrect)
    motorA.backward()
    sleep(2)
    motorA.stop()
    led.off()
    return

def text_objects(text, font):
    textSurface = font.render(text, True, WHITE)
    return textSurface, textSurface.get_rect()
    

def message_display(text, x, y):
    LargeText = pygame.font.Font(None, 75)
    TextSurf, TextRect = text_objects(text, LargeText)
    TextRect.center = (x, y)
    screen.blit(TextSurf, TextRect)
    pygame.display.flip()

def display_messages():
    screen.fill(BLACK)
    message_display(teststring, screenDims[0]/2, screenDims[1]/2 - 100)
    message_display(playerans, screenDims[0]/2, screenDims[1]/2)
    message_display(totalMessage, screenDims[0]/2, screenDims[1]/2 + 100)
    
controls = {'right': 1,
            'up': 2,
            'down': 3,
            'w': 4,
            'a': 5,
            's': 6,
            'd': 7,
            'f': 8,
            'g': 9,
            'left': 0}

def compare_to_answer(input):
    return (input == answer)

for i in range(10):
    submitted = False
    correctness = False
    playerans = ""
    numbers = (random.randint(0, 12), random.randint(0, 12))
    answer = numbers[0] * numbers[1]
    teststring = ('What is %d times %d?' % numbers)
    display_messages()

    while submitted == False:
        for event in pygame.event.get():

            if event.type == pygame.QUIT:
                pygame.quit()
            if event.type == pygame.KEYUP:
                        
                keytype = pygame.key.name(event.key)
                        
                if keytype in controls:
                    keyans = controls[keytype]
                    playerans += str(keyans)
                    display_messages() 
                if event.key == pygame.K_SPACE:
                    submitted = True
                    try:
                        if compare_to_answer(int(playerans)):
                            total += 1
                            correctness = True
                        else:
                            correctness = False
                    except:
                        print ('Enter a number')

    totalMessage = ('You got %d out of %d' % (total, i+1))
    display_messages()
    if i == 9:
        screen.fill(BLACK)
        message_display(('Game Over'), screenDims[0]/2, screenDims[1]/2 - 50)
        message_display('You got %d out of %d' % (total, i+1), screenDims[0]/2, screenDims[1]/2 + 50)
    if correctness:
        correct()
    else:
        incorrect()

```
