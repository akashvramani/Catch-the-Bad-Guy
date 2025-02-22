import pygame
import random
import time
import math
import os

def home_screen():
    pygame.init()

    SCREEN_WIDTH = 800
    SCREEN_HEIGHT = 600

    screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
    pygame.display.set_caption("Home Screen")

    play_button_image = pygame.image.load("play_button.png")
    play_button_image = pygame.transform.scale(play_button_image, (150, 150)) 
    play_button_rect = play_button_image.get_rect(center=(SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2 - 50))

    if os.path.exists("highscore.txt"):
        with open("highscore.txt", "r") as file:
            highscore = int(file.read())
    else:
        highscore = 0

    font = pygame.font.Font("Grand9K Pixel.ttf", 50)
    highscore_text = font.render("High Score: " + str(highscore), True, (255, 255, 255))
    highscore_rect = highscore_text.get_rect(center=(SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2 + 100))

    welcome_text = font.render("Welcome!", True, (255, 255, 255))
    welcome_rect = welcome_text.get_rect(center=(SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2 - 200))

    quit_text = font.render("Quit", True, (255, 255, 255))
    quit_rect = quit_text.get_rect(center=(SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2 + 200))

    run = True
    while run:
        screen.fill((0, 0, 0))
        screen.blit(play_button_image, play_button_rect.topleft)
        screen.blit(highscore_text, highscore_rect.topleft)
        screen.blit(welcome_text, welcome_rect.topleft)
        screen.blit(quit_text, quit_rect.topleft)
        pygame.display.update()

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                return
            if event.type == pygame.MOUSEBUTTONDOWN:
                mouse_pos = event.pos
                if play_button_rect.collidepoint(mouse_pos):
                    main()
                    return
                if quit_rect.collidepoint(mouse_pos):
                    pygame.quit()
                    return

