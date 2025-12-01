# inst.py
# Textos de la aplicación de hidratación diaria

txt_hello = "Bienvenido al Programa de control de hidratación diaria"

txt_instruction = (
    "Esta aplicación te ayuda a hacer una evaluación rápida de tu hidratación.\n\n"
    "Reglas de la prueba:\n"
    "1. Ingresa tus datos básicos (nombre, edad y peso).\n"
    "2. Indica cuántos vasos de agua (250 ml) has tomado hoy.\n"
    "3. El programa calcula si tu consumo se acerca a la cantidad recomendada\n"
    "   según tu peso (entre 30 y 35 ml de agua por kg de peso corporal).\n\n"
    "Datos basados en recomendaciones de la EFSA y la National Academy of Sciences.\n"
    "Recuerda: esto es orientativo y no reemplaza la opinión de un profesional.\n"
)

txt_second_title = "Pantalla 2 – Evalúa tu hidratación y aprende"

txt_timer_help = (
    "Usa el temporizador de 10 segundos como recordatorio para revisar cuánto\n"
    "agua has tomado hoy. Después, escribe el número de vasos (250 ml cada uno)."
)

txt_results_title = "Pantalla 3 – Resultados de tu hidratación diaria"

txt_fact = (
    "Dato: el cuerpo pierde aproximadamente 1–1.5 litros de agua al día solo por\n"
    "respirar, sudar y orinar (NASEM, 2020)."
)# my_app.py
import sys
from PyQt5.QtWidgets import (
    QApplication, QMainWindow, QWidget, QLabel,
    QPushButton, QVBoxLayout, QStackedWidget
)
from PyQt5.QtCore import Qt

from inst import txt_hello, txt_instruction
from second_win import TestWin
from final_win import FinalWin


class StartPage(QWidget):
    """Pantalla 1: bienvenida y reglas"""
    def __init__(self, main_window):
        super().__init__()
        self.main_window = main_window
        self.initUI()

    def initUI(self):
        self.setStyleSheet("background-color: #e3f2fd;")
        layout = QVBoxLayout()

        lbl_title = QLabel(txt_hello)
        lbl_title.setAlignment(Qt.AlignCenter)
        lbl_title.setStyleSheet("font-size: 22px; font-weight: bold;")

        lbl_text = QLabel(txt_instruction)
        lbl_text.setWordWrap(True)
        lbl_text.setStyleSheet("font-size: 14px;")
        lbl_text.setAlignment(Qt.AlignTop)

        btn_next = QPushButton("Ir a la evaluación")
        btn_next.setStyleSheet(
            "font-size: 16px; padding: 10px 20px;"
            "background-color: #0288d1; color: white; border-radius: 8px;"
        )
        btn_next.clicked.connect(self.go_next)

        layout.addWidget(lbl_title)
        layout.addSpacing(15)
        layout.addWidget(lbl_text)
        layout.addStretch()
        layout.addWidget(btn_next, alignment=Qt.AlignCenter)
        layout.addSpacing(20)

        self.setLayout(layout)

    def go_next(self):
        self.main_window.go_to_test()


class MainWin(QMainWindow):
    """Ventana principal que contiene las 3 páginas como un libro"""
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Programa de hidratación diaria")
        self.resize(950, 650)

        self.stack = QStackedWidget()
        self.setCentralWidget(self.stack)

        # Crear páginas
        self.start_page = StartPage(self)
        self.test_page = TestWin(self)
        self.result_page = FinalWin(self)

        # Añadir al stack
        self.stack.addWidget(self.start_page)   # índice 0
        self.stack.addWidget(self.test_page)    # índice 1
        self.stack.addWidget(self.result_page)  # índice 2

    # --- Navegación tipo “pasar página” ---
    def go_to_start(self):
        self.stack.setCurrentWidget(self.start_page)

    def go_to_test(self):
        self.stack.setCurrentWidget(self.test_page)

    def go_to_results(self, data):
        """
        data = dict con:
        name, age, taken_ml, recommended_ml, index, education_score
        """
        self.result_page.update_results(**data)
        self.stack.setCurrentWidget(self.result_page)


