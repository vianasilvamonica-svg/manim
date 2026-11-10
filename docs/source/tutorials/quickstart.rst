from manim import *
import numpy as np


# ============================================================
#   GATINHO PRETO (desenhado com formas geométricas)
# ============================================================
class GatinhoPreto(VGroup):
    def __init__(self, scale_factor=1.0, **kwargs):
        super().__init__(**kwargs)

        PRETO = BLACK
        BRANCO = WHITE

        # Corpo
        corpo = Ellipse(width=1.6, height=1.2, color=PRETO, fill_opacity=1)
        corpo.move_to(DOWN * 0.6)

        # Cabeça
        cabeca = Circle(radius=0.55, color=PRETO, fill_opacity=1)
        cabeca.move_to(UP * 0.6)

        # Orelhas (triângulos)
        orelha_esq = Triangle(color=PRETO, fill_opacity=1).scale(0.28)
        orelha_esq.move_to(UP * 1.15 + LEFT * 0.35)
        orelha_esq.rotate(-0.2)

        orelha_dir = Triangle(color=PRETO, fill_opacity=1).scale(0.28)
        orelha_dir.move_to(UP * 1.15 + RIGHT * 0.35)
        orelha_dir.rotate(0.2)

        # Olhos (grandes e expressivos)
        olho_esq = Circle(radius=0.13, color=BRANCO, fill_opacity=1)
        olho_esq.move_to(UP * 0.7 + LEFT * 0.2)

        olho_dir = Circle(radius=0.13, color=BRANCO, fill_opacity=1)
        olho_dir.move_to(UP * 0.7 + RIGHT * 0.2)

        pupila_esq = Dot(point=olho_esq.get_center(), radius=0.06, color=PRETO)
        pupila_dir = Dot(point=olho_dir.get_center(), radius=0.06, color=PRETO)

        # Nariz
        nariz = Triangle(color=PINK, fill_opacity=1).scale(0.06)
        nariz.move_to(UP * 0.5)
        nariz.rotate(PI)

        # Bigodes
        bigode_esq1 = Line(LEFT * 0.6 + UP * 0.52, LEFT * 0.25 + UP * 0.5, color=WHITE, stroke_width=2)
        bigode_esq2 = Line(LEFT * 0.6 + UP * 0.45, LEFT * 0.25 + UP * 0.47, color=WHITE, stroke_width=2)
        bigode_dir1 = Line(RIGHT * 0.6 + UP * 0.52, RIGHT * 0.25 + UP * 0.5, color=WHITE, stroke_width=2)
        bigode_dir2 = Line(RIGHT * 0.6 + UP * 0.45, RIGHT * 0.25 + UP * 0.47, color=WHITE, stroke_width=2)

        # Cauda
        cauda = Arc(radius=0.5, start_angle=-PI/2, angle=PI, color=PRETO, stroke_width=14)
        cauda.move_to(DOWN * 1.0 + RIGHT * 0.9)

        self.add(corpo, cauda, cabeca, orelha_esq, orelha_dir,
                 olho_esq, olho_dir, pupila_esq, pupila_dir,
                 nariz, bigode_esq1, bigode_esq2, bigode_dir1, bigode_dir2)

        # Referências para piscar
        self.olho_esq = olho_esq
        self.olho_dir = olho_dir
        self.pupila_esq = pupila_esq
        self.pupila_dir = pupila_dir

        self.scale(scale_factor)

    def piscar(self):
        """Animação de piscar os olhos."""
        return AnimationGroup(
            self.olho_esq.animate.stretch(0.1, 1),
            self.olho_dir.animate.stretch(0.1, 1),
            self.pupila_esq.animate.set_opacity(0),
            self.pupila_dir.animate.set_opacity(0),
            lag_ratio=0,
        )

    def abrir_olhos(self):
        return AnimationGroup(
            self.olho_esq.animate.stretch(10, 1),
            self.olho_dir.animate.stretch(10, 1),
            self.pupila_esq.animate.set_opacity(1),
            self.pupila_dir.animate.set_opacity(1),
            lag_ratio=0,
        )


