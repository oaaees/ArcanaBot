# TODO LIST

## 1\. Planificación y Preparación del Contenido

* **Define el Alcance y las Funcionalidades Clave:**
  * [ ] ¿Qué tipos de tiradas de tarot ofrecerá el bot? (Ej. una carta diaria, tres cartas: pasado-presente-futuro, cruz celta, etc.).
  * [ ] ¿La IA solo interpretará las cartas, o también permitirá una conversación más profunda sobre la lectura?
  * [ ] ¿Se guardarán las lecturas de los usuarios para un historial personal? Si es así, ¿cómo (base de datos simple, archivo)?
  * [ ] ¿Cuál será el tono de las interpretaciones de la IA (ej. sabio, alentador, neutral)?
* **Recopila y Estructura los Datos del Tarot:**
  * [ ] Consigue o crea las **imágenes de las 78 cartas del tarot** (Arcanos Mayores y Menores). Asegúrate de tener los derechos de uso.
  * [ ] **Compila los significados de cada carta** en un formato fácilmente accesible (ej. un archivo JSON, un diccionario de Python). Para cada carta, incluye:
    * Nombre de la carta.
    * Significado en posición **derecha**.
    * Significado en posición **invertida** (si aplica, lo cual es muy recomendable).
    * Palabras clave asociadas.
    * [ ] Estructura la información de las tiradas (ej. para la tirada de tres cartas: "Carta 1: Pasado", "Carta 2: Presente", "Carta 3: Futuro").

-----

## 2\. Configuración del Entorno y Obtención de Credenciales

* **Crea tu Bot de Telegram:**
  * [ ] Habla con **@BotFather** en Telegram y usa el comando `/newbot`.
  * [ ] Sigue los pasos para darle un nombre y un `username` a tu bot.
  * [ ] **Guarda el `TOKEN` de tu bot** de Telegram; es esencial para que tu código se conecte.
