# Snake

import time
import tkinter
from random import choice
from tkinter import messagebox

import pygame
import random

pygame.init()

clock = pygame.time.Clock()
def main():

    width = 600

    rows = 20

    win = pygame.display.set_mode((width, width))

    pygame.display.set_caption("Żałrok")

    groundPicture = pygame.image.load( 'pictures/ground.jpg' )
    reptileSkin = pygame.image.load( 'pictures/skin.jpg' )
    reptileHead = pygame.image.load( 'pictures/head.png' )
    reptileTail = pygame.image.load( 'pictures/tail.png' )
    egg = pygame.image.load( 'pictures/egg.png' )

    class Player(object):

        global reptileHead0, reptileSkin0, reptileTail0

        reptileHead0 = pygame.transform.rotate( reptileHead, 0)
        reptileSkin0 = pygame.transform.rotate( reptileSkin, 0)
        reptileTail0 = pygame.transform.rotate( reptileTail, 180)

        def __init__(self, x, y, width, radius, turns, length, direction):

            self.x = x
            self.y = y
            self.width = width
            self.radius = radius
            self.turns = turns
            self.length = length
            self.direction = direction

        def drawBody(self, win):

            global reptileTail0, reptileSkinUp, reptileSkinDown, reptileSkinRight, reptileSkinLeft
            reptileSkinUp = pygame.transform.rotate( reptileSkin, 180 )
            reptileSkinDown = pygame.transform.rotate( reptileSkin, 0 )
            reptileSkinRight = pygame.transform.rotate( reptileSkin, 90 )
            reptileSkinLeft = pygame.transform.rotate( reptileSkin, 270 )

            for i in range(0, Snake.length):
                if Snake.turns[i][2] == 'UP':
                    win.blit( reptileSkinUp,
                                     [Snake.turns[i+1][0], Snake.turns[i+1][1], self.width, self.width])

                if Snake.turns[i][2] == 'DOWN':
                    win.blit( reptileSkinDown,
                                     [Snake.turns[i+1][0], Snake.turns[i+1][1], self.width, self.width])

                if Snake.turns[i][2] == 'RIGHT':
                    win.blit( reptileSkinRight,
                                     [Snake.turns[i+1][0], Snake.turns[i+1][1], self.width, self.width])

                if Snake.turns[i][2] == 'LEFT':
                    win.blit( reptileSkinLeft,
                                     [Snake.turns[i+1][0], Snake.turns[i+1][1], self.width, self.width])

            if Snake.direction == 0:
                reptileTail0 = pygame.transform.rotate(reptileTail, 180)
                win.blit(reptileTail0, [Snake.turns[0][0] + width/rows, Snake.turns[0][1], self.width, self.width])

            else:
                if Snake.turns[1][2] == 'UP':
                    reptileTail0 = pygame.transform.rotate(reptileTail, 90)
                    win.blit(reptileTail0, [Snake.turns[0][0], Snake.turns[0][1], self.width, self.width])

                if Snake.turns[1][2] == 'DOWN':
                    reptileTail0 = pygame.transform.rotate(reptileTail, 270)
                    win.blit(reptileTail0, [Snake.turns[0][0], Snake.turns[0][1], self.width, self.width])

                if Snake.turns[1][2] == 'RIGHT':
                    reptileTail0 = pygame.transform.rotate(reptileTail, 0)
                    win.blit(reptileTail0, [Snake.turns[0][0], Snake.turns[0][1], self.width, self.width])

                if Snake.turns[1][2] == 'LEFT':
                    reptileTail0 = pygame.transform.rotate(reptileTail, 180)
                    win.blit(reptileTail0, [Snake.turns[0][0], Snake.turns[0][1], self.width, self.width])

            win.blit(reptileHead0, [self.x, self.y, self.width, self.width])




        def move(self):

            global changeX
            global changeY
            global reptileHead0

            keys = pygame.key.get_pressed()

            if keys[pygame.K_RIGHT] and Snake.direction != "LEFT" and Snake.direction!= 0:
                Snake.direction = "RIGHT"
                changeX = - width / rows
                changeY = 0

            elif keys[pygame.K_LEFT] and Snake.direction != "RIGHT":
                Snake.direction = "LEFT"
                changeX = + width / rows
                changeY = 0

            elif keys[pygame.K_DOWN] and Snake.direction != "UP":
                Snake.direction = "DOWN"
                changeX = 0
                changeY = - width / rows

            elif keys[pygame.K_UP] and Snake.direction != "DOWN":
                Snake.direction = "UP"
                changeX = 0
                changeY = + width / rows

            if Snake.direction == "UP":
                Snake.y -= width / rows
                reptileHead0 = pygame.transform.rotate( reptileHead, 270 )

            if Snake.direction == "DOWN":
                Snake.y += width / rows
                reptileHead0 = pygame.transform.rotate(reptileHead, 90)

            if Snake.direction == "RIGHT":
                Snake.x += width / rows
                reptileHead0 = pygame.transform.rotate(reptileHead, 180)

            if Snake.direction == "LEFT":
                Snake.x -= width / rows
                reptileHead0 = pygame.transform.rotate(reptileHead, 0)

            if Snake.direction == 0:
                Snake.x += 0
                Snake.y += 0
            # SNAKE CAN CROSS THE WINDOW:

            if Snake.x < 0 and Snake.direction == "LEFT":
                Snake.x = width - width / rows

            if Snake.x > width - width / rows and Snake.direction == "RIGHT":
                Snake.x = 0

            if Snake.y < 0 and Snake.direction == "UP":
                Snake.y = width - width / rows

            if Snake.y > width - width / rows and Snake.direction == "DOWN":
                Snake.y = 0

            self.turns.append([self.x, self.y, self.direction])

        def addBody(length):

            Snake.length += 1


    class Snack(object):

        def __init__(self, x, y, width):
            self.x = x
            self.y = y
            self.width = width

        def newSnackDraw(self, win):

            xSnakeTurns = [x[0] for x in Snake.turns]
            ySnakeTurns = [y[1] for y in Snake.turns]

            # CHOICE PREVENTS APPEARING SNACK INSIDE THE SNAKE

            self.x = random.randrange(0, width, int(width / rows))

            if self.x in xSnakeTurns:

                self.y = int(choice([i for i in range(0, width, int(width / rows)) if i not in ySnakeTurns]))

            else:
                self.y = random.randrange(0, width, int(width / rows))

            win.blit( egg, (self.x, self.y, width / rows, width / rows))

        def draw(self, win):

            win.blit( egg, (self.x, self.y, width / rows, width / rows))


    def MessageBox( title, message ):
        root = tkinter.Tk()
        root.attributes()
        root.withdraw()
        response = messagebox.askretrycancel( title, message )
        if response == True:
            return True
        else:
            return False

        try:
            root.destroy()
        except:
            pass


    def drawGrid():
        sizeBtwn = width // rows
        x = 0
        y = 0

        for i in range(rows):
            x = x + sizeBtwn
            y = y + sizeBtwn

            pygame.draw.line(win, (255, 255, 255), (x, 0), (x, width))
            pygame.draw.line(win, (255, 255, 255), (0, y), (width, y))


    def redrawGameWindow():

        win.fill((100, 100, 0))
        win.blit( groundPicture, ( -550, -400 ))
        drawGrid()

        Snake.drawBody(win)
        Cookie.draw(win)

    RandomX = random.randrange(0, width - width/rows, int(width / rows))
    RandomY = random.randrange(0, width, int(width / rows))

    Snake = Player( RandomX, RandomY,
                   width / rows, width / rows / 10, [ [ RandomX, RandomY ], [ RandomX, RandomY ] ] , 0, 0)
    Cookie = Snack(random.randrange(0, width, int(width / rows)), random.randrange(0, width, int(width / rows)),
                   width / rows)



    def runGame():
        start = round(time.time() * 1000)
        run = True

        while run:

            clock.tick( 8 + ( Snake.length)/2 )

            print(Snake.turns)
            print(Snake.length)

            for event in pygame.event.get():

                if event.type == pygame.QUIT:
                    pygame.quit()
                    exit()

            if Snake.turns[-1][:2] in [i[:-1][:2] for i in Snake.turns[:-1] ] and Snake.direction != 0:

                end = round(time.time() * 1000)
                resultTime = str( ( end - start ) / 1000)

                if MessageBox("Przegrywanko", "Twój wynik: " + str( Snake.length ) + "\nTwój czas: " + resultTime ) == True:

                    main()
                    
                else:
                    break

            Player.move(Snake)

            if Cookie.x == Snake.x and Cookie.y == Snake.y:

                Snack.newSnackDraw(Cookie, win)

                Player.addBody(Snake)

            else:
                Snake.turns.pop(0)
                
            pygame.display.update()

            redrawGameWindow()

    runGame()

    pygame.quit()

main()