# ============================================================
#   CENA PRINCIPAL
# ============================================================
class GatinhoExplicaMecanica(Scene):
    def construct(self):
        self.camera.background_color = "#1a1a2e"

        # ---------- TELA INICIAL ----------
        titulo = Text(
            "Gatinho Preto Explica Mecânica",
            font_size=44,
            color=YELLOW,
            weight=BOLD,
        ).to_edge(UP, buff=0.6)

        gatinho = GatinhoPreto(scale_factor=1.4)
        gatinho.move_to(DOWN * 0.8)

        legenda = Text(
            "Miau! Vamos aprender Física?",
            font_size=32,
            color=WHITE,
        ).next_to(gatinho, UP, buff=0.5)

        self.play(Write(titulo), run_time=1.5)
        self.play(FadeIn(gatinho, shift=UP * 0.5), Write(legenda), run_time=1.5)
        self.play(gatinho.piscar(), run_time=0.15)
        self.play(gatinho.abrir_olhos(), run_time=0.15)
        self.wait(1.5)

        # ---------- MENU ----------
        self.play(FadeOut(legenda))
        menu = self.criar_menu()
        self.play(Write(menu))
        self.wait(1.5)
        self.play(FadeOut(menu), gatinho.animate.scale(0.7).to_corner(DL, buff=0.4))

        # ---------- SEÇÃO 1: CINEMÁTICA ----------
        self.secao_cinematica(gatinho)

        # ---------- SEÇÃO 2: DINÂMICA ----------
        self.secao_dinamica(gatinho)

        # ---------- SEÇÃO 3: LEIS DE NEWTON ----------
        self.secao_leis_newton(gatinho)

        # ---------- ENCERRAMENTO ----------
        self.encerramento(gatinho, titulo)

    # --------------------------------------------------------
    def criar_menu(self):
        itens = VGroup(
            self.botao("1. Cinemática", BLUE),
            self.botao("2. Dinâmica", GREEN),
            self.botao("3. Leis de Newton", ORANGE),
        ).arrange(DOWN, buff=0.4).move_to(RIGHT * 3 + DOWN * 0.5)
        return itens

    def botao(self, texto, cor):
        rect = RoundedRectangle(
            corner_radius=0.15, width=4.5, height=0.9,
            color=cor, stroke_width=3, fill_opacity=0.15,
        )
        label = Text(texto, font_size=28, color=cor).move_to(rect)
        return VGroup(rect, label)

    # --------------------------------------------------------
    def secao_cinematica(self, gatinho):
        titulo = self.titulo_secao("Cinemática", BLUE)

        definicao = Text(
            "Estuda o movimento dos corpos\nsem se preocupar com as causas.",
            font_size=26, color=WHITE, line_spacing=1.2,
        ).next_to(titulo, DOWN, buff=0.6)

        # Fórmula principal
        formula = MathTex(
            r"v = \frac{\Delta s}{\Delta t}", color=YELLOW, font_size=60
        ).next_to(definicao, DOWN, buff=0.6)

        # Simulação de um carrinho em MRU
        eixo = NumberLine(
            x_range=[0, 10, 1], length=8,
            include_numbers=False, color=GREY,
        ).to_edge(DOWN, buff=1.5)

        carrinho = self.carrinho().move_to(eixo.n2p(0) + UP * 0.5)
        pos_label = always_redraw(
            lambda: DecimalNumber(0, num_decimal_places=1, color=YELLOW)
            .next_to(carrinho, UP, buff=0.2)
        )

        self.play(Write(titulo))
        self.play(FadeIn(definicao, shift=UP * 0.3))
        self.play(Write(formula))
        self.play(gatinho.piscar(), run_time=0.15)
        self.play(gatinho.abrir_olhos(), run_time=0.15)
        self.play(Create(eixo), FadeIn(carrinho))
        self.play(
            carrinho.animate.move_to(eixo.n2p(10) + UP * 0.5),
            run_time=3, rate_func=linear,
        )

        texto_final = Text(
            "Movimento Retilíneo Uniforme (MRU)",
            font_size=24, color=BLUE_B,
        ).next_to(eixo, UP, buff=1.2)
        self.play(FadeIn(texto_final))

        # Salva o valor final
        self.play(FadeIn(pos_label))
        self.wait(1.5)

        self.play(
            FadeOut(titulo), FadeOut(definicao), FadeOut(formula),
            FadeOut(eixo), FadeOut(carrinho), FadeOut(texto_final),
            FadeOut(pos_label),
        )

    # --------------------------------------------------------
    def secao_dinamica(self, gatinho):
        titulo = self.titulo_secao("Dinâmica", GREEN)

        definicao = Text(
            "Estuda as causas do movimento:\nforças e suas consequências.",
            font_size=26, color=WHITE, line_spacing=1.2,
        ).next_to(titulo, DOWN, buff=0.6)

        formula = MathTex(
            r"F_{r} = m \cdot a", color=YELLOW, font_size=60
        ).next_to(definicao, DOWN, buff=0.5)

        # Bloco com força aplicada
        bloco = Square(side_length=1.2, color=GREEN, fill_opacity=0.6)
        bloco.move_to(LEFT * 2 + DOWN * 1.8)

        seta_forca = Arrow(
            bloco.get_right(), bloco.get_right() + RIGHT * 2.2,
            buff=0, color=RED, stroke_width=8,
        )
        label_f = MathTex("F", color=RED, font_size=40).next_to(seta_forca, UP, buff=0.15)

        massa_label = MathTex("m", color=GREEN, font_size=36).move_to(bloco)

        self.play(Write(titulo))
        self.play(FadeIn(definicao, shift=UP * 0.3))
        self.play(Write(formula))
        self.play(gatinho.piscar(), run_time=0.15)
        self.play(gatinho.abrir_olhos(), run_time=0.15)

        self.play(Create(bloco), Write(massa_label))
        self.play(GrowArrow(seta_forca), Write(label_f))
        self.wait(0.5)

        # Aceleração do bloco
        self.play(
            bloco.animate.shift(RIGHT * 3),
            seta_forca.animate.shift(RIGHT * 3),
            label_f.animate.shift(RIGHT * 3),
            massa_label.animate.shift(RIGHT * 3),
            run_time=1.5, rate_func=rush_into,
        )

        texto_final = Text(
            "Força resultante → aceleração",
            font_size=24, color=GREEN_B,
        ).to_edge(DOWN, buff=1.0)
        self.play(FadeIn(texto_final))
        self.wait(1.5)

        self.play(
            FadeOut(titulo), FadeOut(definicao), FadeOut(formula),
            FadeOut(bloco), FadeOut(seta_forca), FadeOut(label_f),
            FadeOut(massa_label), FadeOut(texto_final),
        )

    # --------------------------------------------------------
    def secao_leis_newton(self, gatinho):
        titulo = self.titulo_secao("Leis de Newton", ORANGE)

        self.play(Write(titulo))
        self.play(gatinho.piscar(), run_time=0.15)
        self.play(gatinho.abrir_olhos(), run_time=0.15)

        leis = [
            (
                "1ª Lei — Inércia",
                "Um corpo tende a permanecer em\nrepouso ou em movimento uniforme.",
                YELLOW,
            ),
            (
                "2ª Lei — F = m·a",
                "A força resultante é igual à massa\nvezes a aceleração.",
                RED,
            ),
            (
                "3ª Lei — Ação e Reação",
                "Toda ação gera uma reação de igual\nintensidade e sentido oposto.",
                CYAN,
            ),
        ]

        for cab, corpo, cor in leis:
            header = Text(cab, font_size=32, color=cor, weight=BOLD)
            body = Text(corpo, font_size=24, color=WHITE, line_spacing=1.2)
            grupo = VGroup(header, body).arrange(DOWN, buff=0.35)
            grupo.next_to(titulo, DOWN, buff=0.8)

            self.play(Write(header), run_time=0.7)
            self.play(FadeIn(body, shift=UP * 0.2), run_time=0.6)
            self.wait(1.2)
            self.play(FadeOut(header), FadeOut(body), run_time=0.5)

        self.play(FadeOut(titulo))

    # --------------------------------------------------------
    def encerramento(self, gatinho, titulo):
        gatinho2 = GatinhoPreto(scale_factor=1.6).center().shift(DOWN * 0.5)

        texto = Text(
            "Miau! Agora você sabe Mecânica!",
            font_size=36, color=YELLOW, weight=BOLD,
        ).next_to(gatinho2, UP, buff=0.7)

        self.play(FadeIn(gatinho2, shift=UP * 0.4))
        self.play(Write(texto))
        self.play(gatinho2.piscar(), run_time=0.15)
        self.play(gatinho2.abrir_olhos(), run_time=0.15)
        self.wait(2)

        self.play(
            FadeOut(gatinho2), FadeOut(texto), FadeOut(titulo),
            run_time=1.5,
        )

    # --------------------------------------------------------
    def titulo_secao(self, texto, cor):
        return Text(texto, font_size=42, color=cor, weight=BOLD).to_edge(UP, buff=0.6)

    def carrinho(self):
        corpo = RoundedRectangle(
            corner_radius=0.1, width=1.2, height=0.6,
            color=BLUE, fill_opacity=0.8,
        )
        roda1 = Circle(radius=0.15, color=WHITE, fill_opacity=1).next_to(
            corpo.get_corner(DL), DOWN, buff=0.02
        )
        roda2 = Circle(radius=0.15, color=WHITE, fill_opacity=1).next_to(
            corpo.get_corner(DR), DOWN, buff=0.02
        )
        return VGroup(corpo, roda1, roda2)


# ============================================================
#   EXECUÇÃO
#   manim -pqh gatinho_mecanica.py GatinhoExplicaMecanica
# ============================================================
