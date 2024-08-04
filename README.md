import pygame
import random

# Initialize Pygame
pygame.init()

# Screen dimensions
SCREEN_WIDTH = 400
SCREEN_HEIGHT = 600

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GREEN = (0, 255, 0)
RED = (255, 0, 0)

# Bird settings
BIRD_WIDTH = 40
BIRD_HEIGHT = 30
BIRD_X = 50
BIRD_Y = 300
GRAVITY = 0.5
FLAP_POWER = -10

# Pipe settings
PIPE_WIDTH = 70
PIPE_HEIGHT = 500
PIPE_GAP = 200
PIPE_FREQUENCY = 1500  # milliseconds

# Initialize the screen
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Flappy Bird")

# Load bird image
bird_image = pygame.Surface((BIRD_WIDTH, BIRD_HEIGHT))
bird_image.fill(RED)

# Game variables
bird_y = BIRD_Y
bird_y_velocity = 0
pipes = []
score = 0
game_over = False

# Clock object
clock = pygame.time.Clock()

# Custom events
ADD_PIPE = pygame.USEREVENT + 1
pygame.time.set_timer(ADD_PIPE, PIPE_FREQUENCY)

def draw_pipes(pipes):
    for pipe in pipes:
        pygame.draw.rect(screen, GREEN, pipe["top"])
        pygame.draw.rect(screen, GREEN, pipe["bottom"])

def check_collision(pipes):
    for pipe in pipes:
        if bird.colliderect(pipe["top"]) or bird.colliderect(pipe["bottom"]):
            return True
    if bird_y <= 0 or bird_y >= SCREEN_HEIGHT - BIRD_HEIGHT:
        return True
    return False

# Main game loop
running = True
while running:
    screen.fill(WHITE)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE and not game_over:
                bird_y_velocity = FLAP_POWER
            if event.key == pygame.K_SPACE and game_over:
                game_over = False
                bird_y = BIRD_Y
                bird_y_velocity = 0
                pipes = []
                score = 0
        if event.type == ADD_PIPE:
            pipe_y = random.randint(-PIPE_HEIGHT + 100, 100)
            pipes.append({"top": pygame.Rect(SCREEN_WIDTH, pipe_y, PIPE_WIDTH, PIPE_HEIGHT),
                          "bottom": pygame.Rect(SCREEN_WIDTH, pipe_y + PIPE_HEIGHT + PIPE_GAP, PIPE_WIDTH, PIPE_HEIGHT)})

    if not game_over:
        bird_y_velocity += GRAVITY
        bird_y += bird_y_velocity

        bird = pygame.Rect(BIRD_X, bird_y, BIRD_WIDTH, BIRD_HEIGHT)
        screen.blit(bird_image, bird)

        pipes = [{"top": pipe["top"].move(-5, 0), "bottom": pipe["bottom"].move(-5, 0)} for pipe in pipes]
        pipes = [pipe for pipe in pipes if pipe["top"].right > 0]

        draw_pipes(pipes)

        if check_collision(pipes):
            game_over = True

        for pipe in pipes:
            if pipe["top"].right == BIRD_X:
                score += 1

    else:
        font = pygame.font.Font(None, 74)
        text = font.render("Game Over", True, BLACK)
        screen.blit(text, (50, 250))

    font = pygame.font.Font(None, 36)
    text = font.render(f"Score: {score}", True, BLACK)
    screen.blit(text, (10, 10))

    pygame.display.flip()
    clock.tick(30)

pygame.quit()
 
