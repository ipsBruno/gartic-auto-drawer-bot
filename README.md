'''
 Hack para Gartic
 Criado por Bruno da Silva
 Agradecimentos LLM Tutoriais pelo código inicial

 Requerimentos:
        1: Python 3
        2: Lightshot

        Extensões (rode com comando de linha após instalar python)
        pip install pyautogui
        pip install pillow
        pip install pynput
'''

from PIL import Image as img
from time import sleep, time
from datetime import datetime
from math import sqrt
from pynput.mouse import Controller
from PIL import ImageGrab
import pyautogui
import keyboard
import os.path
import uuid
import keyboard as kb
import winsound

esc_hue = 360 / 180
esc_sat = 100 / 100
esc_val = 100 / 100
notHaveFiles = 0
canvas = [0,0]
roda_paleta = [0,0]
cruz_paleta = [0,0]
baixo_barra = [0,0]
lapis = [0,0]


def RGB_HSV(R, G, B):
    RR, GG, BB = R / 255, G / 255, B / 255
    Cmax = max(RR, GG, BB)
    Cmin = min(RR, GG, BB)
    Delta = Cmax - Cmin
    if Cmax == RR:
        try:
            H = ((GG - BB) / (Cmax - Cmin)) * 60
        except ZeroDivisionError:
            H = 0
    elif Cmax == GG:
        try:
            H = (2.0 + (BB - RR) / (Cmax - Cmin)) * 60
        except ZeroDivisionError:
            H = 120
    elif Cmax == BB:
        try:
            H = (4 + (RR - GG) / (Cmax - Cmin)) * 60
        except ZeroDivisionError:
            H = 240
    else:
        print('Erro!')
    if H < 0:
        H += 360
    H = f'{H:.0f}'
    try:
        S = f'{(Delta / Cmax) * 100:.1f}'
    except ZeroDivisionError:
        S = 0
    V = f'{Cmax * 100:.1f}'
    HSV_f = (float(H), float(S), float(V))
    HSV_r = (round(HSV_f[0]), round(HSV_f[1]), round(HSV_f[2]))
    HSV = HSV_r
    return HSV


