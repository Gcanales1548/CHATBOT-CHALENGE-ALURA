# CHATBOT-CHALENGE-ALURA
Challenge de Alura 
# Chatbot Inteligente de Soporte para Control Personal

## Descripción general del proyecto

Este proyecto implementa un chatbot inteligente de soporte para **Control Personal**, una plataforma SaaS orientada a la gestión de personal, tickets, turnos, reemplazos, niveles de servicio SLA, establecimientos, especialidades, reportes y procesos operativos.

El objetivo del chatbot es facilitar el acceso a la información contenida en la documentación oficial de la plataforma, permitiendo que usuarios, técnicos, supervisores, administradores y potenciales clientes realicen preguntas en lenguaje natural.

La solución utiliza una arquitectura **RAG, Retrieval-Augmented Generation**, que combina búsqueda semántica y generación de texto. Antes de generar una respuesta, el sistema busca información relevante dentro de los documentos cargados y utiliza esos fragmentos como contexto para el modelo de inteligencia artificial.

Esto permite reducir respuestas inventadas y mantener las respuestas vinculadas a la documentación oficial del producto.

---

## Objetivos

Los principales objetivos del proyecto son:

* Crear un asistente de soporte para Control Personal.
* Responder preguntas utilizando documentación oficial.
* Facilitar la búsqueda de información sobre módulos y funcionalidades.
* Evitar respuestas basadas únicamente en conocimiento general.
* Mostrar las fuentes documentales utilizadas.
* Mantener memoria básica de la conversación.
* Crear una interfaz sencilla y accesible mediante navegador web.
* Demostrar la implementación de una solución RAG funcional.

---

## Problema identificado

Los usuarios de una plataforma como Control Personal pueden tener dudas sobre:

* Creación y gestión de tickets.
* Prioridades y estados de atención.
* Cumplimiento de SLA.
* Funciones de cada rol.
* Turnos y reemplazos.
* Gestión multiestablecimiento.
* Reportes e indicadores.
* Planes disponibles.
* Privacidad y seguridad.
* Términos y condiciones.

Buscar manualmente esta información en varios documentos puede ser lento y poco práctico.

El chatbot permite consultar toda la documentación desde una única interfaz y obtener respuestas claras en pocos segundos.

---

## Solución implementada

La solución procesa documentos Word y PDF que contienen información oficial de Control Personal.

Los documentos son:

1. Base de Conocimiento de Control Personal.
2. Preguntas Frecuentes.
3. Política de Privacidad.
4. Planes y Precios.
5. Términos y Condiciones.

El sistema extrae el texto de estos archivos, lo divide en fragmentos, crea representaciones vectoriales y almacena la información en una base vectorial FAISS.

Cuando el usuario realiza una pregunta, el sistema recupera los fragmentos más relacionados y los envía al modelo Gemini para generar una respuesta contextualizada.

---

# Arquitectura de la solución

## Diagrama general

```text
Documentos DOCX y PDF
        │
        ▼
Extracción y limpieza del texto
        │
        ▼
División en fragmentos
        │
        ▼
Generación de embeddings locales
        │
        ▼
Base vectorial FAISS
        │
        ▼
Pregunta del usuario
        │
        ▼
Búsqueda semántica
        │
        ▼
Recuperación de fragmentos relevantes
        │
        ▼
Construcción del contexto
        │
        ▼
Modelo Gemini
        │
        ▼
Respuesta y fuentes documentales
        │
        ▼
Interfaz Gradio
```

---

## Componentes de la arquitectura

### 1. Carga de documentos

El sistema permite cargar documentos en los siguientes formatos:

* `.docx`
* `.pdf`

Los archivos contienen la información que forma la base de conocimiento del chatbot.

---

### 2. Extracción de contenido

La información se extrae utilizando cargadores de documentos de LangChain:

* `Docx2txtLoader` para archivos Word.
* `PyPDFLoader` para archivos PDF.

Durante esta etapa también se guardan metadatos como:

* Nombre del archivo.
* Tipo de documento.
* Número de página.
* Número de fragmento.

---

### 3. Limpieza del texto

El contenido extraído se procesa para eliminar:

* Espacios innecesarios.
* Tabulaciones repetidas.
* Saltos de línea excesivos.
* Bloques vacíos.

Esto mejora la calidad de los fragmentos y de la búsqueda semántica.

---

### 4. División en fragmentos

Los documentos se dividen en fragmentos de texto mediante `RecursiveCharacterTextSplitter`.

Cada fragmento tiene aproximadamente:

* 1.000 caracteres.
* 200 caracteres de superposición.

La superposición evita que una idea quede completamente separada entre dos fragmentos consecutivos.

---