def main():
    app = QApplication(sys.argv)
    w = MainWin()
    w.show()
    sys.exit(app.exec_())


if __name__ == "__main__":
    main()
# second_win.py
from PyQt5.QtWidgets import (
    QWidget, QLabel, QLineEdit, QPushButton,
    QVBoxLayout, QHBoxLayout, QSpinBox, QMessageBox, QGroupBox
)
from PyQt5.QtCore import Qt, QTimer

from inst import txt_second_title, txt_timer_help, txt_fact


class TestWin(QWidget):
    """Pantalla 2: datos + temporizador + preguntas educativas"""
    def __init__(self, main_window):
        super().__init__()
        self.main_window = main_window

        self.time_left = 10
        self.timer = None
        self.education_score = 0  # cuántas respuestas correctas
        self.initUI()

    def initUI(self):
        self.setStyleSheet("background-color: #f1f8e9;")
        self.setMinimumSize(950, 650)

        main_layout = QVBoxLayout()

        title = QLabel(txt_second_title)
        title.setAlignment(Qt.AlignCenter)
        title.setStyleSheet("font-size: 20px; font-weight: bold;")
        main_layout.addWidget(title)
        main_layout.addSpacing(10)

        # --- Datos personales y vasos ---
        main_layout.addLayout(self._crear_datos_usuario())

        # --- Temporizador ---
        main_layout.addSpacing(15)
        main_layout.addLayout(self._crear_temporizador())

        # --- Trivia educativa ---
        main_layout.addSpacing(15)
        trivia_group = self._crear_trivia()
        main_layout.addWidget(trivia_group)

        # Dato extra
        lbl_fact = QLabel(txt_fact)
        lbl_fact.setWordWrap(True)
        lbl_fact.setStyleSheet("font-size: 12px; font-style: italic;")
        main_layout.addWidget(lbl_fact)

        # Botones de navegación
        buttons_layout = QHBoxLayout()

        btn_back = QPushButton("Volver a la pantalla 1")
        btn_back.setStyleSheet(
            "font-size: 14px; padding: 8px 14px; border-radius: 6px;"
        )
        btn_back.clicked.connect(self.main_window.go_to_start)

        btn_results = QPushButton("Calcular resultados")
        btn_results.setStyleSheet(
            "font-size: 16px; padding: 10px 20px; "
            "background-color: #0288d1; color: white; border-radius: 8px;"
        )
        btn_results.clicked.connect(self.calculate_results)

        buttons_layout.addWidget(btn_back)
        buttons_layout.addStretch()
        buttons_layout.addWidget(btn_results)

        main_layout.addStretch()
        main_layout.addLayout(buttons_layout)
        main_layout.addSpacing(15)

        self.setLayout(main_layout)

    # ------------------------------------------------------------------
    # Bloques de interfaz
    # ------------------------------------------------------------------
    def _crear_datos_usuario(self):
        layout = QVBoxLayout()

        name_lay = QHBoxLayout()
        lbl_name = QLabel("Nombre completo:")
        self.edt_name = QLineEdit()
        name_lay.addWidget(lbl_name)
        name_lay.addWidget(self.edt_name)

        age_lay = QHBoxLayout()
        lbl_age = QLabel("Edad (años):")
        self.edt_age = QLineEdit()
        age_lay.addWidget(lbl_age)
        age_lay.addWidget(self.edt_age)

        weight_lay = QHBoxLayout()
        lbl_weight = QLabel("Peso (kg):")
        self.edt_weight = QLineEdit()
        self.edt_weight.setPlaceholderText("Ej.: 60")
        weight_lay.addWidget(lbl_weight)
        weight_lay.addWidget(self.edt_weight)

        glasses_lay = QHBoxLayout()
        lbl_glasses = QLabel("Vasos de agua hoy (250 ml c/u):")
        self.spn_glasses = QSpinBox()
        self.spn_glasses.setRange(0, 50)
        glasses_lay.addWidget(lbl_glasses)
        glasses_lay.addWidget(self.spn_glasses)

        layout.addLayout(name_lay)
        layout.addLayout(age_lay)
        layout.addLayout(weight_lay)
        layout.addLayout(glasses_lay)

        return layout

    def _crear_temporizador(self):
        layout = QVBoxLayout()

        lbl_help = QLabel(txt_timer_help)
        lbl_help.setWordWrap(True)
        layout.addWidget(lbl_help)

        row = QHBoxLayout()
        self.lbl_timer = QLabel("00:00:10")
        self.lbl_timer.setAlignment(Qt.AlignRight)
        self.lbl_timer.setStyleSheet("font-size: 30px; font-weight: bold;")

        self.btn_start_timer = QPushButton("Iniciar 10 segundos")
        self.btn_start_timer.setStyleSheet(
            "font-size: 14px; padding: 8px 14px; "
            "background-color: #43a047; color: white; border-radius: 6px;"
        )
        self.btn_start_timer.clicked.connect(self.start_timer)

        row.addWidget(self.btn_start_timer)
        row.addStretch()
        row.addWidget(self.lbl_timer)

        layout.addLayout(row)
        return layout

    def _crear_trivia(self):
        group = QGroupBox("Pon a prueba tus conocimientos 💧")
        layout = QVBoxLayout()

        # Pregunta 1
        lbl_q1 = QLabel("1. ¿Cuánta agua pierde el cuerpo diariamente?")
        btn1A = QPushButton("A) 1 a 1.5 litros")
        btn1B = QPushButton("B) 3 litros")
        btn1C = QPushButton("C) 5 litros")
        btn1A.clicked.connect(lambda: self._respuesta(1, "A"))
        btn1B.clicked.connect(lambda: self._respuesta(1, "B"))
        btn1C.clicked.connect(lambda: self._respuesta(1, "C"))

        layout.addWidget(lbl_q1)
        layout.addWidget(btn1A)
        layout.addWidget(btn1B)
        layout.addWidget(btn1C)
        layout.addSpacing(10)

        # Pregunta 2
        lbl_q2 = QLabel("2. ¿Qué indicador casero es mejor para ver tu hidratación?")
        btn2A = QPushButton("A) El color de la orina")
        btn2B = QPushButton("B) Las horas de sueño")
        btn2C = QPushButton("C) La cantidad de ejercicio")
        btn2A.clicked.connect(lambda: self._respuesta(2, "A"))
        btn2B.clicked.connect(lambda: self._respuesta(2, "B"))
        btn2C.clicked.connect(lambda: self._respuesta(2, "C"))

        layout.addWidget(lbl_q2)
        layout.addWidget(btn2A)
        layout.addWidget(btn2B)
        layout.addWidget(btn2C)
        layout.addSpacing(10)

        # Pregunta 3
        lbl_q3 = QLabel("3. ¿Cada cuánto se recomienda beber agua a lo largo del día?")
        btn3A = QPushButton("A) Cada 30 minutos")
        btn3B = QPushButton("B) Cada 2–3 horas")
        btn3C = QPushButton("C) Solo cuando tengas sed")
        btn3A.clicked.connect(lambda: self._respuesta(3, "A"))
        btn3B.clicked.connect(lambda: self._respuesta(3, "B"))
        btn3C.clicked.connect(lambda: self._respuesta(3, "C"))

        layout.addWidget(lbl_q3)
        layout.addWidget(btn3A)
        layout.addWidget(btn3B)
        layout.addWidget(btn3C)

        group.setLayout(layout)
        return group

    # ------------------------------------------------------------------
    # Lógica del temporizador
    # ------------------------------------------------------------------
    def start_timer(self):
        self.time_left = 10
        self.lbl_timer.setText("00:00:10")
        if self.timer:
            self.timer.stop()

        self.timer = QTimer(self)
        self.timer.timeout.connect(self.update_timer)
        self.timer.start(1000)

    def update_timer(self):
        self.time_left -= 1
        if self.time_left < 0:
            self.timer.stop()
            self.lbl_timer.setText("¡Tiempo!")
            return
        self.lbl_timer.setText(f"00:00:{self.time_left:02d}")

    # ------------------------------------------------------------------
    # Lógica de preguntas
    # ------------------------------------------------------------------
    def _respuesta(self, pregunta, opcion):
        correctas = {1: "A", 2: "A", 3: "B"}
        mensajes_ok = {
            1: "Correcto: el cuerpo pierde 1–1.5 L/día (NASEM, 2020).",
            2: "Exacto: el color de la orina es un buen indicador (Mayo Clinic).",
            3: "Muy bien: beber cada 2–3 h ayuda a mantenerte hidratado."
        }

        if opcion == correctas[pregunta]:
            self.education_score += 1
            QMessageBox.information(self, "Respuesta correcta", mensajes_ok[pregunta])
        else:
            QMessageBox.warning(
                self,
                "Respuesta incorrecta",
                "No es la opción correcta.\n\n" + mensajes_ok[pregunta]
            )

    # ------------------------------------------------------------------
    # Cálculo y cambio de página
    # ------------------------------------------------------------------
    def calculate_results(self):
        name = self.edt_name.text().strip()
        age_text = self.edt_age.text().strip()
        weight_text = self.edt_weight.text().strip()
        glasses = self.spn_glasses.value()

        if not name:
            QMessageBox.warning(self, "Dato faltante", "Escribe tu nombre.")
            return

        try:
            age = int(age_text)
            weight = float(weight_text)
        except ValueError:
            QMessageBox.warning(
                self, "Dato inválido",
                "Edad debe ser entero y peso un número (ej. 60 o 60.5)."
            )
            return

        if age <= 0 or weight <= 0:
            QMessageBox.warning(self, "Dato inválido", "Edad y peso deben ser mayores a 0.")
            return

        recommended_ml = int(weight * 35)   # fórmula básica 35 ml/kg
        taken_ml = glasses * 250
        index = int((taken_ml / recommended_ml) * 100)

        data = {
            "user_name": name,
            "age": age,
            "taken_ml": taken_ml,
            "recommended_ml": recommended_ml,
            "index": index,
            "education_score": self.education_score
        }

        # Pasar a pantalla 3 como si fuera otra página del libro
        self.main_window.go_to_results(data)
