# Detección de Cascos de Seguridad con YOLOv8

Proyecto de Visión Artificial para la detección automática de cascos de seguridad en entornos industriales, utilizando un modelo YOLOv8 entrenado con un dataset personalizado.

**Integrante:** Christian Gonzalez Zarate   23310384   6E

---

## 📋 Tabla de Contenidos

- [Descripción del proyecto](#descripción-del-proyecto)
- [Dataset](#dataset)
- [Instrucciones para correr el código](#instrucciones-para-correr-el-código)
- [Resultados del modelo](#resultados-del-modelo)
- [Caso de Estudio: Aplicación en la vida real](#caso-de-estudio-aplicación-en-la-vida-real)
- [Estructura del repositorio](#estructura-del-repositorio)

---

## Descripción del proyecto

Este proyecto entrena un modelo de detección de objetos **YOLOv8** (Ultralytics) capaz de identificar, en imágenes de obras de construcción y entornos industriales, si una persona:

- ✅ Lleva puesto un casco de seguridad (`helmet`)
- ❌ No lleva casco (`no-helmet`)
- ⚠️ Lleva un casco colocado incorrectamente (`wrong-helmet`)

El objetivo es explorar cómo un modelo de este tipo podría integrarse en un sistema real de monitoreo de seguridad laboral.

---

## Dataset

- **Fuente:** [Safety Helmet Detection Dataset](https://universe.roboflow.com/new-ocean-is/safety-helmet-detection-jzl1b) — Roboflow Universe, por New Ocean IS
- **Licencia:** CC BY 4.0
- **Total de imágenes:** 1,726
- **Clases:** `helmet`, `no-helmet`, `wrong-helmet`
- **División:** 1,323 imágenes de entrenamiento / 403 imágenes de prueba

---

## Instrucciones para correr el código

1. Clona este repositorio:
   ```bash
   git clone https://github.com/ChristianZarate/AV_Project_3P.git
   ```
2. Instala las dependencias:
   ```bash
   pip install -r requirements.txt
   ```
3. Abre el notebook `Helmet_Detection_YOLOv8.ipynb` en [Google Colab](https://colab.research.google.com).
4. Activa el acelerador de hardware **GPU (T4)**: `Entorno de ejecución → Cambiar tipo de entorno de ejecución → GPU T4`.
5. Ejecuta las celdas en orden (`Entorno de ejecución → Ejecutar todo`). El notebook se encarga de:
   - Instalar las librerías necesarias (`ultralytics`, `roboflow`)
   - Descargar el dataset desde Roboflow
   - Entrenar el modelo YOLOv8
   - Evaluar el modelo entrenado
   - Generar imágenes de prueba con las detecciones (bounding boxes)

> El entrenamiento toma aproximadamente 15-20 minutos usando la GPU gratuita de Google Colab.

---

## Resultados del modelo

El modelo fue entrenado por 50 épocas usando **YOLOv8n** (nano) como arquitectura base.

| Métrica | Valor |
|---|---|
| **mAP50 (general)** | 0.941 |
| **mAP50-95 (general)** | 0.658 |
| **Precisión** | 0.946 |
| **Recall** | 0.869 |

### Resultados por clase

| Clase | mAP50 |
|---|---|
| helmet | 0.945 |
| no-helmet | 0.935 |
| wrong-helmet | 0.942 |

Las imágenes con las detecciones (bounding boxes) generadas por el modelo se encuentran en la carpeta [`evidencias/`](./evidencias).

---

## Caso de Estudio: Aplicación en la vida real

### Problema a resolver

En obras de construcción y plantas industriales, el incumplimiento del uso de casco de seguridad es una de las principales causas de accidentes laborales graves. La supervisión manual del cumplimiento de esta norma depende de personal de seguridad que no puede vigilar todos los accesos y zonas de trabajo en todo momento, lo que genera puntos ciegos donde ocurren la mayoría de las infracciones.

Este proyecto propone un sistema de **monitoreo automático y continuo** del uso de casco en los puntos de acceso a la obra, reduciendo la dependencia de supervisión humana constante y generando un registro objetivo del cumplimiento de seguridad.

### Hardware propuesto

| Componente | Descripción |
|---|---|
| **Cámara** | Cámara IP fija de vigilancia (1080p mínimo), instalada en la **entrada principal de la obra/fábrica**, a una altura de ~2.5 m con ángulo descendente para capturar el rostro y la parte superior del cuerpo de cada persona que ingresa. |
| **Procesador** | Mini PC o Jetson Nano (NVIDIA) conectado localmente a la cámara, donde corre el modelo YOLOv8 entrenado para hacer inferencia en tiempo real sobre el video. |
| **Conectividad** | Conexión Wi-Fi/Ethernet local para enviar alertas al servidor de la obra. |
| **Salida** | Pantalla o tablet en la caseta de control, donde el supervisor recibe la notificación. |

### Flujo de funcionamiento

1. **Captura:** la cámara fija en la entrada graba video de forma continua durante el horario laboral.
2. **Inferencia:** cada cierto número de frames, el procesador local corre el modelo YOLOv8 sobre la imagen capturada, identificando a las personas presentes y clasificando si llevan casco (`helmet`), no lo llevan (`no-helmet`), o lo llevan mal puesto (`wrong-helmet`).
3. **Decisión:** si el modelo detecta una persona en clase `no-helmet` o `wrong-helmet` con un nivel de confianza superior al 70%, el sistema dispara una alerta.
4. **Notificación:** la alerta se envía automáticamente a una **app/dashboard de seguridad** usada por el supervisor de obra, incluyendo:
   - Hora y ubicación de la detección
   - Captura de la imagen con el bounding box marcado
   - Clasificación de la infracción
5. **Acción humana:** el supervisor, al recibir la notificación, puede acercarse físicamente a la persona para corregir la situación antes de que ingrese a zonas de riesgo.
6. **Registro:** todas las detecciones (cumplimiento e incumplimiento) se almacenan en una base de datos para generar reportes de seguridad periódicos, útiles para auditorías internas o de la aseguradora.

### Beneficios esperados

- Reduce la dependencia de vigilancia humana constante en los accesos.
- Genera evidencia objetiva y con marca de tiempo del cumplimiento de seguridad.
- Permite detectar patrones (ej. una cuadrilla específica con incumplimientos recurrentes) para reforzar la capacitación donde más se necesita.

---

## Estructura del repositorio

```
AV_Project_3P/
├── README.md                          # Este archivo
├── requirements.txt                   # Dependencias del proyecto
├── Helmet_Detection_YOLOv8.ipynb      # Notebook con todo el código (entrenamiento + evaluación)
└── evidencias/                        # Imágenes de prueba con detecciones (bounding boxes)
```