* **Obtén tu API Key de Google AI Studio (Gemini):**
  * [ ] Ve a [Google AI Studio](https://aistudio.google.com/) e inicia sesión con tu cuenta de Google.
  * [ ] Genera una nueva **API Key** para usar los modelos de Gemini. **Guárdala de forma segura.**
* **Prepara tu Entorno de Desarrollo Python:**
  * [ ] Asegúrate de tener **Python 3.8 o superior** instalado.
  * [ ] Crea y activa un **entorno virtual** para tu proyecto:

    ```bash
    python -m venv venv
    # En Linux/macOS:
    source venv/bin/activate
    # En Windows:
    .\venv\Scripts\activate
    ```
  
  * [ ] Instala las librerías necesarias:

    ```bash
    pip install python-telegram-bot
    pip install google-generativeai
    pip install python-dotenv # Para manejar tus credenciales de forma segura
    ```

* **Configura Variables de Entorno Seguras:**
  * [ ] Crea un archivo llamado `.env` en la raíz de tu proyecto.
  * [ ] Dentro de `.env`, añade tus tokens y claves:

    ```txt
    TELEGRAM_BOT_TOKEN="TU_TOKEN_DE_BOTFATHER"
    GOOGLE_API_KEY="TU_API_KEY_DE_GOOGLE_AI_STUDIO"
    ```

  * [ ] **Añade `.env` a tu archivo `.gitignore`** para evitar subir tus credenciales a repositorios públicos.

-----

## 3\. Desarrollo del Bot de Telegram (Python)

* **Estructura del Código Base (`main.py` o similar):**
  * [ ] Importa las librerías necesarias: `os`, `dotenv`, `telegram.ext`, `google.generativeai`.
  * [ ] Carga las variables de entorno al inicio del script.
  * [ ] Inicializa el `ApplicationBuilder` de `python-telegram-bot` con tu `TELEGRAM_BOT_TOKEN`.
* **Implementa la Lógica de Selección de Cartas:**
  * [ ] Crea una función que lea y cargue los datos de las cartas del tarot desde tu archivo (ej. JSON).
  * [ ] Desarrolla una función para **seleccionar aleatoriamente** el número de cartas requerido para una tirada (ej. 3 para pasado-presente-futuro).
  * [ ] Implementa una lógica para determinar si cada carta seleccionada está en posición **derecha o invertida** (ej. 50% de probabilidad para cada una).
  * [ ] Diseña una función para **enviar las imágenes** de las cartas seleccionadas al usuario en Telegram.
* **Crea los Manejadores de Comandos de Telegram:**
  * [ ] Define una función para el comando `/start` que envíe un mensaje de bienvenida y explique cómo usar el bot.
  * [ ] Crea una función para el comando `/help` que liste las funcionalidades y comandos disponibles.
  * [ ] Implementa una función para el comando `/tirada` (o similar) que inicie el proceso de selección de cartas y solicite la pregunta del usuario (si aplica).
  * [ ] Usa `CommandHandler` y `Application.add_handler()` para registrar estas funciones.

-----

## 4\. Integración con Google AI Studio (Gemini)

* **Configura el Modelo Gemini:**
  * [ ] Inicializa el modelo de Gemini con tu `GOOGLE_API_KEY`: `genai.configure(api_key=os.getenv("GOOGLE_API_KEY"))`.
  * [ ] Carga el modelo específico de Gemini que vas a usar (ej. `model = genai.GenerativeModel('gemini-pro')`).
* **Diseña el "Prompt" para la Interpretación de la IA:**
  * [ ] Esta es la parte más crítica. Crea una función que construya un **prompt detallado y efectivo** para el modelo de Gemini. El prompt debe incluir:
    * **El rol del bot/IA:** "Eres un sabio y experimentado lector de tarot..."
    * **Las cartas seleccionadas:** Nombre de la carta, su posición (derecha/invertida) y su significado base (que extrajiste de tus datos).
    * **El tipo de tirada** (ej. "Esta es una lectura de pasado, presente y futuro...").
    * **La pregunta específica del usuario** (si el usuario la proporciona).
    * **Instrucciones sobre el tono, la longitud y el formato** deseado de la respuesta (ej. "Sé empático, ofrece consejos prácticos, y no menciones que eres una IA").
* **Realiza la Llamada a la API de Gemini:**
  * [ ] Dentro de tu función de manejo de la tirada de tarot, envía el prompt generado al modelo de Gemini.
  * [ ] Maneja la respuesta recibida, extrayendo el texto de la interpretación.
  * [ ] Implementa bloques `try-except` para capturar y manejar posibles errores de la API (ej. límites de tasa, errores de conexión).
* **Envía la Interpretación de la IA al Usuario:**
  * [ ] Formatea la interpretación recibida de Gemini para que sea clara y agradable de leer en el chat de Telegram. Puedes usar **negritas**, **cursivas** y **saltos de línea** de Markdown.

-----

## 5\. Despliegue y Mantenimiento

* **Realiza Pruebas Exhaustivas:**
  * [ ] Prueba todas las funcionalidades: `/start`, `/help`, diferentes tipos de `/tirada`.
  * [ ] Verifica la calidad, coherencia y relevancia de las interpretaciones generadas por la IA para varias combinaciones de cartas.
  * [ ] Prueba el manejo de errores (ej. ¿qué pasa si la API de Gemini falla?).
* **Selecciona una Plataforma de Despliegue:**
  * [ ] Investiga opciones de hosting para bots de Python (ej. Heroku, Google Cloud Run, Replit, un VPS, PythonAnywhere). Considera la facilidad de uso y el costo.
* **Despliega tu Bot:**
  * [ ] Sigue las instrucciones específicas de la plataforma elegida para subir tu código y mantener tu bot funcionando 24/7.
  * [ ] Configura tus **variables de entorno** (`TELEGRAM_BOT_TOKEN`, `GOOGLE_API_KEY`) directamente en el entorno de despliegue, no en el código.
* **Monitoreo y Mejoras Continuas:**
  * [ ] Monitorea el uso de tu bot y el consumo de la API de Google AI Studio para controlar los costos.
  * [ ] Recopila feedback de los usuarios y úsalo para **refinar tus prompts de la IA**, mejorando la calidad de las interpretaciones.
  * [ ] Considera implementar un sistema de **logging** para depurar problemas y entender cómo interactúan los usuarios.