### 5. Generación de embeddings

Los embeddings se generan de forma local utilizando el modelo:

```text
sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2
```

Este modelo transforma cada fragmento en una representación numérica que conserva su significado semántico.

El modelo es multilingüe y puede trabajar correctamente con documentos y preguntas en español.

---

### 6. Almacenamiento vectorial

Los embeddings se almacenan en una base vectorial FAISS.

FAISS permite comparar la pregunta del usuario con los fragmentos documentales y encontrar aquellos que tienen mayor similitud semántica.

---

### 7. Recuperación de información

Cuando el usuario realiza una consulta, el sistema:

1. Convierte la pregunta en un embedding.
2. Compara ese vector con los almacenados en FAISS.
3. Selecciona los fragmentos más relacionados.
4. Recupera el contenido y sus metadatos.
5. Construye un contexto documental.

---

### 8. Generación de la respuesta

El contexto recuperado se envía a un modelo Gemini mediante el SDK oficial `google-genai`.

El modelo recibe instrucciones para:

* Responder únicamente con información del contexto.
* No inventar funcionalidades ni condiciones.
* Entregar procedimientos paso a paso cuando corresponda.
* Responder en español.
* Indicar cuando la información no se encuentra disponible.

---

### 9. Interfaz del chatbot

La interfaz se construye con Gradio.

Permite:

* Escribir preguntas.
* Visualizar respuestas.
* Mantener un historial básico.
* Probar preguntas predefinidas.
* Acceder al chatbot mediante un enlace web temporal.

---

# Tecnologías y herramientas utilizadas

## Lenguaje

* Python 3

## Entorno de ejecución

* Google Colab

## Inteligencia artificial

* Google Gemini API
* SDK oficial `google-genai`

## Procesamiento documental

* LangChain Community
* `Docx2txtLoader`
* `PyPDFLoader`
* `RecursiveCharacterTextSplitter`

## Embeddings

* Hugging Face
* Sentence Transformers
* `paraphrase-multilingual-MiniLM-L12-v2`

## Base vectorial

* FAISS

## Interfaz

* Gradio

## Formatos soportados

* DOCX
* PDF

---

# Estructura recomendada del repositorio

```text
chatbot-control-personal/
│
├── README.md
├── Chatbot_Control_Personal.ipynb
│
├── documentos/
│   ├── Base_de_Conocimiento_Control_Personal.docx
│   ├── FAQ_Control_Personal.docx
│   ├── Politica_Privacidad_Control_Personal.docx
│   ├── Planes_y_Precios_Control_Personal.docx
│   └── Terminos_y_Condiciones_Control_Personal.docx
│
├── capturas/
│   ├── carga_documentos.png
│   ├── base_vectorial.png
│   ├── chatbot_funcionando.png
│   └── respuesta_con_fuentes.png
│
└── requirements.txt
```

---

# Requisitos previos

Para ejecutar el proyecto se necesita:

* Una cuenta de Google.
* Acceso a Google Colab.
* Una API Key de Google Gemini.
* Los documentos de Control Personal en formato DOCX o PDF.
* Conexión a internet.

---

# Obtener una API Key de Gemini

1. Ingresar a Google AI Studio.
2. Crear o seleccionar un proyecto.
3. Generar una API Key.
4. Copiar la clave.
5. Abrir el notebook en Google Colab.
6. Seleccionar el ícono de llave en la barra lateral.
7. Crear un secreto llamado:

```text
GOOGLE_API_KEY
```

8. Pegar la API Key como valor.
9. Habilitar el acceso del notebook al secreto.

La clave no debe escribirse directamente en el código ni publicarse en el repositorio.

---

# Instrucciones para ejecutar el proyecto

## Opción 1: ejecutar en Google Colab

### Paso 1: abrir el notebook

Subir o abrir el archivo:

```text
Chatbot_Control_Personal.ipynb
```

### Paso 2: configurar la clave

Crear el secreto `GOOGLE_API_KEY` en Google Colab.

### Paso 3: ejecutar el código

Ejecutar la celda principal del notebook.

El sistema instalará automáticamente las librerías requeridas.

### Paso 4: cargar los documentos

Cuando Colab muestre el selector de archivos, cargar los documentos Word o PDF de Control Personal.

### Paso 5: esperar el procesamiento

El sistema realizará las siguientes tareas:

1. Lectura de documentos.
2. Limpieza del contenido.
3. División en fragmentos.
4. Descarga del modelo de embeddings.
5. Creación de la base vectorial FAISS.
6. Selección de un modelo Gemini disponible.
7. Prueba automática del chatbot.

### Paso 6: abrir la interfaz

Al finalizar, Gradio mostrará una dirección similar a:

