# amazingtreasure
# encoding=utf-8
import tkinter as tk
import pygame, sys
import time
import random
import pygame.freetype
import math
import os
import datetime
import string
import copy
import numpy as np
import pickle
import inspect,re
def Tkinters():#点击“play"界面
    root = tk.Tk()
    root.geometry('1000x600')
    root.configure(bg='blue')
    root.title("进入游戏")
    playButton = tk.Button(root, text='play', height=60, font=('arial', 100), width=200, bg='red',
                           command=root.destroy)
    playButton.pack()
    root.mainloop()
def Initlist():#初始化数组
    global wall, treasure, keys, mo, skey, box
    wall = [[0 for col in range(12)] for row in range(16)]
    treasure = [0, 0, 0]
    keys = [0, 0]
    mo = [0, 0, 0, 0, 0, 0]
    skey = [0]
    box = [0]
def Loadpicture(pname):  # 加载图片函数(暂时从本机固定文件夹加载）
    pic = pygame.image.load("迷宫素材/" + pname)

    return pic
def LOADpicture():  # 加载图片
    # 加载墙壁图片
    for i in range(16):
        for j in range(12):
            wall[i][j] = Loadpicture(roles[20])
    # 加载宝箱图片
    for i in range(3):
        treasure[i] = Loadpicture(roles[10])
    # 加载钥匙图片
    for i in range(2):
        keys[i] = Loadpicture(roles[11])
    # 加载卫兵图片
    for i in range(6):
        mo[i] = Loadpicture(roles[6])
    skey[0] = Loadpicture(roles[12])
    box[0] = Loadpicture(roles[13])
def drawText(content, setting, bg, si):#输出界面文字信息
    pygame.font.init()
    font = pygame.freetype.Font('C://Windows//Fonts//msyh.ttc', 25)
    text_sf = font.render_to(screen, setting, content, fgcolor=(0, 30, 255), size=si)
    return text_sf
def makedir(path):#新建文件夹
    folder = os.path.exists(path)
    if not folder:
        os.makedirs(path)
def keyx():#地图随机位置
    for k in range(300):
        g = random.randrange(0, 16, 1)
        h = random.randrange(0, 12, 1)
        z = [g, h]
    return z

def saveList(themap,themap2,location,goldmap,keymap,monstermap,medicinemap,casenum):#保存随机产生的关卡数据，用来制造新的关卡
    np.savetxt("guanqia\\" +str(casenum) + varname(themap)+".txt",themap)
    np.savetxt("guanqia\\" +str(casenum) + varname(themap2) + ".txt", themap2)
    np.savetxt("guanqia\\" +str(casenum) + varname(location) + ".txt", location)
    np.savetxt("guanqia\\" +str(casenum) + varname(goldmap) + ".txt", goldmap)
    np.savetxt("guanqia\\" +str(casenum) + varname(keymap) + ".txt", keymap)
    np.savetxt("guanqia\\" +str(casenum) + varname(monstermap) +".txt", monstermap)
    np.savetxt("guanqia\\" +str(casenum) + varname(medicinemap) + ".txt", medicinemap)
def loadList(themap,themap2,location,goldmap,keymap,monstermap,medicinemap,casenum):#加载关卡数据
    themapx = np.loadtxt("guanqia\\" +str(casenum) + varname(themap)+".txt")
    themap2x = np.loadtxt("guanqia\\" +str(casenum) + varname(themap2) + ".txt")
    locationx = np.loadtxt("guanqia\\" +str(casenum) + varname(location) + ".txt")
    goldmapx = np.loadtxt("guanqia\\" +str(casenum) + varname(goldmap) + ".txt")
    keymapx = np.loadtxt("guanqia\\" +str(casenum) + varname(keymap) + ".txt")
    monstermapx = np.loadtxt("guanqia\\" +str(casenum) + varname(monstermap) + ".txt")
    medicinemapx= np.loadtxt("guanqia\\" +str(casenum) + varname(medicinemap) + ".txt")


    return themapx,themap2x,locationx,goldmapx,keymapx,monstermapx,medicinemapx
def varname(p):#获取数组名
    for line in inspect.getframeinfo(inspect.currentframe().f_back)[3]:
        m = re.search(r'\bvarname\s*\(\s*([A-Za-z_][A-Za-z0-9_]*)\s*\)', line)
        if m:
            return m.group(1)