# final_win.py
from PyQt5.QtWidgets import (
    QWidget, QLabel, QPushButton, QVBoxLayout,
    QHBoxLayout, QProgressBar
)
from PyQt5.QtCore import Qt

from inst import txt_results_title


# ----------------------------------------------------------------------
# Funciones de texto
# ----------------------------------------------------------------------
def classify_index(index):
    """Clasifica el % de hidratación."""
    if index >= 110:
        return (
            "Has superado la recomendación. Escucha a tu cuerpo y evita exagerar: "
            "hidratarse demasiado rápido también puede ser incómodo."
        )
    elif 90 <= index < 110:
        return "¡Excelente! Estás muy cerca de la hidratación ideal para tu cuerpo."
    elif 70 <= index < 90:
        return (
            "Aceptable, pero podrías tomar uno o dos vasos más durante el día "
            "para llegar al rango ideal."
        )
    elif 50 <= index < 70:
        return (
            "Tu hidratación es baja. Intenta llevar una botella de agua contigo y "
            "dar pequeños sorbos a lo largo del día."
        )
    else:
        return (
            "Hidratación muy baja. Es importante aumentar el consumo de agua y, "
            "si te sientes mareado o con dolor de cabeza, consultar a un profesional."
        )


def classify_education(score):
    """Mensaje según cuántas preguntas acertó."""
    if score == 3:
        return (
            "¡Genial! Contestaste correctamente las 3 preguntas. Tienes muy buena "
            "información sobre cómo cuidar tu hidratación."
        )
    elif score == 2:
        return (
            "Vas muy bien, acertaste 2 de 3. Con un pequeño repaso tendrás un "
            "súper nivel de conocimiento sobre hidratación."
        )
    elif score == 1:
        return (
            "Acertaste 1 de 3. Ya tienes una base, ahora sabes algunos datos "
            "clave para cuidar mejor tu cuerpo."
        )
    else:
        return (
            "Esta vez no acertaste, pero lo importante es que ahora conoces datos "
            "nuevos y puedes mejorar tus hábitos desde hoy."
        )