def closest_color(r,g,b):
    COLORS = [
    (0,0,0),(128,0,0),(0,128,0),(128,128,0),(0,0,128),(128,0,128),(0,128,128),(192,192,192),(128,128,128),(255,0,0),(0,255,0),(255,255,0),(0,0,255),(255,0,255),(0,255,255),(255,255,255),(0,0,0),(0,0,95),(0,0,135),(0,0,175),(0,0,215),(0,0,255),(0,95,0),(0,95,95),(0,95,135),(0,95,175),(0,95,215),(0,95,255),(0,135,0),(0,135,95),(0,135,135),(0,135,175),(0,135,215),(0,135,255),(0,175,0),(0,175,95),(0,175,135),(0,175,175),(0,175,215),(0,175,255),(0,215,0),(0,215,95),(0,215,135),(0,215,175),(0,215,215),(0,215,255),(0,255,0),(0,255,95),(0,255,135),(0,255,175),(0,255,215),(0,255,255),(95,0,0),(95,0,95),(95,0,135),(95,0,175),(95,0,215),(95,0,255),(95,95,0),(95,95,95),(95,95,135),(95,95,175),(95,95,215),(95,95,255),(95,135,0),(95,135,95),(95,135,135),(95,135,175),(95,135,215),(95,135,255),(95,175,0),(95,175,95),(95,175,135),(95,175,175),(95,175,215),(95,175,255),(95,215,0),(95,215,95),(95,215,135),(95,215,175),(95,215,215),(95,215,255),(95,255,0),(95,255,95),(95,255,135),(95,255,175),(95,255,215),(95,255,255),(135,0,0),(135,0,95),(135,0,135),(135,0,175),(135,0,215),(135,0,255),(135,95,0),(135,95,95),(135,95,135),(135,95,175),(135,95,215),(135,95,255),(135,135,0),(135,135,95),(135,135,135),(135,135,175),(135,135,215),(135,135,255),(135,175,0),(135,175,95),(135,175,135),(135,175,175),(135,175,215),(135,175,255),(135,215,0),(135,215,95),(135,215,135),(135,215,175),(135,215,215),(135,215,255),(135,255,0),(135,255,95),(135,255,135),(135,255,175),(135,255,215),(135,255,255),(175,0,0),(175,0,95),(175,0,135),(175,0,175),(175,0,215),(175,0,255),(175,95,0),(175,95,95),(175,95,135),(175,95,175),(175,95,215),(175,95,255),(175,135,0),(175,135,95),(175,135,135),(175,135,175),(175,135,215),(175,135,255),(175,175,0),(175,175,95),(175,175,135),(175,175,175),(175,175,215),(175,175,255),(175,215,0),(175,215,95),(175,215,135),(175,215,175),(175,215,215),(175,215,255),(175,255,0),(175,255,95),(175,255,135),(175,255,175),(175,255,215),(175,255,255),(215,0,0),(215,0,95),(215,0,135),(215,0,175),(215,0,215),(215,0,255),(215,95,0),(215,95,95),(215,95,135),(215,95,175),(215,95,215),(215,95,255),(215,135,0),(215,135,95),(215,135,135),(215,135,175),(215,135,215),(215,135,255),(215,175,0),(215,175,95),(215,175,135),(215,175,175),(215,175,215),(215,175,255),(215,215,0),(215,215,95),(215,215,135),(215,215,175),(215,215,215),(215,215,255),(215,255,0),(215,255,95),(215,255,135),(215,255,175),(215,255,215),(215,255,255),(255,0,0),(255,0,95),(255,0,135),(255,0,175),(255,0,215),(255,0,255),(255,95,0),(255,95,95),(255,95,135),(255,95,175),(255,95,215),(255,95,255),(255,135,0),(255,135,95),(255,135,135),(255,135,175),(255,135,215),(255,135,255),(255,175,0),(255,175,95),(255,175,135),(255,175,175),(255,175,215),(255,175,255),(255,215,0),(255,215,95),(255,215,135),(255,215,175),(255,215,215),(255,215,255),(255,255,0),(255,255,95),(255,255,135),(255,255,175),(255,255,215),(255,255,255),(8,8,8),(18,18,18),(28,28,28),(38,38,38),(48,48,48),(58,58,58),(68,68,68),(78,78,78),(88,88,88),(98,98,98),(108,108,108),(118,118,118),(128,128,128),(138,138,138),(148,148,148),(158,158,158),(168,168,168),(178,178,178),(188,188,188),(198,198,198),(208,208,208),(218,218,218),(228,228,228),(238,238,238)]
    color_diffs = []
    for color in COLORS:
        cr, cg, cb = color
        color_diff = sqrt(abs(r - cr)**2 + abs(g - cg)**2 + abs(b - cb)**2)
        color_diffs.append((color_diff, color))
    return min(color_diffs)[1]


def restart_app():
    winsound.Beep(300, 700)
    import sys
    os.execl(sys.executable, sys.executable, *sys.argv)


def ler_file(file):
    with open(file) as f:
        return int(f.read())

def on_triggered():
    mouse = Controller()
    (x,y) = mouse.position

    global lapis, canvas, roda_paleta, cruz_paleta, baixo_barra
    if((lapis[0]) == 0):
        lapis = [x,y]
        f= open("configs/lapisX.log","w");    f.write(("%d" % x));      f.close();
        f= open("configs/lapisY.log","w");    f.write(("%d" % y));      f.close();
        print ("Posicione o cursor no canto superior esquerdo do QUADRO de DESENHO e dê ALT+X")
        winsound.Beep(3000, 700)
        return True
    if((canvas[0]) == 0):
        canvas = [x,y]
        print ("Posicione o cursor em cima do icone de PALETA e dê ALT+X")
        winsound.Beep(3000, 700)
        f= open("configs/canvasX.log","w");    f.write(("%d" % x));      f.close();
        f= open("configs/canvasY.log","w");    f.write(("%d" % y));      f.close();
        return True
    if((roda_paleta[0]) == 0):
        print ("Posicione o cursor em cima do icone de CRUZ da PALETA e dê ALT+X")
        winsound.Beep(3000, 700)
        f= open("configs/paletaX.log","w");    f.write(("%d" % x));      f.close();
        f= open("configs/paletaY.log","w");    f.write(("%d" % y));      f.close();
        roda_paleta = [x,y]
        return True
    if((cruz_paleta[0]) == 0):
        print ("Posicione o cursor na parte inferior da barra da paleta e dê ALT+X")
        winsound.Beep(3000, 700)
        f= open("configs/cruzX.log","w");    f.write(("%d" % x));      f.close();
        f= open("configs/cruzY.log","w");    f.write(("%d" % y));      f.close();
        cruz_paleta = [x,y]
        return True
    if((baixo_barra[0]) == 0):
        print ("\n\n\n\nPOSIÇÃO DOS BOTÕES ARMAZENADA COM SUCESSO\nNAS PROXIMAS EXECUCOES VOCE NAO PRECISARA MAIS CONFIGURAR O BOT COM ALT+X\nCASO OCORRA ALGUM ERRO NO BOT RECONFIGURE ELE APAGANDO OS ARQUIVOS DA PASTA CONFIG\n\n\n\n")
        winsound.Beep(3000, 1500)
        baixo_barra = [x,y]
        f= open("configs/barraX.log","w");    f.write(("%d" % x));      f.close();
        f= open("configs/barraY.log","w");    f.write(("%d" % y));      f.close();
        return True


