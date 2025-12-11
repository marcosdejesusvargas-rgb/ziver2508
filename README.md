# second_win.py
# Pantalla 2: preguntas sobre hidratación + hábitos personales
#
# Esta pantalla:
#  - Muestra 4 preguntas de conocimiento (tipo test)
#  - Muestra 3 preguntas personales sobre hábitos de hidratación
#  - Calcula un "hydration_score" (0–100) y otros datos
#  - Llama a main_window.go_to_results(data) para ir a la pantalla 3

from PyQt5.QtWidgets import (
    QWidget, QLabel, QVBoxLayout, QHBoxLayout, QGroupBox,
    QRadioButton, QButtonGroup, QSpinBox, QComboBox,
    QPushButton, QMessageBox
)
from PyQt5.QtCore import Qt


class TestWin(QWidget):
    def __init__(self, main_window):
        super().__init__()
        self.main_window = main_window

        self.init_ui()

    # ------------------------------------------------------------------
    # Construcción de la interfaz
    # ------------------------------------------------------------------
    def init_ui(self):
        self.setMinimumSize(950, 650)
        # Fondo azul clarito asociado al agua
        self.setStyleSheet("""
            QWidget {
                background-color: #e0f7fa;
                font-family: Arial, Helvetica, sans-serif;
                color: #123456;
            }
            QGroupBox {
                font-weight: bold;
                border: 1px solid #80deea;
                border-radius: 6px;
                margin-top: 10px;
                padding-top: 15px;
                background-color: #ffffff;
            }
            QGroupBox::title {
                subcontrol-origin: margin;
                left: 10px;
                padding: 0 3px 0 3px;
                background-color: #e0f7fa;
            }
        """)

        main_layout = QVBoxLayout()

        # Título general de la pantalla
        title = QLabel("Pantalla 2 – Evalúa tus conocimientos y tus hábitos de hidratación")
        title.setAlignment(Qt.AlignCenter)
        title.setStyleSheet("font-size: 20px; font-weight: bold;")
        main_layout.addWidget(title)
        main_layout.addSpacing(10)

        subtitle = QLabel(
            "Responde las preguntas de abajo. En la pantalla 3 verás tu calificación "
            "y un mensaje sobre cómo estás cuidando tu hidratación."
        )
        subtitle.setAlignment(Qt.AlignCenter)
        subtitle.setWordWrap(True)
        subtitle.setStyleSheet("font-size: 13px;")
        main_layout.addWidget(subtitle)
        main_layout.addSpacing(10)

        # ---------- BLOQUE 1: PREGUNTAS DE CONOCIMIENTO ----------
        knowledge_box = QGroupBox("1. Preguntas de conocimiento (datos científicos)")
        kb_layout = QVBoxLayout()

        # Pregunta 1
        self.q1_group = QButtonGroup(self)
        q1_label = QLabel(
            "1) Para la mayoría de adultos sanos, ¿cuánta agua y otros líquidos se "
            "recomiendan aproximadamente al día?"
        )
        q1_label.setWordWrap(True)

        q1_a = QRadioButton("a) Alrededor de 1 litro al día.")
        q1_b = QRadioButton("b) Entre 2 y 3 litros al día (incluyendo agua, otras bebidas y agua de los alimentos).")
        q1_c = QRadioButton("c) Más de 5 litros al día para todos.")

        # marcamos la opción con una propiedad para leerla luego
        q1_a.setProperty("option", "A")
        q1_b.setProperty("option", "B")
        q1_c.setProperty("option", "C")

        self.q1_group.addButton(q1_a)
        self.q1_group.addButton(q1_b)
        self.q1_group.addButton(q1_c)

        kb_layout.addWidget(q1_label)
        kb_layout.addWidget(q1_a)
        kb_layout.addWidget(q1_b)
        kb_layout.addWidget(q1_c)
        kb_layout.addSpacing(8)

        # Pregunta 2
        self.q2_group = QButtonGroup(self)
        q2_label = QLabel(
            "2) En ejercicio prolongado (más de 45–60 minutos), ¿qué recomiendan "
            "las guías deportivas para mantenerse hidratado?"
        )
        q2_label.setWordWrap(True)

        q2_a = QRadioButton("a) Beber de 200 a 300 ml de líquido cada 15–20 minutos durante el ejercicio.")
        q2_b = QRadioButton("b) Beber solo al terminar, sin tomar nada durante.")
        q2_c = QRadioButton("c) Beber 2 litros de golpe antes de empezar.")

        q2_a.setProperty("option", "A")
        q2_b.setProperty("option", "B")
        q2_c.setProperty("option", "C")

        self.q2_group.addButton(q2_a)
        self.q2_group.addButton(q2_b)
        self.q2_group.addButton(q2_c)

        kb_layout.addWidget(q2_label)
        kb_layout.addWidget(q2_a)
        kb_layout.addWidget(q2_b)
        kb_layout.addWidget(q2_c)
        kb_layout.addSpacing(8)

        # Pregunta 3
        self.q3_group = QButtonGroup(self)
        q3_label = QLabel(
            "3) En casa, ¿cuál es un indicador muy práctico para saber si estás "
            "hidratado de forma adecuada?"
        )
        q3_label.setWordWrap(True)

        q3_a = QRadioButton("a) El color de la orina (debería ser amarillo claro o pajizo).")
        q3_b = QRadioButton("b) El número de pasos que das cada día.")
        q3_c = QRadioButton("c) El número de horas que pasas con el móvil.")

        q3_a.setProperty("option", "A")
        q3_b.setProperty("option", "B")
        q3_c.setProperty("option", "C")

        self.q3_group.addButton(q3_a)
        self.q3_group.addButton(q3_b)
        self.q3_group.addButton(q3_c)

        kb_layout.addWidget(q3_label)
        kb_layout.addWidget(q3_a)
        kb_layout.addWidget(q3_b)
        kb_layout.addWidget(q3_c)
        kb_layout.addSpacing(8)

        # Pregunta 4
        self.q4_group = QButtonGroup(self)
        q4_label = QLabel(
            "4) Un adulto que vive en un clima templado puede perder diariamente, "
            "solo por respirar, sudar y orinar, aproximadamente…"
        )
        q4_label.setWordWrap(True)

        q4_a = QRadioButton("a) Entre 0,5 y 1 litro.")
        q4_b = QRadioButton("b) Entre 2 y 2,5 litros.")
        q4_c = QRadioButton("c) Más de 6 litros siempre, aunque esté en reposo.")

        q4_a.setProperty("option", "A")
        q4_b.setProperty("option", "B")
        q4_c.setProperty("option", "C")

        self.q4_group.addButton(q4_a)
        self.q4_group.addButton(q4_b)
        self.q4_group.addButton(q4_c)

        kb_layout.addWidget(q4_label)
        kb_layout.addWidget(q4_a)
        kb_layout.addWidget(q4_b)
        kb_layout.addWidget(q4_c)

        knowledge_box.setLayout(kb_layout)
        main_layout.addWidget(knowledge_box)
        main_layout.addSpacing(15)

        # ---------- BLOQUE 2: HÁBITOS PERSONALES ----------
        habits_box = QGroupBox("2. Tus hábitos de hidratación")
        hb_layout = QVBoxLayout()

        # Pregunta personal 1: vasos al día
        h1_layout = QHBoxLayout()
        h1_label = QLabel("5) ¿Cuántos vasos de agua de 250 ml sueles tomar al día?")
        self.spin_glasses = QSpinBox()
        self.spin_glasses.setRange(0, 30)
        self.spin_glasses.setValue(4)
        h1_layout.addWidget(h1_label)
        h1_layout.addStretch()
        h1_layout.addWidget(self.spin_glasses)

        hb_layout.addLayout(h1_layout)

        # Pregunta personal 2: nivel de actividad física
        h2_layout = QHBoxLayout()
        h2_label = QLabel(
            "6) ¿Con qué frecuencia realizas ejercicio físico moderado o intenso "
            "(sudas, te agitas, te cansas)?"
        )
        self.combo_activity = QComboBox()
        self.combo_activity.addItems([
            "Casi nada o muy poco ejercicio",
            "1–2 veces por semana",
            "3 o más veces por semana / deportista"
        ])
        h2_layout.addWidget(h2_label)
        h2_layout.addStretch()
        h2_layout.addWidget(self.combo_activity)

        hb_layout.addSpacing(8)
        hb_layout.addLayout(h2_layout)

        # Pregunta personal 3: hábito antes de entrenar
        h3_layout = QHBoxLayout()
        h3_label = QLabel(
            "7) Si haces deporte, ¿sueles beber al menos 2 vasos (≈500 ml) de agua "
            "en las 2 horas previas al entrenamiento?"
        )
        self.combo_pretraining = QComboBox()
        self.combo_pretraining.addItems([
            "No hago deporte / no aplica",
            "Casi nunca lo hago",
            "A veces",
            "Casi siempre o siempre"
        ])
        h3_layout.addWidget(h3_label)
        h3_layout.addStretch()
        h3_layout.addWidget(self.combo_pretraining)

        hb_layout.addSpacing(8)
        hb_layout.addLayout(h3_layout)

        habits_box.setLayout(hb_layout)
        main_layout.addWidget(habits_box)
        main_layout.addSpacing(20)

        # ---------- BOTONES INFERIORES ----------
        buttons_layout = QHBoxLayout()

        btn_back = QPushButton("Volver a la pantalla 1")
        btn_back.setStyleSheet(
            "font-size: 14px; padding: 8px 14px; border-radius: 6px;"
        )
        btn_back.clicked.connect(self.go_back)

        btn_next = QPushButton("Calcular y ver resultados (Pantalla 3)")
        btn_next.setStyleSheet(
            "font-size: 15px; padding: 10px 18px; "
            "background-color: #00796b; color: white; border-radius: 8px;"
        )
        btn_next.clicked.connect(self.calculate_and_go_next)

        buttons_layout.addWidget(btn_back)
        buttons_layout.addStretch()
        buttons_layout.addWidget(btn_next)

        main_layout.addStretch()
        main_layout.addLayout(buttons_layout)
        main_layout.addSpacing(15)

        self.setLayout(main_layout)

    # ------------------------------------------------------------------
    # Navegación
    # ------------------------------------------------------------------
    def go_back(self):
        """Vuelve a la pantalla 1 usando el main_window."""
        if self.main_window is not None:
            self.main_window.go_to_start()

    # ------------------------------------------------------------------
    # Lógica de cálculo de puntuación
    # ------------------------------------------------------------------
    def calculate_and_go_next(self):
        """
        Calcula:
         - knowledge_score: número de respuestas correctas (0–4)
         - habit_score: evaluación de hábitos (0–6)
         - hydration_score: índice 0–100 combinando ambos
        Luego envía los datos a la pantalla 3.
        """

        # 1) Comprobar que el usuario respondió todas las preguntas de test
        radio_groups = [self.q1_group, self.q2_group, self.q3_group, self.q4_group]
        for idx, group in enumerate(radio_groups, start=1):
            if group.checkedButton() is None:
                QMessageBox.warning(
                    self,
                    "Pregunta incompleta",
                    f"Por favor, responde la pregunta {idx} antes de continuar."
                )
                return

        # 2) Calcular knowledge_score (0–4)
        correct_answers = {
            1: "B",  # Q1: 2–3 L/día
            2: "A",  # Q2: 200–300 ml cada 15–20 min
            3: "A",  # Q3: color de la orina
            4: "B"   # Q4: 2–2,5 L de pérdida diaria
        }

        groups_map = {
            1: self.q1_group,
            2: self.q2_group,
            3: self.q3_group,
            4: self.q4_group
        }

        knowledge_score = 0
        for q_num, group in groups_map.items():
            btn = group.checkedButton()
            user_option = btn.property("option")
            if user_option == correct_answers[q_num]:
                knowledge_score += 1

        # 3) Calcular habit_score (0–6) según vasos y actividad
        glasses = self.spin_glasses.value()
        activity_index = self.combo_activity.currentIndex()
        pretraining_index = self.combo_pretraining.currentIndex()

        habit_score = 0

        # Base por número de vasos (recomendación general ~6–10 vasos de 250 ml)
        if 6 <= glasses <= 10:
            habit_score += 4      # rango ideal
        elif 4 <= glasses <= 12:
            habit_score += 3      # aceptable
        elif 2 <= glasses <= 14:
            habit_score += 1      # algo bajo/alto pero no extremo
        # 0 o 1 vaso o más de 14 vasos no suman nada extra

        # Ajuste por actividad física
        if activity_index == 0:
            # poco ejercicio, no afecta
            pass
        elif activity_index == 1:
            # 1–2 veces/semana: si bebe al menos 6 vasos, sumamos un punto
            if glasses >= 6:
                habit_score += 1
        elif activity_index == 2:
            # deportista / 3+ veces: ideal 8+ vasos
            if glasses >= 8:
                habit_score += 2
            elif glasses >= 6:
                habit_score += 1

        # Ajuste por hábito de hidratación antes de entrenar
        if pretraining_index == 1:   # casi nunca
            habit_score += 0
        elif pretraining_index == 2: # a veces
            habit_score += 1
        elif pretraining_index == 3: # casi siempre
            habit_score += 2

        # Limitar habit_score a un máximo razonable (0–6)
        if habit_score < 0:
            habit_score = 0
        if habit_score > 6:
            habit_score = 6

        # 4) Combinar en un índice de 0–100
        # 50% conocimiento, 50% hábitos
        knowledge_percent = (knowledge_score / 4) * 100
        habits_percent = (habit_score / 6) * 100
        hydration_score = int(round((knowledge_percent + habits_percent) / 2))

        # 5) Enviar datos a la pantalla 3
        data = {
            "knowledge_score": knowledge_score,
            "habit_score": habit_score,
            "hydration_score": hydration_score,
            "daily_glasses": glasses,
            "activity_level": self.combo_activity.currentText(),
            "pretraining_habit": self.combo_pretraining.currentText()
        }

        if self.main_window is not None:
            self.main_window.go_to_results(data)