def motivational_message(index, score):
    """Frase motivadora combinando resultado + conocimiento."""
    if index >= 90 and score >= 2:
        return (
            "Estás cuidando tu cuerpo por dentro y también tu mente. "
            "Sigue así, eres un buen ejemplo de autocuidado. 💧💪"
        )
    elif index >= 70 and score >= 1:
        return (
            "Vas por buen camino. Con algunos vasos extra de agua y pequeños "
            "recordatorios, tu cuerpo te lo va a agradecer. 😊"
        )
    elif index < 70 and score >= 2:
        return (
            "Sabes qué hacer, ahora toca aplicarlo: usa lo que aprendiste y "
            "añade más agua a tu día. Cada vaso cuenta. 🌱"
        )
    else:
        return (
            "Este resultado no es para regañarte, sino para ayudarte a mejorar. "
            "Hoy es un buen día para empezar a hidratarte mejor. ✨"
        )


# ----------------------------------------------------------------------
# Clase de la pantalla final
# ----------------------------------------------------------------------
class FinalWin(QWidget):
    """Pantalla 3: muestra resultados, barras y mensajes motivacionales."""
    def __init__(self, main_window=None):
        super().__init__()
        self.main_window = main_window
        self.initUI()

    def initUI(self):
        # Fondo y tipografía base
        self.setStyleSheet("""
            QWidget {
                background-color: #fff3e0;
                font-family: Arial, Helvetica, sans-serif;
            }
            QLabel {
                color: #333333;
            }
        """)
        self.setMinimumSize(950, 650)

        main_layout = QVBoxLayout()

        # Título
        self.lbl_title = QLabel(txt_results_title)
        self.lbl_title.setAlignment(Qt.AlignCenter)
        self.lbl_title.setStyleSheet("font-size: 22px; font-weight: bold;")
        main_layout.addWidget(self.lbl_title)
        main_layout.addSpacing(10)

        # Nombre y edad
        self.lbl_name = QLabel("")
        self.lbl_name.setAlignment(Qt.AlignCenter)
        self.lbl_name.setStyleSheet("font-size: 14px;")
        main_layout.addWidget(self.lbl_name)

        # ------------------------------------------------------------------
        # Sección de hidratación (porcentaje + barra)
        # ------------------------------------------------------------------
        hydration_layout = QVBoxLayout()

        self.lbl_index = QLabel("")
        self.lbl_index.setAlignment(Qt.AlignCenter)
        self.lbl_index.setStyleSheet("font-size: 18px; font-weight: bold;")
        hydration_layout.addWidget(self.lbl_index)

        self.hydration_bar = QProgressBar()
        self.hydration_bar.setRange(0, 150)  # hasta 150% por si se pasa
        self.hydration_bar.setTextVisible(True)
        self.hydration_bar.setStyleSheet("""
            QProgressBar {
                border: 1px solid #888;
                border-radius: 8px;
                background: #ffe0b2;
                height: 24px;
            }
            QProgressBar::chunk {
                border-radius: 8px;
                background-color: #ff9800;
            }
        """)
        hydration_layout.addWidget(self.hydration_bar)

        self.lbl_ml = QLabel("")
        self.lbl_ml.setAlignment(Qt.AlignCenter)
        self.lbl_ml.setStyleSheet("font-size: 14px;")
        hydration_layout.addWidget(self.lbl_ml)

        self.lbl_message = QLabel("")
        self.lbl_message.setWordWrap(True)
        self.lbl_message.setAlignment(Qt.AlignTop | Qt.AlignHCenter)
        self.lbl_message.setStyleSheet("font-size: 14px; margin-top: 8px;")
        hydration_layout.addWidget(self.lbl_message)

        main_layout.addLayout(hydration_layout)
        main_layout.addSpacing(10)

        # ------------------------------------------------------------------
        # Sección de “puntaje educativo”
        # ------------------------------------------------------------------
        education_layout = QVBoxLayout()

        self.lbl_education_title = QLabel("Puntaje educativo sobre hidratación")
        self.lbl_education_title.setAlignment(Qt.AlignCenter)
        self.lbl_education_title.setStyleSheet(
            "font-size: 16px; font-weight: bold; color: #6a1b9a;"
        )
        education_layout.addWidget(self.lbl_education_title)

        # Barra para el puntaje (0–3 preguntas correctas)
        self.education_bar = QProgressBar()
        self.education_bar.setRange(0, 3)
        self.education_bar.setTextVisible(True)
        self.education_bar.setStyleSheet("""
            QProgressBar {
                border: 1px solid #888;
                border-radius: 8px;
                background: #f3e5f5;
                height: 20px;
            }
            QProgressBar::chunk {
                border-radius: 8px;
                background-color: #8e24aa;
            }
        """)
        education_layout.addWidget(self.education_bar)

        self.lbl_education = QLabel("")
        self.lbl_education.setWordWrap(True)
        self.lbl_education.setAlignment(Qt.AlignTop | Qt.AlignHCenter)
        self.lbl_education.setStyleSheet("font-size: 13px; color: #4a148c;")
        education_layout.addWidget(self.lbl_education)

        main_layout.addLayout(education_layout)
        main_layout.addSpacing(10)

        # ------------------------------------------------------------------
        # Mensaje motivacional (como “frase final”)
        # ------------------------------------------------------------------
        self.lbl_motivation = QLabel("")
        self.lbl_motivation.setWordWrap(True)
        self.lbl_motivation.setAlignment(Qt.AlignCenter)
        self.lbl_motivation.setStyleSheet(
            "font-size: 14px; font-weight: bold; color: #1565c0; margin: 10px;"
        )
        main_layout.addWidget(self.lbl_motivation)
        main_layout.addStretch()

        # ------------------------------------------------------------------
        # Botones inferiores
        # ------------------------------------------------------------------
        buttons_layout = QHBoxLayout()

        self.btn_again = QPushButton("Volver al inicio")
        self.btn_again.setStyleSheet(
            "font-size: 14px; padding: 8px 16px; "
            "background-color: #0288d1; color: white; border-radius: 8px;"
        )
        self.btn_again.clicked.connect(self.go_start)

        self.btn_exit = QPushButton("Salir")
        self.btn_exit.setStyleSheet(
            "font-size: 14px; padding: 8px 16px; "
            "background-color: #d84315; color: white; border-radius: 8px;"
        )
        self.btn_exit.clicked.connect(self.close_app)

        buttons_layout.addWidget(self.btn_again)
        buttons_layout.addStretch()
        buttons_layout.addWidget(self.btn_exit)

        main_layout.addLayout(buttons_layout)
        main_layout.addSpacing(20)

        self.setLayout(main_layout)

    # ------------------------------------------------------------------
    # Método que recibe los datos desde la pantalla 2
    # ------------------------------------------------------------------
    def update_results(self, user_name, age, taken_ml, recommended_ml, index, education_score):
        """
        Se llama desde TestWin para actualizar la información.

        index: porcentaje de hidratación (0–100 o más)
        education_score: preguntas correctas (0–3)
        """
        # Texto básico
        self.lbl_name.setText(f"Nombre: {user_name} (edad: {age} años)")
        self.lbl_index.setText(f"Índice de hidratación: {index} %")
        self.lbl_ml.setText(
            f"Has tomado {taken_ml} ml de un total recomendado de {recommended_ml} ml."
        )

        # Barra de hidratación (limitamos visualmente a 150%)
        valor_barra = max(0, min(index, 150))
        self.hydration_bar.setValue(valor_barra)

        # Mensaje de hidratación
        self.lbl_message.setText(classify_index(index))

        # Puntaje educativo
        self.education_bar.setValue(education_score)
        self.education_bar.setFormat(f"{education_score} / 3 respuestas correctas")
        self.lbl_education.setText(classify_education(education_score))

        # Mensaje motivacional final
        self.lbl_motivation.setText(motivational_message(index, education_score))

    # ------------------------------------------------------------------
    # Navegación
    # ------------------------------------------------------------------
    def go_start(self):
        """Vuelve a la primera página del 'libro'."""
        if self.main_window is not None:
            self.main_window.go_to_start()

    def close_app(self):
        """Cierra toda la aplicación."""
        from PyQt5.QtWidgets import QApplication
        QApplication.instance().quit()
git status
git add
git commit
