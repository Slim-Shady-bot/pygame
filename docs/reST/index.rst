import pygame
import random
import sys

pygame.init()

# Window
WIDTH, HEIGHT = 500, 700
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Car Gas Collector")

clock = pygame.time.Clock()

# Colors
WHITE = (255, 255, 255)
RED = (200, 0, 0)
GREEN = (0, 200, 0)
BLUE = (0, 120, 255)
YELLOW = (255, 255, 0)
BLACK = (0, 0, 0)

# Player car
player = pygame.Rect(225, 600, 50, 80)
player_speed = 4
boost_speed = 8

# Enemy cars
enemy_list = []
ENEMY_SPEED = 4
SPAWN_ENEMY = pygame.USEREVENT + 1
pygame.time.set_timer(SPAWN_ENEMY, 900)

# Gas cans
gas_list = []
SPAWN_GAS = pygame.USEREVENT + 2
pygame.time.set_timer(SPAWN_GAS, 1400)

score = 0
font = pygame.font.SysFont("Arial", 28)

def spawn_enemy():
    x = random.randint(50, WIDTH - 100)
    enemy_list.append(pygame.Rect(x, -100, 50, 80))

def spawn_gas():
    x = random.randint(50, WIDTH - 80)
    gas_list.append(pygame.Rect(x, -60, 30, 30))

def draw_text(text, x, y):
    img = font.render(text, True, WHITE)
    screen.blit(img, (x, y))

# Game Loop
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
if event.type == SPAWN_ENEMY:
            spawn_enemy()

        if event.type == SPAWN_GAS:
            spawn_gas()

    keys = pygame.key.get_pressed()

    # Movement
    speed = boost_speed if keys[pygame.K_LSHIFT] else player_speed

    if keys[pygame.K_LEFT] and player.x > 0:
        player.x -= speed
    if keys[pygame.K_RIGHT] and player.x < WIDTH - player.width:
        player.x += speed
    if keys[pygame.K_UP] and player.y > 0:
        player.y -= speed
    if keys[pygame.K_DOWN] and player.y < HEIGHT - player.height:
        player.y += speed

    # Move enemies
    for enemy in enemy_list[:]:
        enemy.y += ENEMY_SPEED
        if enemy.y > HEIGHT:
            enemy_list.remove(enemy)
        if enemy.colliderect(player):
            print("GAME OVER")
            pygame.quit()
            sys.exit()

    # Move gas
    for gas in gas_list[:]:
        gas.y += 3
        if gas.y > HEIGHT:
            gas_list.remove(gas)
        if gas.colliderect(player):
            gas_list.remove(gas)
            score += 1

    # Draw
    screen.fill(BLACK)

    pygame.draw.rect(screen, BLUE, player)

    for enemy in enemy_list:
        pygame.draw.rect(screen, RED, enemy)

    for gas in gas_list:
        pygame.draw.rect(screen, YELLOW, gas)

    draw_text(f"Score: {score}", 10, 10)

    pygame.display.flip()
    clock.tick(60)