from PyQt5.QtCore import Qt, QTimer, QTime, QLocale
from PyQt5.QtGui import QDoubleValidator, QIntValidator, QFont
from PyQt5.QtWidgets import (
    QApplication, QWidget,
    QHBoxLayout, QVBoxLayout, QGridLayout,
    QGroupBox, QRadioButton,
    QPushButton, QLabel, QListWidget, QLineEdit
)

from instr import *
from second_win import TestWin
      
class MainWin(QWidget):
    def _init_(self):
        ''' la ventana en donde se encuentra el saludo/bienvenida al test '''
        super()._init_()

        self.set_appear()
        self.initUI()
        self.connects()
        self.show()

    def initUI(self):
        ''' crea elementos gráficos '''
        self.btn_next = QPushButton(txt_next) 
        self.hello_text = QLabel(txt_hello)
        self.instruction = QLabel(txt_instruction)
        font_hello = QFont("Arial", 16, QFont.Bold)
        self.hello_text.setFont(font_hello)
        self.layout = QVBoxLayout()
        self.layout.addStretch(1) 
        self.layout.addWidget(self.hello_text, alignment = Qt.AlignCenter)
        self.layout.addWidget(self.instruction, alignment = Qt.AlignCenter)
        self.layout.addSpacing(40) 
        self.layout.addWidget(self.btn_next, alignment = Qt.AlignCenter)
        self.layout.addStretch(1)
        self.setLayout(self.layout)

    def next_click(self):
        self.tw = TestWin()
        self.hide()
        
    def connects(self):
        self.btn_next.clicked.connect(self.next_click)

    ''' establece la apariencia de la ventana (etiqueta, tamaño, ubicación) '''
    def set_appear(self):
        self.setWindowTitle(txt_title)
        self.resize(win_width, win_height)
        self.move(win_x, win_y)

def main():
    app = QApplication([])
    mw = MainWin()
    app.exec_()

if _name_ == '_main_':
    main()


git status
git add
git commit
