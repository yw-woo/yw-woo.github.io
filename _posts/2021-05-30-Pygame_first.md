---
title: "처음 만들어본 Python 프로그램 - 파이게임으로 게임 만들기"
date: 2021-05-30 15:27:28 -0400
categories: Python
---

아래 유튜브 동영상을 보면서 만들어본 첫번째 파이썬 프로그램입니다.

파이썬으로 게임 만들기 강의 

<https://www.youtube.com/watch?v=pRBO1W0OX4I&list=PLZOm4uzWk9WPN8Newzmsr-Q62jwbFhTY_>

<br>




```python
import pygame
import random
import time
from datetime import datetime
```

    pygame 2.0.1 (SDL 2.0.14, Python 3.7.10)
    Hello from the pygame community. https://www.pygame.org/contribute.html
    


```python
# 각 물체의 이미지 로드, 크기 조정, 화면 표시를 위한 class
class obj:
    def _init_(self):
        self.x = 0
        self.y = 0
        self.sx = 0
        self.sy = 0
        
    def put_img(self, address):
        if address[-3:] == 'png':
            self.img = pygame.image.load(address).convert_alpha()
        else:
            self.img = pygame.image.load(address)
        self.sx, self.sy = self.img.get_size()
        
    def change_size(self, sx, sy):
        self.img = pygame.transform.scale(self.img, (sx, sy))
        self.sx, self.sy = self.img.get_size()
        self.sx = sx
        self.sy = sy
    
    def show(self): 
        screen.blit(self.img, (self.x, self.y))
```


```python
#충동 판정 함수
def crash(a, b):
    if a.x - b.sx < b.x and b.x < a.x+a.sx:
        if a.y - b.sy < b.y and b.y < a.y+a.sy:
            return True
        else:
            return False
    else:
        return False
    
    

```


```python
# 1 게임 초기화
pygame.init()

# 2 게임창 옵션 설정 
size = [500, 900]
screen = pygame.display.set_mode(size)

title = 'My Game'
pygame.display.set_caption(title)

# 3 게임 내 필요한 설정 
clock = pygame.time.Clock()


left_go = False
right_go = False
fire_go = False

target_file = './우주선4.png'
ss_size_x = 80
ss_size_y = 100

mm_file = './총알.png'
mm_size_x = 3
mm_size_y = 9

aa_file = './운석4.png'
aa_size_x = 40
aa_size_y = 70


ss = obj()
ss.put_img(target_file)
ss.change_size(ss_size_x, ss_size_y)
ss.x = size[0] // 2 - ss.sx // 2
ss.y = size[1] - ss.sy - 5


color = (0, 0, 0)

kill = 0
loss = 0

k = 0
GO = 0
```


