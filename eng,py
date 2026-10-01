import pygame
import math

# 1. 파이게임 초기화 및 설정
pygame.init()
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("2D 드리프트 물리 엔진")

# 색상
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
BLUE = (50, 150, 255)
RED = (255, 50, 50)

class Kart:
    def __init__(self, x, y):
        self.pos = pygame.math.Vector2(x, y)
        self.velocity = pygame.math.Vector2(0, 0)
        self.angle = 0        # 카트가 바라보는 각도
        self.speed = 0        # 현재 속력
        self.acceleration = 0.5
        self.max_speed = 12
        self.steering = 4     # 핸들링 (회전 속도)

    def update(self, keys):
        # 1. 전진 및 후진 (가속도)
        if keys[pygame.K_UP]:
            self.speed += self.acceleration
        elif keys[pygame.K_DOWN]:
            self.speed -= self.acceleration
        else:
            # 엑셀을 떼면 서서히 감속 (자연 마찰)
            self.speed *= 0.95 

        # 최고 속도 제한
        self.speed = max(-self.max_speed / 2, min(self.speed, self.max_speed))

        # 2. 회전 (차가 움직일 때만 핸들이 돌아감)
        if abs(self.speed) > 0.1:
            if keys[pygame.K_LEFT]:
                self.angle -= self.steering
            if keys[pygame.K_RIGHT]:
                self.angle += self.steering

        # 3. 차가 바라보는 방향(Heading) 벡터 계산
        rad = math.radians(self.angle)
        heading = pygame.math.Vector2(math.cos(rad), math.sin(rad))

        # 4. 드리프트 시스템 (핵심 로직)
        drifting = keys[pygame.K_LSHIFT] or keys[pygame.K_RSHIFT]
        
        # 타이어의 그립력 (1.0이면 미끄러짐 없음, 0에 가까울수록 빙판길처럼 미끄러짐)
        if drifting:
            grip = 0.05  # 드리프트 중: 그립력을 크게 낮춰 관성대로 미끄러지게 함
        else:
            grip = 0.8   # 평상시: 바라보는 방향으로 빠르게 진행 방향을 맞춤

        # 목표 속도 벡터 (바라보는 방향 * 현재 속력)
        target_velocity = heading * self.speed

        # 현재 속도에서 목표 속도로 부드럽게 전환 (Lerp 알고리즘)
        self.velocity = self.velocity.lerp(target_velocity, grip)

        # 5. 위치 업데이트
        self.pos += self.velocity

        # 화면 밖으로 나가지 않게 처리
        self.pos.x = max(0, min(WIDTH, self.pos.x))
        self.pos.y = max(0, min(HEIGHT, self.pos.y))

    def draw(self, surface):
        # 카트 본체(직사각형) 생성 및 회전
        kart_image = pygame.Surface((40, 24), pygame.SRCALPHA)
        
        # 카트 색상 (드리프트 중일 때는 빨간색으로 변경)
        keys = pygame.key.get_pressed()
        color = RED if (keys[pygame.K_LSHIFT] or keys[pygame.K_RSHIFT]) else BLUE
        
        pygame.draw.rect(kart_image, color, (0, 0, 40, 24))
        pygame.draw.rect(kart_image, WHITE, (30, 4, 10, 16)) # 자동차 앞유리(방향 표시용)

        # 이미지 회전 (-self.angle인 이유는 Pygame의 y축이 아래로 증가하기 때문)
        rotated_image = pygame.transform.rotate(kart_image, -self.angle)
        rect = rotated_image.get_rect(center=(int(self.pos.x), int(self.pos.y)))
        
        surface.blit(rotated_image, rect.topleft)

# 2. 게임 루프 준비
clock = pygame.time.Clock()
kart = Kart(WIDTH // 2, HEIGHT // 2)
running = True

# 3. 메인 루프
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    keys = pygame.key.get_pressed()
    
    kart.update(keys)

    # 화면 그리기
    screen.fill((50, 50, 50)) # 아스팔트 배경
    
    # 조작법 텍스트
    font = pygame.font.SysFont(None, 24)
    text1 = font.render("Arrows: Move & Turn", True, WHITE)
    text2 = font.render("Shift: DRIFT!", True, RED)
    screen.blit(text1, (10, 10))
    screen.blit(text2, (10, 30))

    kart.draw(screen)

    pygame.display.flip()
    clock.tick(60)

pygame.quit()