```text
Running on public URL: https://xxxxxxxx.gradio.live
```

Abrir ese enlace en el navegador.

---

## Opción 2: ejecutar localmente

También es posible ejecutar el proyecto en un computador local.

### Crear un entorno virtual

```bash
python -m venv venv
```

### Activar el entorno en Windows

```bash
venv\Scripts\activate
```

### Activar el entorno en Linux o macOS

```bash
source venv/bin/activate
```

### Instalar dependencias

```bash
pip install google-genai
pip install langchain-community
pip install langchain-text-splitters
pip install langchain-huggingface
pip install sentence-transformers
pip install faiss-cpu
pip install docx2txt
pip install pypdf
pip install gradio
```

### Configurar la variable de entorno

En Windows PowerShell:

```powershell
$env:GOOGLE_API_KEY="TU_API_KEY"
```

En Linux o macOS:

```bash
export GOOGLE_API_KEY="TU_API_KEY"
```

Después se debe adaptar la sección de carga de documentos, ya que `google.colab.files` solo funciona en Google Colab.

---

# Ejemplos de preguntas que el agente puede responder

El chatbot puede responder preguntas como:

```text
¿Qué es Control Personal?
```

```text
¿Cómo se crea un ticket?
```

```text
¿Qué prioridades tienen los tickets?
```

```text
¿Qué funciones tiene un supervisor?
```

```text
¿Qué puede hacer un administrador?
```

```text
¿Cómo funciona el cumplimiento de SLA?
```

```text
¿Qué ocurre cuando un ticket supera su tiempo de atención?
```

```text
¿Cómo se asigna un reemplazo?
```

```text
¿Cómo funciona el sistema de turnos?
```

```text
¿Control Personal permite administrar varios establecimientos?
```

```text
¿Qué incluye el plan Enterprise?
```

```text
¿Cómo se protegen los datos personales?
```

```text
¿Qué información almacena la plataforma?
```

```text
¿Quién puede crear nuevos usuarios?
```

```text
¿Se pueden generar reportes?
```

```text
¿Control Personal puede utilizarse desde un teléfono móvil?
```

---

# Ejemplos de respuestas generadas

## Ejemplo 1

### Pregunta

```text
¿Qué es Control Personal?
```

### Respuesta generada

```text
Control Personal es una plataforma SaaS orientada a la gestión
de personal y procesos operativos.

Permite administrar tickets, turnos, reemplazos, niveles de
servicio SLA, establecimientos, especialidades, usuarios,
reportes y notificaciones.

La plataforma está diseñada para organizaciones que necesitan
centralizar la asignación de tareas, supervisar tiempos de
respuesta y mantener trazabilidad sobre las actividades
realizadas.
```

### Fuentes

```text
Base_de_Conocimiento_Control_Personal.docx
FAQ_Control_Personal.docx
```

---

## Ejemplo 2

### Pregunta

```text
¿Cómo se crea un ticket?
```

### Respuesta generada

```text
Para crear un ticket en Control Personal debes seguir estos pasos:

1. Ingresar a la plataforma con una cuenta autorizada.
2. Acceder al módulo de Tickets.
3. Seleccionar la opción Nuevo ticket.
4. Completar la descripción de la solicitud.
5. Elegir el establecimiento y la especialidad correspondiente.
6. Seleccionar la prioridad del ticket.
7. Adjuntar fotografías o evidencias, si corresponde.
8. Guardar la solicitud.

Después de crear el ticket, este podrá ser asignado a un técnico
o gestionado según las reglas definidas por el establecimiento.
```

### Fuentes

```text
Base_de_Conocimiento_Control_Personal.docx
FAQ_Control_Personal.docx
```

---

## Ejemplo 3

### Pregunta

```text
¿Qué prioridades tienen los tickets?
```

### Respuesta generada

```text
Los tickets de Control Personal pueden clasificarse en cuatro
niveles de prioridad:

- Baja.
- Media.
- Alta.
- Crítica.

La prioridad permite definir la urgencia de la solicitud y los
tiempos de respuesta asociados al nivel de servicio SLA.
```

### Fuentes

```text
Base_de_Conocimiento_Control_Personal.docx
FAQ_Control_Personal.docx
```

---

## Ejemplo 4

### Pregunta

```text
¿Qué incluye el plan Enterprise?
```

### Respuesta generada

```text
El plan Enterprise está orientado a organizaciones que necesitan
una implementación de mayor escala.

Entre sus principales características se encuentran:

- Gestión multiestablecimiento.
- Usuarios ilimitados, de acuerdo con la configuración comercial.
- Acceso a API e integraciones.
- Personalización de marca.
- Soporte dedicado.
- Funcionalidades avanzadas de administración.

Las condiciones finales deben validarse con la propuesta
comercial vigente.
```