if os.path.exists('configs/barraY.log'):
    lapis[0] = ler_file('configs/lapisX.log')
    lapis[1] = ler_file('configs/lapisY.log')
    canvas[0] = ler_file('configs/canvasX.log')
    canvas[1] = ler_file('configs/canvasY.log')
    roda_paleta[0] = ler_file('configs/paletaX.log')
    roda_paleta[1] = ler_file('configs/paletaY.log')
    cruz_paleta[0] = ler_file('configs/cruzX.log')
    cruz_paleta[1] = ler_file('configs/cruzY.log')
    baixo_barra[0] = ler_file('configs/barraX.log')
    baixo_barra[1] = ler_file('configs/barraY.log')
    notHaveFiles = 1

    winsound.Beep(1000, 200)
    print('\n\n\nBOT INICIADO COM SUCESSO!\n\n\nPor Bruno Silva\n\nwww.brunodasilva.com\n\n\nCopie seu desenho e aperta CTRL + B\n\n')
    keyboard.add_hotkey('ctrl+i', restart_app)

else:
    print("\n\n\nANTES DE USAR CONFIGURE O BOT\n\n\n")
    print ("Posicione o cursor em CIMA do LÁPIS e dê ALT+X\n\n\n")
    winsound.Beep(3000, 700)
    keyboard.add_hotkey('alt+x', on_triggered)