```python
#게임 대기 화면
SB = 0
while SB == 0:
    clock.tick(60)
    #background 이미지 로딩 및 사이즈 조절
    bg = pygame.image.load('./우주_배경.jpg')
    bg = pygame.transform.scale(bg, (size[0], size[1]))
    
    screen.blit(bg, (0, 0))
    
    for event in pygame.event.get():
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                SB = 1

    #글자 화면 표시
    font = pygame.font.Font('C:/Windows/Fonts/ariblk.ttf', 15)
    text_kill = font.render(f'PRESS SPACE KEY TO START THE GAME.', True, (255,255,255))
    screen.blit(text_kill, (70,round(size[1] - 100)))
    pygame.display.flip()
    
    
# 4 메인 이벤트
SB = 0

m_list = []
a_list = []
k = 0

s_time = datetime.now()
while (SB==0):
    
    #background 이미지 로딩 및 사이즈 조절
    bg = pygame.image.load('./우주_배경.jpg')
    bg = pygame.transform.scale(bg, (size[0], size[1]))
    
    screen.blit(bg, (0, 0))
    
    
    # 4-1 FPS설정 
    clock.tick(60)
    # 4-2 각종 입력 감지 
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            SB = 1
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                left_go = True
            elif event.key == pygame.K_RIGHT:     
                right_go = True
            elif event.key == pygame.K_SPACE:
                if fire_go == False:
                    k = 0
                fire_go = True
                
        elif event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT:
                left_go = False
            elif event.key == pygame.K_RIGHT:
                right_go = False
            elif event.key == pygame.K_SPACE:
                fire_go = False\
#         if event.type == pygame.KEYDOWN:
#             if event.key == pygame.K_LEFT:
#                 ss.x = ss.x - 20
#                 if ss.x <= 0:
#                     ss.x = 0
#             elif event.key == pygame.K_RIGHT:
#                 ss.x = ss.x + 20
#                 if ss.x >= size[0] - ss_size_x:
#                     ss.x = size[0] - ss_size_x
                
    # 4-3입력 시간에 따른 변화
    now_time = datetime.now() 
    delta_time = (now_time - s_time).total_seconds()
                
    if left_go == True:
        ss.x -= 7
    if right_go == True:
        ss.x += 7
    
    if ss.x < 0:
        ss.x = size[0] - ss_size_x
#         print(ss.x)
    if ss.x > size[0] - ss_size_x:
        ss.x = 0
#         print(ss.x)

    
    # 미사일 생성 
    if fire_go == True and k % 5 == 0:
        mm= obj()
        mm.put_img(mm_file)
        mm.change_size(mm_size_x, mm_size_y)
        mm.x = ss.x + ss_size_x // 2 - mm_size_x  // 2
        mm.y = ss.y - 10
        
        m_list.append(mm)
    k += 1
#     print(f'k={k}')
    # 미사일 이동 및 화면밖 미사일 제거
    d_list = []
    for i in range(len(m_list)):
        m = m_list[i]
        m.y -= 15
        if m.y <= - mm_size_y:
            d_list.append(i)
    for d in d_list:
        del m_list[d]
    
        
    # 운석 생성
    if random.random() <= 0.02*5:
        aa= obj()
        aa.put_img(aa_file)
        aa.change_size(aa_size_x, aa_size_y)
        aa.x = random.randrange(0, size[0] - aa_size_x)
        aa.y = 0
        a_list.append(aa)
        # 운석 이동 및 화면밖 운석 제거
    z_list = []
    for z in range(len(a_list)):
        a = a_list[z]
        a.y += 3
        if a.y >= size[1]:
            z_list.append(z)
            loss += 1
    for z in z_list:
        del a_list[z]
    
    #운석과 미사일 충돌 판정
    dm_list = []
    da_list = []
    for i in range(len(m_list)):
        for j in range(len(a_list)):
            m = m_list[i]
            a = a_list[j]
            if crash(m,a) == True:
                dm_list.append(i)
                da_list.append(j)
                kill += 1
    dm_list = list(set(dm_list))
    dm_list.sort(reverse=True)
    da_list = list(set(da_list))
    da_list.sort(reverse=True)
    
    for dm in dm_list:
        del m_list[dm]
    for da in da_list:
        del a_list[da]
    
    for i in range(len(a_list)):
        a = a_list[i]
        if crash(a, ss) == True:
            SB = 1
            GO = 1
#             time.sleep(1)
        
    
    # 4-4 그리기
#     screen.fill(color)
    ss.show()
    for m in m_list:
        m.show()
    for a in a_list:
        a.show()
    
        
    #글자 화면 표시
    font = pygame.font.Font('C:/Windows/Fonts/ariblk.ttf', 20)
    text_kill = font.render(f'kiiled : {kill} loss : {loss}', True, (255,255,0))
    screen.blit(text_kill, (10,5))
    
    text_time = font.render(f'time : {int(delta_time)}', True, (255,255,255))
    screen.blit(text_time, (size[0] - 120,5))
    
            
        
    screen.blit(ss.img, (ss.x, ss.y)) #비행기 표시
    # 4-5 업데이트   
    pygame.display.flip()
    
# 5게임 종료
while GO == 1:
    clock.tick(60)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            GO = 0
    
    font = pygame.font.Font('C:/Windows/Fonts/ariblk.ttf', 40)
    text_kill = font.render(f'GAME OVER.', True, (255,0,0))
    screen.blit(text_kill, (110,round(size[1] / 2 - 50)))
    pygame.display.flip()
    
pygame.quit()
```
