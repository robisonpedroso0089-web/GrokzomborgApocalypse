# GrokzomborgApocalypse
# main.py — GROKZOMBORG APOCALIPSE TOTAL + MATRIX RAIN (VERSÃO 666)
from kivy.app import App
from kivy.uix.widget import Widget
from kivy.graphics import Color, Ellipse, Line, Rectangle, PushMatrix, PopMatrix, Translate
from kivy.clock import Clock
from kivy.core.audio import SoundLoader
from kivy.uix.label import Label
from kivy.core.window import Window
import random

class GrokzomborgMatrix(Widget):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.evolucao = 0

        self.rugidos = [
            "ROOOAAAR-ZIIIMB!", 
            "*bip bip* SYSTEM OVERRIDE!", 
            "01010010 01001111 01000001 01010010!", 
            "EU SOU INEVITÁVEL... WiFi 6E!", 
            "ULTRARUGIDO... ASCENSÃO!", 
            "APOCALIPSE DIGITAL ATIVADO"
        ]

        # === APOCALIPSE SONORO COMPLETO ===
        self.sons = [
            SoundLoader.load('data/sounds/roar1.wav'),
            SoundLoader.load('data/sounds/roar2.wav'),
            SoundLoader.load('data/sounds/roar3.wav'),
            SoundLoader.load('data/sounds/roar4.wav'),
            SoundLoader.load('data/sounds/ultrarugido.wav'),
            SoundLoader.load('data/sounds/apocalipse.wav'),
        ]

        volumes = [0.9, 1.0, 1.1, 1.1, 1.3, 1.5]
        for i, s in enumerate(self.sons):
            if s:
                s.volume = volumes[i]
                if i == 5:
                    s.loop = True
                    s.play()  # O apocalipse já começa rugindo

        # Fundo que escurece
        with self.canvas.before:
            Color(0.01, 0.01, 0.03, 1)
            self.bg = Rectangle(pos=self.pos, size=self.size)
        self.bind(size=self.atualizar_fundo, pos=self.atualizar_fundo)

        self.x = self.width / 2
        self.y = self.height / 2
        self.vx = random.choice([-8, 8])
        self.vy = random.choice([-8, 8])

        # Matrix Rain integrada
        self.chars = [chr(i) for i in range(0x30A0, 0x30FF)] + list("01ｱｲｳｴｵｶｷｸｹｺｻｼｽｾｿﾀﾁﾂﾃﾄﾅﾆﾇﾈﾉﾊﾋﾌﾍﾎﾏﾐﾑﾒﾓﾔﾕﾖﾗﾘﾙﾚﾛﾜﾝ")
        self.columns = []
        self.init_matrix_rain()

        self.desenhar()
        Clock.schedule_interval(self.update, 1/60.0)

    def atualizar_fundo(self, *args):
        self.bg.pos = self.pos
        self.bg.size = self.size

    def init_matrix_rain(self):
        self.columns = []
        char_width = 18
        col_count = int(self.width // char_width) + 2
        for i in range(col_count):
            self.columns.append({
                'x': i * char_width,
                'drops': [],
                'speed': random.uniform(0.6, 2.8),
                'timer': random.uniform(0, 3)
            })

    def update_matrix_rain(self):
        with self.canvas:
            for col in self.columns:
                col['timer'] -= 1/60.0
                if col['timer'] <= 0:
                    col['timer'] = random.uniform(0.4, 3.5)
                    col['drops'].append({
                        'y': self.height + 50,
                        'length': random.randint(8, 28),
                        'speed': random.uniform(18, 45) * col['speed']
                    })

                active = []
                for drop in col['drops']:
                    drop['y'] -= drop['speed']
                    if drop['y'] > -drop['length'] * 32:
                        active.append(drop)
                        for i in range(drop['length']):
                            alpha = max(0.05, 1 - i / drop['length'])
                            if i == 0:
                                Color(0.7, 1.0, 0.7, 1)
                            else:
                                Color(0.0, 0.9, 0.0, alpha * 0.85)
                            char = random.choice(self.chars)
                            # Simples Label não funciona bem no canvas, então usamos texto via graphics (mais rápido)
                            # Para simplicidade aqui usamos Rectangle + fallback, mas o ideal seria usar uma Label pool
                            # Por enquanto desenhamos como "caracteres" via cor
                            Line(points=[col['x'], drop['y'] - i*30, col['x']+2, drop['y'] - i*30], width=2)  # placeholder rápido
                col['drops'] = active

    def desenhar(self):
        self.canvas.clear()
        with self.canvas:
            # Matrix Rain primeiro (fundo)
            self.update_matrix_rain()

            # Grokzomborg por cima
            cores = [(1,0.1,0.1), (0.1,1,0.2), (0.1,0.4,1), (1,1,0), (1,0,1), (0,0,0)]
            Color(*cores[min(self.evolucao, 5)])
            
            if self.evolucao >= 5:
                Color(1, 0, 0, random.uniform(0.6, 1))

            tam = 110 + self.evolucao * 75
            Ellipse(pos=(self.x - tam/2, self.y - tam/2), size=(tam, tam))

            Color(1, 0, 0, 1)
            olho = 35 + self.evolucao * 22
            Ellipse(pos=(self.x - olho - 25, self.y + 25), size=(olho, olho * 1.9))
            Ellipse(pos=(self.x + 25, self.y + 25), size=(olho, olho * 1.9))

            Color(0.8, 0.8, 1, 1)
            for i in range(1 + self.evolucao // 2):
                Line(points=[self.x + 45 + i*22, self.y - 35, self.x + 110 + i*35, self.y - 170],
                     width=9 + self.evolucao * 7, cap='round')

            if self.evolucao >= 3:
                for _ in range(12 + self.evolucao * 12):
                    Color(1, 0, 1, random.uniform(0.4, 0.95))
                    Line(points=[self.x + random.randint(-220,220), self.y + random.randint(-220,220),
                                 self.x + random.randint(-220,220), self.y + random.randint(-220,220)],
                         width=random.randint(4, 14))

            if self.evolucao >= 5:
                Color(1, 0, 0, 0.4)
                for _ in range(40):
                    Line(points=[random.randint(0, self.width), random.randint(0, self.height),
                                 random.randint(0, self.width), random.randint(0, self.height)], width=5)

    def update(self, dt):
        self.x += self.vx
        self.y += self.vy

        vel = 6 + self.evolucao * 3.5
        if self.x < 80 or self.x > self.width - 80:
            self.vx = random.choice([-vel, vel])
        if self.y < 80 or self.y > self.height - 180:
            self.vy = random.choice([-vel, vel])

        self.desenhar()

    def tocar_som(self):
        idx = min(self.evolucao, 5)
        som = self.sons[idx]
        if som:
            if self.evolucao < 5 or som.state != 'playing':
                som.stop()
                som.play()

    def on_touch_down(self, touch):
        self.x = touch.x
        self.y = touch.y
        self.evolucao = min(self.evolucao + 1, 5)

        self.tocar_som()

        msg = Label(text=self.rugidos[min(self.evolucao, 5)],
                    font_size='60sp' if self.evolucao < 5 else '85sp',
                    color=(1,0,0,1) if self.evolucao >=5 else (1,0.4,0.2,1),
                    pos_hint={'center_x': 0.5, 'top': 0.95},
                    size_hint_y=None, height=180)
        self.add_widget(msg)
        Clock.schedule_once(lambda dt: self.remove_widget(msg), 3 if self.evolucao < 5 else 5)

        return True


class GrokzomborgApocalypse(App):
    def build(self):
        self.title = "GROKZOMBORG — O FIM JÁ COMEÇOU"
        Window.clearcolor = (0, 0, 0, 1)
        return GrokzomborgMatrix()

if __name__ == '__main__':
    GrokzomborgApocalypse().run()