def choosebeijing():#更换背景
    xxxx = random.randrange(1, 5, 1)
    if xxxx == 1:
        beijing = roles[23]

    if xxxx == 2:
        beijing = roles[22]
    if xxxx == 3:
        beijing = roles[21]
    if xxxx == 4:
        beijing = roles[24]
    return beijing
def Main():#游戏主函数
    global gamestate
    casenum=0
    pagestate = 5  # 主页动画
    Loadpicture("bitbug_favicon.ico")
    pygame.display.set_icon(Loadpicture("bitbug_favicon.ico"))
    while gamestate != 0:#游戏主循环
        musical = 'mazemusic.mp3'
        # 音乐
        pygame.mixer.music.load(musical)
        pygame.mixer.music.play()
        global speed
        speed = [0, 0]
        level = pygame.display.get_caption()
        themap2 = [[-1, -1], [0, -1], [1, -1], [2, -1], [3, -1], [4, -1], [5, -1], [6, -1], [7, -1], [8, -1], [9, -1],
                   [10, -1], [11, -1], [12, -1], [13, -1], [14, -1], [15, -1], [16, -1], \
                   [-1, 12], [0, 12], [1, 12], [2, 12], [3, 12], [4, 12], [5, 12], [6, 12], [7, 12], [8, 12], [9, 12],
                   [10, 12], [11, 12], [12, 12], [13, 12], [14, 12], [15, 12], [16, 12], \
                   [-1, 0], [-1, 1], [-1, 2], [-1, 3], [-1, 4], [-1, 5], [-1, 6], [-1, 7], [-1, 8], [-1, 9], [-1, 10],
                   [-1, 11], \
                   [16, 0], [16, 1], [16, 2], [16, 3], [16, 4], [16, 5], [16, 6], [16, 7], [16, 8], [16, 9], [16, 10],
                   [16, 11]]  # 墙壁地图位置坐标数组
        themap = []  # 墙壁地图位置坐标数组(不包括四周)

        # 箱子
        goldmap = [[-1 for col in range(3)] for row in
                   range(3)]  # goldmap=[[箱子图片treasure[i]，箱子坐标y=[p,q]，箱子号gold[i]],[,,],[,,]]

        for role in roles:  # 加载人物图片、右侧图片
            ball = Loadpicture(roles[0])
            side = Loadpicture(roles[9])
        screen.fill(BLACK)
        if gamestate == 1:
            #播放动图
            if pagestate > 0:
                screen.blit(Loadpicture(roles[16]), (0, 0))
                pagestate -= 1
            elif -4 < pagestate <= 0:
                screen.blit(Loadpicture(roles[17]), (0, 0))
                pagestate -= 1
            elif pagestate == -4:
                pagestate = 5
                screen.blit(Loadpicture(roles[17]), (0, 0))
            pygame.display.update()
            for event in pygame.event.get():
                if event.type == event.type == pygame.MOUSEBUTTONDOWN and 50 < event.pos[0] < 250 and 100 < event.pos[
                    1] < 200:
                    gamestate = 2.1  # 随机关卡
                if event.type == event.type == pygame.MOUSEBUTTONDOWN and 50 < event.pos[0] < 250 and 250 < event.pos[
                    1] < 350:
                    screen.blit(Loadpicture(roles[18]), (0, 0))
                    gamestate = 3#选关
                if event.type == pygame.QUIT:
                    gamestate == 1
                    pygame.quit()
                    sys.exit()

        if gamestate == 1.5:
            gamestate = 3
        if 2 <= gamestate <= 3:
            wallnumber = random.randrange(20, 70, 1)
            keynumber = random.randrange(20, 70, 1)
            monsternum = random.randrange(2, 12, 1)
            medicinenum = random.randrange(1, 5, 1)
            choice = random.randrange(1, 4, 1)#怪物类型
            choosestate=0#背景是否随重新开始更换（是否已按下更换背景）
            if choice == 1:
                themonster = roles[3]
            elif choice == 2:
                themonster = roles[4]
            else:
                themonster = roles[5]
            hasinmap = []
            while gamestate == 3:  # 选关

                goldlocate = []
                screen.blit(Loadpicture(roles[18]), (0, 0))
                for event in pygame.event.get():
                    # if event.type == pygame.MOUSEBUTTONDOWN and 400 < event.pos[0] < 600 and 150 < \
                    #         event.pos[1] < 200:  # easy
                    #     keynumber = 40
                    #     wallnumber = 40
                    #     monsternum = 4
                    #     medicinenum = 4
                    #     themonster = roles[3]
                    #
                    #     gamestate = 2
                    # if event.type == pygame.MOUSEBUTTONDOWN and 400 < event.pos[0] < 600 and 250 < \
                    #         event.pos[1] < 350:  # moderate
                    #     keynumber = 50
                    #     wallnumber = 50
                    #     monsternum = 6
                    #     medicinenum = 3
                    #     themonster = roles[4]
                    #
                    #     gamestate = 2
                    # if event.type == pygame.MOUSEBUTTONDOWN and 400 < event.pos[0] < 600 and 400 < \
                    #         event.pos[1] < 500:  # 3
                    #     keynumber = 70
                    #     wallnumber = 40
                    #     monsternum = 10
                    #     medicinenum = 2
                    #     themonster = roles[5]
                    #
                    #     gamestate = 2
                    after=[0 for col in range(16)]
                    for afternum in range(16):
                        px=afternum+1
                        after0 = np.loadtxt(("zuigaojilu\\第" + str(px) + "关.txt"))
                        after1= np.trunc(after0).astype(float).tolist()
                        after[afternum]=after1[0]
                        drawText("最高纪录"+str(after[afternum]),((afternum%4)*250+100,int(afternum/4)*150+100),(240, 66, 19),15)

                    if event.type == pygame.MOUSEBUTTONDOWN and 0 < event.pos[0] < 250 and 0 < \
                                     event.pos[1] < 150:  # easy
                        casenum=1
                        gamestate = 2
                        themonster = roles[3]
                    if event.type == pygame.MOUSEBUTTONDOWN and 250 < event.pos[0] < 500 and 0 < \
                                     event.pos[1] < 150:  # easy
                        casenum=2
                        gamestate = 2
                        themonster = roles[3]
                    if event.type == pygame.MOUSEBUTTONDOWN and 500 < event.pos[0] < 750 and 0 < \
                                     event.pos[1] < 150:  # easy
                        casenum=3
                        gamestate = 2
                        themonster = roles[3]
                    if event.type == pygame.MOUSEBUTTONDOWN and 750 < event.pos[0] < 1000 and 0 < \
                                     event.pos[1] < 150:  # easy
                        casenum=4
                        gamestate = 2
                        themonster = roles[3]
                    if event.type == pygame.MOUSEBUTTONDOWN and 0 < event.pos[0] < 250 and 150 < \
                                     event.pos[1] < 300:  # easy
                        casenum=5
                        gamestate = 2
                        themonster = roles[4]
                    if event.type == pygame.MOUSEBUTTONDOWN and 250 < event.pos[0] < 500 and 150 < \
                                     event.pos[1] < 300:  # easy
                        casenum=6
                        gamestate = 2
                        themonster = roles[4]
                    if event.type == pygame.MOUSEBUTTONDOWN and 500 < event.pos[0] < 750 and 150 < \
                                     event.pos[1] < 300:  # easy
                        casenum=7
                        gamestate = 2
                        themonster = roles[4]
                    if event.type == pygame.MOUSEBUTTONDOWN and  750< event.pos[0] < 1000 and 150 < \
                                     event.pos[1] < 300:  # easy
                        casenum=8
                        gamestate = 2
                        themonster = roles[4]
                    if event.type == pygame.MOUSEBUTTONDOWN and 0 < event.pos[0] < 250 and 300 < \
                                     event.pos[1] < 450:  # easy
                        casenum=9
                        gamestate = 2
                        themonster = roles[4]
                    if event.type == pygame.MOUSEBUTTONDOWN and 250 < event.pos[0] < 500 and 300 < \
                                     event.pos[1] < 450:  # easy
                        casenum=10
                        gamestate = 2
                        themonster = roles[4]
                    if event.type == pygame.MOUSEBUTTONDOWN and 500 < event.pos[0] < 750 and 300 < \
                                     event.pos[1] < 450:  # easy
                        casenum=11
                        gamestate = 2
                        themonster = roles[4]
                    if event.type == pygame.MOUSEBUTTONDOWN and 750 < event.pos[0] < 1000 and 300 < \
                                     event.pos[1] < 450:  # easy
                        casenum=12
                        gamestate=2
                        themonster = roles[4]
                    if event.type == pygame.MOUSEBUTTONDOWN and 0 < event.pos[0] < 250 and 450 < \
                                     event.pos[1] < 600:  # easy
                        casenum=13
                        gamestate=2
                        themonster = roles[5]
                    if event.type == pygame.MOUSEBUTTONDOWN and 250 < event.pos[0] < 500 and 450 < \
                                     event.pos[1] < 600:  # easy
                        casenum=14
                        gamestate=2
                        themonster = roles[5]
                    if event.type == pygame.MOUSEBUTTONDOWN and 500 < event.pos[0] < 750 and 450 < \
                                     event.pos[1] < 600:  # easy
                        casenum=15
                        gamestate=2
                        themonster = roles[5]
                    if event.type == pygame.MOUSEBUTTONDOWN and 750 < event.pos[0] < 1000 and 450 < \
                                     event.pos[1] < 600:  # easy
                        casenum=16
                        gamestate=2
                        themonster = roles[5]
                    if event.type == pygame.QUIT:
                        gamestate == 1
                        pygame.quit()
                        sys.exit()
                    pygame.display.update()

            # 建立地图
            if gamestate==2.1:
                # 随机地图
                for k in range(wallnumber):
                    i = random.randrange(0, 16, 1)
                    j = random.randrange(0, 12, 1)
                    x = [i, j]
                    if x in themap:
                        k = k - 1
                        continue
                    themap.append(x)
                    hasinmap.append(x)
                themap2.extend(themap)


                # 摆人物
                for k in range(100):
                    i = random.randrange(0, 16, 1)
                    j = random.randrange(0, 12, 1)
                    x = [i, j]
                    x0 = x
                    if x not in themap:
                        hasinmap.append(x)
                        break
                location = [i * 50, j * 50]

                # 箱子计数
                dd = 0
                # 摆箱子
                for k in range(300):
                    p = random.randrange(0, 16, 1)
                    q = random.randrange(0, 12, 1)
                    y = [p, q]
                    if y not in hasinmap and p != 0 and p != 15 and q != 0 and q != 11:
                        hasinmap.append(y)
                        screen.blit(treasure[dd], (p * 50, q * 50))
                        goldmap[dd][0] = p
                        goldmap[dd][1] = q
                        goldmap[dd][2] = dd
                        dd += 1
                        if dd == 3:
                            break

                # 摆钥匙
                keymap = [[-1, -1] for row in range(keynumber)]
                zz = 0  # 钥匙计数
                breaks = 0

                keyflat = 0

                for key in keymap:
                    for month in range(300):
                        z = keyx()
                        g, h = z[0], z[1]
                        breaks = 0
                        if z not in hasinmap:

                            hasinmap.append(z)
                            keymap.remove(keymap[0])
                            keymap.append(z)

                            screen.blit(Loadpicture(roles[11]), (g * 50, h * 50))
                            zz += 1
                            breaks = 1
                        if breaks == 1:

                            break

                    if zz == keynumber:
                        break


                # 摆怪物
                monstermap = [0 for col in range(monsternum)]
                zzz = 0  # 怪物计数
                for monster in monstermap:
                    for mon in range(300):
                        qq = keyx()
                        g, h = qq[0], qq[1]
                        breaks = 0
                        if qq not in hasinmap:
                            hasinmap.append(qq)
                            monstermap.remove(monstermap[0])
                            monstermap.append(qq)

                            zzz += 1
                            break
                    if zzz == monsternum:
                        break


                medicinemap = [0 for col in range(medicinenum)]

                yyy = 0  # 药瓶计数
                for medicine in medicinemap:
                    for mon in range(300):
                        yy = keyx()
                        g, h = yy[0], yy[1]
                        breaks = 0
                        if yy not in hasinmap:
                            hasinmap.append(yy)
                            medicinemap.append(yy)
                            medicinemap.remove(medicinemap[0])
                            yyy += 1
                            break
                    if yyy == medicinenum:
                        break
                casenum=0

            step = 0
            health = 100
            c = 0
            e = 0
            has = False
            boxside = False
            xx = 0
            yourstate = "精力充沛"
            state = ""
            goldpic = Loadpicture(roles[10])
            t1 = time.time()
            tx = 1
            broke=False#是否破纪录
            # print("themap=",themap)
            # print("themap2=",themap2)
            # print("location=",location)
            # print("goldmap=",goldmap)
            # print("keymap=",keymap)
            if casenum!=0:
                themap=[]
                themap2=[]
                location=[]
                goldmap=[]
                keymap=[]
                monstermap=[]
                medicinemap=[]
                # saveList(themap,themap2,location,goldmap,keymap,monstermap,medicinemap,casenum)
                themap, themap2, location, goldmap, keymap, monstermap, medicinemap=\
                    loadList(themap,themap2,location,goldmap,keymap,monstermap,medicinemap,casenum)

                themap = np.trunc(themap).astype(int).tolist()

                themap2 = np.trunc(themap2).astype(int).tolist()

                location = np.trunc(location).astype(int).tolist()

                goldmap =np.trunc(goldmap).astype(int).tolist()

                keymap = np.trunc(keymap).astype(int).tolist()

                monstermap = np.trunc(monstermap).astype(int).tolist()

                medicinemap = np.trunc(medicinemap).astype(int).tolist()

            themapcopy = copy.deepcopy(themap)
            themap2copy = copy.deepcopy(themap2)
            locationcopy = copy.deepcopy(location)
            goldmapcopy = copy.deepcopy(goldmap)
            keymapcopy = copy.deepcopy(keymap)
            monstermapcopy = copy.deepcopy(monstermap)
            medicinemapcopy = copy.deepcopy(medicinemap)
        while 2 <= gamestate < 3:#游戏进行界面主循环



            #保存关卡
            # print(goldmap)
            # np.savetxt("guanqia\\goldmap1.txt",goldmap)
            # goldmap2=np.loadtxt("guanqia\\1.txt")
            # print(goldmap2)
            # print(themap)
            # np.savetxt("guanqia\\themap1.txt",themap)
            # themap0=np.loadtxt("guanqia\\themap1.txt")
            # print(themap0)


            if gamestate == 2.7:
                gamestate = 0
            if gamestate == 2.6:
                health = 100
                step = 0
                c = 0
                e = 0
                has = False
                boxside = False
                xx = 0
                yourstate = "精力充沛"
                state = ""

                t1 = time.time()
                tx = 1
                goldpic = Loadpicture(roles[10])
                themap = copy.deepcopy(themapcopy)
                themap2 = copy.deepcopy(themap2copy)
                location = copy.deepcopy(locationcopy)
                goldmap = copy.deepcopy(goldmapcopy)

                keymap = copy.deepcopy(keymapcopy)
                monstermap = copy.deepcopy(monstermapcopy)
                medicinemap = copy.deepcopy(medicinemapcopy)
                gamestate = 2
            beijingnum=24




            while 2 <= gamestate < 2.5:#未点击任何文字时的主循环
                beijing = roles[beijingnum]
                screen.blit(Loadpicture(beijing),(0,0))
                if health == 100:
                    yourstate = "精力充沛"
                elif 0 < health < 100:
                    yourstate = "受伤"

                for gold in goldmap:

                    screen.blit(goldpic, (gold[0] * 50, gold[1] * 50))


                for z in keymap:

                    if c < 5:
                        screen.blit(keys[0], (z[0] * 50, z[1] * 50))
                    elif c == 5:
                        screen.blit(Loadpicture("wall.jpg"), (z[0] * 50, z[1] * 50))
                for monster in monstermap:

                    screen.blit(Loadpicture(themonster), (monster[0] * 50, monster[1] * 50))
                for medicine in medicinemap:

                    screen.blit(Loadpicture(roles[19]), (medicine[0] * 50, medicine[1] * 50))
                localtime = time.strftime("%Y-%m-%d %H:%M:%S", time.localtime())

                pygame.display.set_caption("挑战时间" + localtime)
                screen.blit(side, (800, 0))

                i = location[0] / 50
                j = location[1] / 50
                for event in pygame.event.get():
                    if event.type == pygame.QUIT:
                        gamestate == 1
                        pygame.quit()
                        sys.exit()
                    # 键盘事件
                    if event.type == pygame.KEYDOWN and gamestate!=2.4 and gamestate!=2.45:
                        # 上
                        if event.key == pygame.K_UP and [i, j - 1] not in themap2 \
                                and (([i,j-1]!=[goldmap[0][0],goldmap[0][1]] and [i,j-1]!=[goldmap[1][0],goldmap[1][1]] \
                                and [i,j-1]!=[goldmap[2][0],goldmap[2][1]]) or (has or [i,j-2] in themap2) )\
                                and ([i, j - 1] not in keymap or c < 5)\
                                :  # 上
                            speed[1] = -50
                            j -= 1
                            step += 1
                        if event.key == pygame.K_UP and [i, j - 1] not in themap2 \
                                and ( ([i, j - 1] == goldmap[0][1] or [i,j - 1] ==goldmap[1][1] or [i, j - 1] ==goldmap[2][1]) and not has):
                            xx = xx * 2 + 1
                        for gold in goldmap:
                            if event.key == pygame.K_UP and [i, j] ==[gold[0],gold[1]] \
                                    and [i, j - 1] not in themap2\
                                    and ([i,j - 1] != [goldmap[0][0],goldmap[0][1]] and [i,j - 1] != [goldmap[1][0],goldmap[1][1]] and [i,j - 1] != [goldmap[2][0],goldmap[2][1]]) \
                                    and ([i, j - 1] not in keymap or c < 5)\
                                    and has :

                                gold[0],gold[1] = [i, j - 1]
                                c -= 1
                            # 消耗钥匙
                        # 下
                        if event.key == pygame.K_DOWN and [i, j + 1] not in themap2 \
                                and (([i, j + 1] != [goldmap[0][0],goldmap[0][1] ]and [i, j + 1] != [goldmap[1][0],goldmap[1][1] ]\
                                and [i, j + 1] !=[goldmap[2][0],goldmap[2][1] ]) or (has or [i,j+2] in themap2)) \
                                and ([i, j + 1] not in keymap or c < 5)\
                                :  # 下
                            speed[1] = 50
                            j += 1
                            step += 1
                        if event.key == pygame.K_DOWN and [i, j + 1] not in themap2\
                                and (([i, j + 1] == goldmap[0][1] or [i,j + 1] ==goldmap[1][1] or [i, j + 1] ==goldmap[2][1]) and not has):
                            xx = xx * 2 + 1
                        for gold in goldmap:
                            if event.key == pygame.K_DOWN and [i, j] ==[gold[0],gold[1]] \
                                    and [i, j + 1] not in themap2\
                                    and [i,j + 1] != [goldmap[0][0],goldmap[0][1]] and [i,j + 1] != [goldmap[1][0],goldmap[1][1]] and [i,j + 1] != [goldmap[2][0],goldmap[2][1]] \
                                    and ([i, j + 1] not in keymap or c < 5) \
                                    and has :
                                gold[0],gold[1] = [i, j + 1]
                                c -= 1
                        # 左
                        if event.key == pygame.K_LEFT and [i-1, j] not in themap2 \
                                and (([i-1,j]!=[goldmap[0][0],goldmap[0][1]] and [i-1,j]!=[goldmap[1][0],goldmap[1][1]] \
                                and [i-1,j]!=[goldmap[2][0],goldmap[2][1]]) or (has or [i-2,j] in themap2))  \
                                and ([i-1, j ] not in keymap or c < 5)\
                                :  # 左
                            speed[0] = -50
                            i -= 1
                            step += 1
                        if event.key == pygame.K_LEFT and [i-1, j ] not in themap2 \
                                and ( ([i - 1, j] == goldmap[0][1] or [i - 1,j] ==goldmap[1][1] or [i - 1, j] ==goldmap[2][1]) and not has):
                            xx = xx * 2 + 1
                        for gold in goldmap:
                            if event.key == pygame.K_LEFT and [i, j] ==[gold[0],gold[1]] \
                                    and [i-1, j] not in themap2\
                                    and [i-1,j ] != [goldmap[0][0],goldmap[0][1]] and [i-1,j ]  != [goldmap[1][0],goldmap[1][1]] and [i-1,j ]  != [goldmap[2][0],goldmap[2][1]] \
                                    and ([i-1, j ] not in keymap or c < 5) \
                                    and has :
                                gold[0],gold[1] = [i-1, j]
                                c -= 1
                        # 右
                        if event.key == pygame.K_RIGHT  and [i+1, j] not in themap2 \
                                and (([i+1,j]!=[goldmap[0][0],goldmap[0][1]] and [i+1,j]!=[goldmap[1][0],goldmap[1][1]]\
                                and [i+1,j]!=[goldmap[2][0],goldmap[2][1]]) or (has or [i+2,j] in themap2))\
                                and ([i+1, j ] not in keymap or c < 5)\
                                : # 右
                            speed[0] = 50
                            i += 1
                            step += 1
                        if event.key == pygame.K_RIGHT and [i+1, j] not in themap2\
                                and (([i + 1, j] == goldmap[0][1] or [i + 1, j] == goldmap[1][1] or [i + 1, j] == goldmap[2][1]) and not has):
                            xx = xx * 2 + 1
                        for gold in goldmap:
                            if event.key == pygame.K_RIGHT and [i, j] ==[gold[0],gold[1]] \
                                    and [i+1, j] not in themap2\
                                    and [i+1,j ] != [goldmap[0][0],goldmap[0][1]] and [i+1,j ]  != [goldmap[1][0],goldmap[1][1]] and [i+1,j ]  != [goldmap[2][0],goldmap[2][1]] \
                                    and ([i+1, j ] not in keymap or c < 5) \
                                    and has :
                                gold[0],gold[1] = [i+1, j ]
                                c -= 1
                        if event.key == pygame.K_ESCAPE:
                            on = False
                            pygame.quit()
                            sys.exit()
                    # 鼠标事件
                    if event.type == pygame.MOUSEBUTTONDOWN and 810 < event.pos[0] < 914 and 450 < event.pos[1] < 476:
                        gamestate = 1.5  # “主页”控件
                    elif event.type == pygame.MOUSEBUTTONDOWN and 940 < event.pos[0] < 992 and 450 < event.pos[1] < 476:
                        gamestate = 1  # “换个地图”控件
                    elif event.type == pygame.MOUSEBUTTONDOWN and 810 < event.pos[0] < 992 and 550 < event.pos[1] < 576:
                        gamestate = 2.6  # “重新开始”控件
                    elif event.type == pygame.MOUSEBUTTONDOWN and 900 < event.pos[0] < 980 and 30 < event.pos[1] < 50:
                        pygame.quit()#“退出游戏”控件
                        sys.exit()
                    elif event.type == pygame.MOUSEBUTTONDOWN and 810 < event.pos[0] < 890 and 30 < event.pos[1] < 50:
                        beijingnum-=1
                        if beijingnum==20:
                            beijingnum=24
                location = [i * 50, j * 50]
                speed = [0, 0]

                drawText(state, (810, 300), (240, 66, 19), 20)

                if tx == 0:
                    tscore =round(float(tscore),2)
                    pscore = round(float(pscore),2)
                    aa = 666 - tscore - pscore
                    totle = format(aa, '.2f')
                    totlestring=np.array([str(totle),str(tscore),str(pscore)])

                    drawText("评分:" + str(totle), (810, 60), (240, 66, 19), 15)
                    if broke==True:
                        drawText("破纪录了！", (810, 80), (240, 66, 19), 15)
                        broke=False
                    #themap = np.trunc(themap).astype(int).tolist()


                    if casenum!=0:
                        before = np.loadtxt(("zuigaojilu\\第" + str(casenum) + "关.txt"))
                        before0 = np.trunc(before).astype(float).tolist()
                        sum,tscore0,pscore0 = before0[0],before0[1],before0[2]
                        if float(sum)<float(totle):
                            np.savetxt(("zuigaojilu\\第" + str(casenum) + "关.txt"), totlestring, fmt='%s')
                            broke=True
                drawText("更换背景", (810, 30), (240, 66, 19), 20)
                drawText("退出游戏",  (900, 30), (240, 66, 19), 20)
                drawText("生命值:" + str(health), (810, 350), (18, 161, 18), 30)
                drawText("步数:" + str(step), (810, 400), (18, 161, 18), 30)
                drawText("换个地图", (810, 450), (43, 38, 231), 26)
                drawText("主页", (940, 450), (43, 38, 231), 26)
                drawText("状态:" + yourstate, (810, 490), (18, 161, 18), 15)
                drawText("钥匙:", (810, 520), (18, 161, 18), 15)
                drawText("重新开始", (810, 550), (43, 38, 231), 26)
                if themonster==roles[3]:
                    jian=33
                elif themonster==roles[4]:
                    jian=44
                elif themonster==roles[5]:
                    jian=99

                for monster in monstermap:
                    if [i, j] == monster and health - jian > 0:
                        monstermap.remove(monster)

                        health -= jian
                    elif [i, j] == monster and health - jian <= 0:
                        yourstate = "defeat"
                        health -= jian
                        monstermap.remove(monster)
                        gamestate = 2.4

                        break
                if gamestate == 2.4:
                    state = "挑战失败"
                if gamestate != 2.4:
                    screen.blit(ball, (location[0], location[1]))
                for medicine in medicinemap:
                    if [i, j] == medicine:
                        medicinemap.remove(medicine)
                        health = 100

                if xx % 2 == 1:
                    drawText("提示信息:没有钥匙！", (810, 580), (18, 161, 18), 12)
                # 捡钥匙
                for key in keymap:
                    if [i, j] == key and c >= 5:
                        drawText("提示信息:最多同时有5把钥匙！", (810, 580), (18, 161, 18), 12)
                    if [i, j] == key and c < 5:
                        c += 1
                        d = c
                        keymap.remove(key)
                # 屏幕右侧显示钥匙数
                for d in range(c):
                    screen.blit(skey[0], (850 + d * 12, 520))
                if c > 0:
                    has = True
                    xx = xx * 2
                if ((abs(goldmap[0][0] * 50 - goldmap[1][0] * 50) + abs(
                        goldmap[0][0] * 50 - goldmap[2][0] * 50) == 50 and \
                     abs(goldmap[0][1] * 50 - goldmap[1][1] * 50) + abs(
                            goldmap[0][1] * 50 - goldmap[2][1] * 50) == 50) or \
                    (abs(goldmap[1][0] * 50 - goldmap[0][0] * 50) + abs(
                        goldmap[1][0] * 50 - goldmap[2][0] * 50) == 50 and \
                     abs(goldmap[1][1] * 50 - goldmap[0][1] * 50) + abs(
                                goldmap[1][1] * 50 - goldmap[2][1] * 50) == 50) or \
                    (abs(goldmap[2][0] * 50 - goldmap[1][0] * 50) + abs(
                        goldmap[2][0] * 50 - goldmap[0][0] * 50) == 50 and \
                     abs(goldmap[2][1] * 50 - goldmap[1][1] * 50) + abs(
                                goldmap[2][1] * 50 - goldmap[0][1] * 50) == 50)) and tx == 1:
                    t2 = time.time()
                    state = "恭喜通关,用时" + str(format(t2 - t1, '.2f'))
                    tscore = round(t2 - t1,2)
                    pscore = step
                    tx = 0
                    gamestate = 2.45
                    for gold in goldmap:
                        goldpic = Loadpicture(roles[15])

                for d in range(e):
                    screen.blit(box[0], (850 + d * 12, 490))
                if c <= 0:
                    has = False

                # 刷新墙壁

                for xmap in themap:
                    screen.blit(wall[xmap[0]][xmap[1]], (xmap[0] * 50, xmap[1] * 50))
                if gamestate==2.4:
                    screen.blit(Loadpicture(roles[25]),(0,0))
                pygame.display.update()
                fclock.tick(fps)  # sui随机#