### Fuentes

```text
Planes_y_Precios_Control_Personal.docx
Base_de_Conocimiento_Control_Personal.docx
```

---

## Ejemplo 5

### Pregunta

```text
¿Cómo se protegen los datos personales?
```

### Respuesta generada

```text
Control Personal contempla diferentes medidas para proteger la
información almacenada en la plataforma.

Entre ellas se incluyen:

- Control de acceso mediante usuarios y roles.
- Restricción de módulos según el perfil.
- Cifrado de las comunicaciones.
- Respaldos periódicos.
- Registro de actividades y auditoría.
- Tratamiento confidencial de la información.

Los datos se utilizan para prestar el servicio, autenticar
usuarios, generar reportes y mantener la trazabilidad de las
operaciones.
```

### Fuentes

```text
Politica_Privacidad_Control_Personal.docx
Terminos_y_Condiciones_Control_Personal.docx
```

---

## Ejemplo 6: información no encontrada

### Pregunta

```text
¿Cuál es el precio exacto del plan Enterprise?
```

### Respuesta generada

```text
No encontré esta información en la documentación oficial de
Control Personal. Te recomiendo contactar al administrador o
al equipo de soporte.
```

Este comportamiento demuestra que el chatbot intenta evitar la generación de información no documentada.

---

# Características principales

* Consulta de documentación en lenguaje natural.
* Búsqueda semántica.
* Arquitectura RAG.
* Soporte para documentos Word y PDF.
* Embeddings locales.
* Base vectorial FAISS.
* Generación de respuestas con Gemini.
* Selección automática de un modelo disponible.
* Visualización de fuentes.
* Historial básico de conversación.
* Interfaz web mediante Gradio.
* Control de respuestas no documentadas.
* Respuestas en español.

---

# Limitaciones

La versión actual presenta las siguientes limitaciones:

* La interfaz pública de Gradio es temporal.
* La base vectorial se genera nuevamente cuando se reinicia Colab.
* Los documentos deben cargarse manualmente.
* La calidad de las respuestas depende de la calidad de la documentación.
* La detección de información no relacionada utiliza un umbral configurable.
* La memoria de conversación solo se mantiene durante la sesión.
* La solución no incluye autenticación de usuarios.
* No existe todavía una integración directa con la aplicación Control Personal.

---

# Posibles mejoras futuras

Entre las mejoras que podrían implementarse se encuentran:

* Integración directa dentro de Control Personal.
* Autenticación por usuario y establecimiento.
* Filtrado documental según rol.
* Base vectorial persistente en la nube.
* Panel de administración de documentos.
* Carga automática de nuevas versiones.
* Registro de preguntas sin respuesta.
* Panel de métricas del chatbot.
* Calificación de respuestas.
* Escalamiento de consultas a soporte humano.
* Integración con sistema de tickets.
* Respuestas diferentes según el plan contratado.
* Identificación automática del establecimiento.
* Integración con una base de datos operacional.
* Despliegue permanente en un servidor o servicio cloud.

---

# Seguridad

La API Key de Gemini debe almacenarse como secreto o variable de entorno.

Nunca se debe publicar una clave dentro de:

* El código fuente.
* El archivo README.
* Capturas de pantalla.
* Archivos del repositorio.
* Historial de commits.

También se recomienda evitar el uso de documentos que contengan:

* Contraseñas.
* API Keys.
* Datos personales sensibles.
* Información confidencial no autorizada.
* Credenciales de acceso.
* Configuraciones internas críticas.

---

# Conclusiones

El proyecto demuestra cómo utilizar inteligencia artificial generativa junto con recuperación documental para construir un chatbot de soporte especializado.

La arquitectura RAG permite mejorar la confiabilidad de las respuestas al obligar al modelo a utilizar información obtenida desde documentos oficiales.

La solución desarrollada cumple con los objetivos del Challenge porque incorpora:

* Documentación propia.
* Procesamiento de archivos.
* Fragmentación de texto.
* Embeddings.
* Búsqueda vectorial.
* Recuperación semántica.
* Generación de respuestas.
* Visualización de fuentes.
* Interfaz conversacional.

Además de cumplir con el Challenge, el proyecto puede servir como base para implementar un asistente de soporte real dentro de Control Personal.

---

# Autor

**Gino Canales**

Proyecto desarrollado como parte de un Challenge de Inteligencia Artificial y generación aumentada por recuperación.

---

# Licencia

Este proyecto fue desarrollado con fines educativos y demostrativos.

La documentación, nombre, diseño y funcionalidades asociadas a Control Personal pertenecen a sus respectivos propietarios.
