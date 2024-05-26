from pygame import *
from random import choice


font.init()


class GameSprite(sprite.Sprite):
    def __init__(self, image_path, x, y, speed, width, height):
        super().__init__()

        self.image = transform.scale(
            image.load(image_path), (width, height)
        )

        self.speed = speed

        self.rect = self.image.get_rect()
        self.rect.x = x
        self.rect.y = y

    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))


class Player(GameSprite):
    def __init__(self, image_path, x, y, speed, width, height, key_up, key_down):
        super().__init__(image_path, x, y, speed, width, height)
        self.key_up = key_up
        self.key_down = key_down

    def update(self):
        keys = key.get_pressed()
        if keys[self.key_up] and self.rect.y > 0:
            self.rect.y -= self.speed

        if keys[self.key_down] and self.rect.y < win_height - self.rect.height:
            self.rect.y += self.speed

        self.reset()


class Ball(GameSprite):
    def __init__(self, image_path, x, y, speed, width, height):
        super().__init__(image_path, x, y, speed, width, height)

        self.speed_x = speed * choice([1, -1]) * choice([0.7, 0.8, 0.9, 1, 1.1, 1.2, 1.3])
        self.speed_y = speed * choice([1, -1]) * choice([0.7, 0.8, 0.9, 1, 1.1, 1.2, 1.3])

    def update(self):
        self.rect.x += self.speed_x
        self.rect.y -= self.speed_y
        self.reset()


back_color = (200, 255, 255)
win_width, win_height = 600, 500

window = display.set_mode((win_width, win_height))
window.fill(back_color)


player1 = Player("files/raketka.jpg_1.png", 5, 200, 8, 40, 150, K_w, K_s)
player2 = Player("files/raketka.jpg_1.png", 555, 200, 8, 40, 150, K_UP, K_DOWN)

ball_1 = Ball("files/ball.png", win_width // 2, win_height // 2, 3, 55, 55)
ball_2 = Ball("files/ball.png", win_width // 3, win_height // 3, 3, 55, 55)

game = True
finish = False

while game:
    for e in event.get():
        if e.type == QUIT:
            game = False
        if e.type == KEYDOWN and e.key == K_r and finish:
            finish = False
            ball_1.rect.x = win_width // 2
            ball_1.rect.y = win_height // 2
            ball_2.rect.x = win_width // 3
            ball_2.rect.y = win_height // 3

    if not finish:
        window.fill(back_color)

        player1.update()
        player2.update()
        ball_1.update()
        ball_2.update()

        if ball_1.rect.bottom > win_height or ball_1.rect.top < 0:
            ball_1.speed_y *= -1

        if sprite.collide_rect(ball_1, player1):
            ball_1.speed_x = abs(ball_1.speed_x)

        if sprite.collide_rect(ball_1, player2):
            ball_1.speed_x = -abs(ball_1.speed_x)

        if sprite.collide_rect(ball_1, ball_2):
            ball_1.speed_x = abs(ball_1.speed_x)

        if ball_1.rect.right > win_width:
            finish = True
            window.blit(
                font.SysFont("Arial", 40).
                render("PLAYER 1 WIN!", True, (255, 0, 0)),
                (180, win_height // 2)
            )

        if ball_1.rect.left < 0:
            finish = True
            window.blit(
                font.SysFont("Arial", 40).
                render("PLAYER 2 WIN!", True, (255, 0, 0)),
                (180, win_height // 2)
            )
#ball_2
        if ball_2.rect.bottom > win_height or ball_2.rect.top < 0:
            ball_2.speed_y *= -1

        if sprite.collide_rect(ball_2, ball_1):
            ball_2.speed_x = abs(ball_2.speed_x)

        if sprite.collide_rect(ball_2, player1):
            ball_2.speed_x = abs(ball_2.speed_x)

        if sprite.collide_rect(ball_2, player2):
            ball_2.speed_x = -abs(ball_2.speed_x)

        if ball_2.rect.right > win_width:
            finish = True
            window.blit(
                font.SysFont("Arial", 40).
                render("PLAYER 1 WIN!", True, (255, 0, 0)),
                (180, win_height // 2)
            )

        if ball_2.rect.left < 0:
            finish = True
            window.blit(
                font.SysFont("Arial", 40).
                render("PLAYER 2 WIN!", True, (255, 0, 0)),
                (180, win_height // 2)
            )

    else:
        window.blit(
            font.SysFont("Arial", 30).render("Нажмите 'R' для перезапуска", True, (224, 46, 255)),
            (150, 290)
        )
    display.update()
    time.Clock().tick(60)