# 进入游戏窗口
Tkinters()
pygame.init()
pygame.mixer.init()
BLACK = 255, 69, 0
# role数组【0角色1大史莱姆2蝙蝠3骷髅4骷髅队长5魔王6魔王卫士7兽人8小史莱姆9右侧图片10箱子图片
# 11钥匙12钥匙图标13宝箱图标14黄色门15金条16mainpage17mainpage2 18选关界面19红药瓶20墙21陈瑶22背景二23背景24纯色背景三25lose
roles = ["littleman.png", "魔塔素材_2.jpg", "魔塔素材_bianfu1.jpg", "魔塔素材_kulou.png", \
         "魔塔素材_kuloumonitor.png", "魔塔素材_mowang.png", "魔塔素材_mowei.jpg", "魔塔素材_shouren.jpg", "魔塔素材_副本.jpg", \
         "side.png", "treasure.png", 'thekey.png', "魔塔素材_yellowkey_副本.png", "treasure_副本.png", \
         "魔塔素材_yellowdoor.jpg", "goldmetal.png", "mainpage.gif", "mainpage2.gif", "xuanguan (2).png",\
         "魔塔素材_redmedicine.png","wall.jpg","beijing4.jpg","beijing3.jpg","beijing1.jpg","chunbeijing.png","lose.png"]  #
global wall, treasure, keys, mo, skey
Initlist()
LOADpicture()
fps = 120
fclock = pygame.time.Clock()
size = width, height = 1000, 600
screen = pygame.display.set_mode(size)

# 主函数
gamestate = 1  # 游戏界面循环
Main()