def main():
    end = False

    if os.path.exists("highscore.txt"):
        with open("highscore.txt", "r") as file:
            highscore = int(file.read())
    else:
        highscore = 0
        with open("highscore.txt", "w") as file:
            file.write(str(highscore))


    def save_highscore(score):
        with open("highscore.txt", "w") as file:
            file.write(str(score))

    pygame.init()

    SCREEN_WIDTH = 800
    SCREEN_HEIGHT = 600

    scoreSound = pygame.mixer.Sound("score.wav")
    gameOverSound = pygame.mixer.Sound("gameover.wav")
    gameMusic = pygame.mixer.Sound("gamemusic4.wav")

    pygame.display.set_caption("Moving Game")
    player_up = pygame.image.load("player_up.png")
    player_up = pygame.transform.scale(player_up, (70, 70))
    player_down = pygame.image.load("player_down.png")
    player_down = pygame.transform.scale(player_down, (70, 70))
    player_left = pygame.image.load("player_left.png")
    player_left = pygame.transform.scale(player_left, (70, 70))
    player_right = pygame.image.load("player_right.png")
    player_right = pygame.transform.scale(player_right, (70, 70))
    player_image = player_down  # Default direction

    enemy_image = pygame.image.load("square_enemy.png")
    enemy_image = pygame.transform.scale(enemy_image, (70, 70))
    play_button_image = pygame.image.load("play_button.png")
    play_button_image = pygame.transform.scale(play_button_image, (150, 150))
    back_button_image = pygame.image.load("back_button.png")
    back_button_image = pygame.transform.scale(back_button_image, (100, 50))  
    speed_power_up_image = pygame.image.load("speed_power_up.png")
    speed_power_up_image = pygame.transform.scale(speed_power_up_image, (50, 50))
    score_power_up_image = pygame.image.load("+1_score.png")
    score_power_up_image = pygame.transform.scale(score_power_up_image, (50, 50))
    screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
    screen.blit(player_image, (300, 250))
    screen.blit(enemy_image, (400, 250))

    def create_player_rect(x, y, width, height):
        return pygame.Rect(x, y, width, height)
    def create_enemy_rect(x, y, width, height):
        return pygame.Rect(x, y, width, height)
    def create_power_up_rect(x, y, width, height):
        return pygame.Rect(x, y, width, height)
    player = create_player_rect(300, 250, 50, 50)
    enemy = create_enemy_rect(400, 250, 50, 50)
    speed_power_up = create_power_up_rect(random.randint(50, SCREEN_WIDTH - 50), random.randint(50, SCREEN_HEIGHT - 50), 50, 50)
    score_power_up = create_power_up_rect(random.randint(50, SCREEN_WIDTH - 50), random.randint(50, SCREEN_HEIGHT - 50), 50, 50)
    score = 0

    run = True

    start_time = time.time()
    game_duration = 20
    elapsed_time = time.time() - start_time
    speed_power_up_spawn_time = time.time()
    score_power_up_spawn_time = time.time()
    score_power_up_active = True

    clock = pygame.time.Clock()  
    movement_speed = 12

    if not end:
        gameMusic.play()

    while run:
        if game_duration - elapsed_time < 6:
            movement_speed = 15

        screen.fill((0, 0, 0))

        screen.blit(player_image, player.topleft)
        screen.blit(enemy_image, enemy.topleft)

        # Spawn speed power-up every 6 seconds
        if time.time() - speed_power_up_spawn_time >= 6:
            speed_power_up.x = random.randint(50, SCREEN_WIDTH - 50)
            speed_power_up.y = random.randint(50, SCREEN_HEIGHT - 50)
            speed_power_up_spawn_time = time.time()

        # Spawn score power-up every 10 seconds
        if time.time() - score_power_up_spawn_time >= 10 and not score_power_up_active:
            score_power_up.x = random.randint(50, SCREEN_WIDTH - 50)
            score_power_up.y = random.randint(50, SCREEN_HEIGHT - 50)
            score_power_up_spawn_time = time.time()
            score_power_up_active = True

        screen.blit(speed_power_up_image, speed_power_up.topleft)
        if score_power_up_active:
            screen.blit(score_power_up_image, score_power_up.topleft)

        key = pygame.key.get_pressed()
        if key[pygame.K_a] or key[pygame.K_LEFT]:
            player.move_ip(-movement_speed, 0)
            player_image = player_left
        elif key[pygame.K_d] or key[pygame.K_RIGHT]:
            player.move_ip(movement_speed, 0)
            player_image = player_right
        elif key[pygame.K_w] or key[pygame.K_UP]:
            player.move_ip(0, -movement_speed)
            player_image = player_up
        elif key[pygame.K_s] or key[pygame.K_DOWN]:
            player.move_ip(0, movement_speed)
            player_image = player_down

        # Ensure player stays within screen boundaries
        if player.left < 0:
            player.left = 0
        if player.right > SCREEN_WIDTH:
            player.right = SCREEN_WIDTH
        if player.top < 0:
            player.top = 0
        if player.bottom > SCREEN_HEIGHT:
            player.bottom = SCREEN_HEIGHT

        if player.colliderect(enemy):
            enemy.x = random.randint(50, SCREEN_WIDTH - 50)
            enemy.y = random.randint(50, SCREEN_HEIGHT - 50)
            score += 1
            scoreSound.play()
        if player.colliderect(speed_power_up):
            movement_speed += 5
            speed_power_up.x = random.randint(50, SCREEN_WIDTH - 50)
            speed_power_up.y = random.randint(50, SCREEN_HEIGHT - 50)
        if player.colliderect(score_power_up) and score_power_up_active:
            score += 1
            score_power_up_active = False
            scoreSound.play()

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                run = False
            if end and event.type == pygame.MOUSEBUTTONDOWN:
                mouse_pos = event.pos
                if play_button_rect.collidepoint(mouse_pos):
                    main() 
                    return
                if back_button_rect.collidepoint(mouse_pos):
                    home_screen()
                    return

        elapsed_time = time.time() - start_time
        time_remaining = game_duration - elapsed_time
        font = pygame.font.Font("Grand9K Pixel.ttf", 20)
        text = font.render("Time Remaining: " + str(math.ceil(time_remaining)), 5, (255, 255, 255))
        text3 = font.render("High Score: " + str(highscore), 5, (255, 255, 255))
        text2 = font.render("Score: " + str(score), 5, (255, 255, 255))
        screen.blit(text, (10, 10))
        screen.blit(text2, (600, 10))
        screen.blit(text3, (600, 50))
        if elapsed_time >= game_duration:
            end = True

        pygame.display.update()

        if end:
            pygame.mixer.pause()
            gameOverSound.play()
            if score > highscore:
                save_highscore(score)
                highscore = score
            screen.fill((0, 0, 0))
            font = pygame.font.Font("Grand9K Pixel.ttf", 50)
            text = font.render("Game Over!", 5, (255, 255, 255))
            text2 = font.render("Score: " + str(score), 5, (255, 255, 255))
            text3 = font.render("High Score: " + str(highscore), 5, (255, 255, 255))
            screen.blit(text2, (210, 300))
            screen.blit(text, (210, 200))
            screen.blit(text3, (210, 400))
            play_button_rect = play_button_image.get_rect(center=(SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2 - 200))
            back_button_rect = back_button_image.get_rect(topleft=(10, 10)) 
            screen.blit(play_button_image, play_button_rect.topleft)
            screen.blit(back_button_image, back_button_rect.topleft)
            pygame.display.update()
            while end:
                for event in pygame.event.get():
                    if event.type == pygame.QUIT:
                        pygame.quit()
                        return
                    if event.type == pygame.MOUSEBUTTONDOWN:
                        mouse_pos = event.pos
                        if play_button_rect.collidepoint(mouse_pos):
                            main() 
                            return
                        if back_button_rect.collidepoint(mouse_pos):
                            home_screen()
                            return

        clock.tick(60) 

    pygame.quit()

if __name__ == "__main__":
    home_screen()