def on_triggered_screen():
    if notHaveFiles == 0:
        winsound.Beep(600, 1000)
        print("Configure o BOT direito antes de dar CTRL + B")
        restart_app()
    print("Imagem salva com sucesso!")
    im = ImageGrab.grabclipboard()
    global notHaveFiles
    filename =('cliparts/%s.png' %  str(uuid.uuid4()))
    try:
        im.thumbnail((90,90), img.ANTIALIAS)
    except:
        winsound.Beep(600, 1000)
        print("Erro na imagem copiada, tente copiar e dar CTRL + B novamente")
        restart_app()
    im.save(filename, "PNG")
    imagem = img.open(filename)
    largura, altura = imagem.size
    total_de_pixels = largura * altura
    winsound.Beep(3500, 100)
    print ('Mapeando imagem ...')
    imageMapColor = {}
    imageMapPixels = {}
    for y in range(altura):
        for x in range(largura):
            pixel = imagem.getpixel((x, y))
            R, G, B = pixel[0], pixel[1], pixel[2]
            R,G,B = closest_color(R,G,B)
            rgb = "%d,%d,%d" % (R,G,B);
            pixel = "%d_%d" % (x,y);
            if rgb not in imageMapColor.keys():
                imageMapColor[rgb] = []
            imageMapColor[rgb].append([x,y])
            if pixel not in imageMapPixels.keys():
                imageMapPixels[pixel] = []
            imageMapPixels[pixel] = rgb
    print ('Imagem mapeada com sucesso')
    winsound.Beep(1500, 100)
    pyautogui.PAUSE = 1/1000
    for rgb in imageMapColor.keys():
        R, G, B = rgb.split(',')
        if int(R) > 215 and int(G) > 215 and int(B) > 215:
            continue
        HSV = RGB_HSV(int(R),int(G),int(B))
        Hue, Saturation, Value = HSV[0], HSV[1], HSV[2]
        pyautogui.click(roda_paleta)
        x_cor = cruz_paleta[0] + (Hue / esc_hue)
        y_cor = cruz_paleta[1] - (Saturation / esc_sat)
        pyautogui.click(x_cor, y_cor)
        x_luminosidade = baixo_barra[0]
        y_luminosidade = baixo_barra[1] - (Value / esc_val)
        pyautogui.click(x_luminosidade, y_luminosidade)
        conta = 0
        while(conta < len(imageMapColor[rgb])):
            if kb.is_pressed("ctrl+i"):
                restart_app()
            if(("{0}_{1}".format(imageMapColor[rgb][conta][0]+1, imageMapColor[rgb][conta][1]) in imageMapPixels and
            imageMapPixels["{0}_{1}" .format( imageMapColor[rgb][conta][0], imageMapColor[rgb][conta][1])] != imageMapPixels["{0}_{1}".format(imageMapColor[rgb][conta][0]+1, imageMapColor[rgb][conta][1])])
            or
            ("{0}_{1}".format(imageMapColor[rgb][conta][0]-1, imageMapColor[rgb][conta][1]) in imageMapPixels and
            imageMapPixels["{0}_{1}" .format( imageMapColor[rgb][conta][0], imageMapColor[rgb][conta][1])] != imageMapPixels["{0}_{1}".format(imageMapColor[rgb][conta][0]-1, imageMapColor[rgb][conta][1])])
            or
            ("{0}_{1}".format(imageMapColor[rgb][conta][0], imageMapColor[rgb][conta][1]+1) in imageMapPixels and
            imageMapPixels["{0}_{1}" .format( imageMapColor[rgb][conta][0], imageMapColor[rgb][conta][1])] != imageMapPixels["{0}_{1}".format( imageMapColor[rgb][conta][0], imageMapColor[rgb][conta][1]+1)])
            or
            ("{0}_{1}".format(imageMapColor[rgb][conta][0], imageMapColor[rgb][conta][1]-1) in imageMapPixels and
            imageMapPixels["{0}_{1}" .format( imageMapColor[rgb][conta][0], imageMapColor[rgb][conta][1])] != imageMapPixels["{0}_{1}".format(imageMapColor[rgb][conta][0], imageMapColor[rgb][conta][1]-1)])
            or
            ("{0}_{1}".format(imageMapColor[rgb][conta][0]+1, imageMapColor[rgb][conta][1]+1) in imageMapPixels and
            imageMapPixels["{0}_{1}" .format( imageMapColor[rgb][conta][0], imageMapColor[rgb][conta][1])] != imageMapPixels["{0}_{1}".format( imageMapColor[rgb][conta][0]+1, imageMapColor[rgb][conta][1]+1)])
            or
            ("{0}_{1}".format(imageMapColor[rgb][conta][0]-1, imageMapColor[rgb][conta][1]-1) in imageMapPixels and
            imageMapPixels["{0}_{1}".format( imageMapColor[rgb][conta][0], imageMapColor[rgb][conta][1])] != imageMapPixels["{0}_{1}".format(imageMapColor[rgb][conta][0]-1, imageMapColor[rgb][conta][1]-1)])
            ):
                pyautogui.click(canvas[0] + (imageMapColor[rgb][conta][0]*3), canvas[1] + (imageMapColor[rgb][conta][1]*3))
            conta += 1
    for rgb in imageMapColor.keys():
        R, G, B = rgb.split(',')
        if int(R) > 215 and int(G) > 215 and int(B) > 215:
            continue
        HSV = RGB_HSV(int(R),int(G),int(B))
        Hue, Saturation, Value = HSV[0], HSV[1], HSV[2]
        pyautogui.click(roda_paleta)
        x_cor = cruz_paleta[0] + (Hue / esc_hue)
        y_cor = cruz_paleta[1] - (Saturation / esc_sat)
        pyautogui.click(x_cor, y_cor)
        x_luminosidade = baixo_barra[0]
        y_luminosidade = baixo_barra[1] - (Value / esc_val)
        pyautogui.click(x_luminosidade, y_luminosidade)
        conta = 0
        while(conta < len(imageMapColor[rgb])):
            if kb.is_pressed("ctrl+i"):
                restart_app()
            pyautogui.click(canvas[0] + (imageMapColor[rgb][conta][0]*3), canvas[1] + (imageMapColor[rgb][conta][1]*3))
            conta += 1
    input('Desenho completado!')
    restart_app()
while not kb.is_pressed("ctrl+b"):
        continue
on_triggered_screen()
