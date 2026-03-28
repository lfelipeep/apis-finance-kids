# 🐷 Finance Kids — Plataforma Educativa de Finanzas

> **Aprende finanzas como un juego.** Finance Kids es una plataforma educativa para niños inspirada en Duolingo, donde aprenden conceptos financieros a través de lecciones cortas, quizzes interactivos, monedas de recompensa e insignias de logros.

<br>

## 📌 Tabla de Contenidos

- [¿Qué es Finance Kids?](#-qué-es-finance-kids)
- [Tecnologías](#-tecnologías)
- [Estructura del Proyecto](#-estructura-del-proyecto)
- [APIs Implementadas](#-apis-implementadas)
  - [gRPC — API Asignada](#-grpc--api-asignada)
  - [REST — API Adicional](#-rest--api-adicional)
- [¿Por qué estas dos APIs?](#-por-qué-estas-dos-apis)
- [Instalación y Ejecución](#-instalación-y-ejecución)
  - [Requisitos Previos](#requisitos-previos)
  - [Ejecutar API REST](#1-ejecutar-api-rest)
  - [Ejecutar API gRPC](#2-ejecutar-api-grpc)
- [Interfaces de Prueba (HTML)](#-interfaces-de-prueba-html)
- [Endpoints y Servicios](#-endpoints-y-servicios)
- [Lecciones Disponibles](#-lecciones-disponibles)
- [Estudiantes de Prueba](#-estudiantes-de-prueba)
- [Equipo](#-equipo)

<br>

---

## 🎯 ¿Qué es Finance Kids?

Finance Kids es una aplicación educativa donde los niños pueden:

- 📚 **Tomar lecciones** sobre dinero, ahorro, presupuesto y bancos
- ❓ **Responder quizzes** con retroalimentación inmediata y explicaciones
- 🪙 **Ganar monedas** por cada respuesta correcta
- 🏆 **Desbloquear insignias** por logros de aprendizaje (como Duolingo)
- 🔥 **Mantener rachas** de días practicando
- 📊 **Ver su progreso** y compararse con otros en el ranking

El proyecto implementa **dos tipos de API** para demostrar cómo arquitecturas reales combinan diferentes protocolos según el caso de uso.

<br>

---

## 🛠 Tecnologías

| Tecnología | Uso |
|---|---|
| Python 3.10+ | Lenguaje principal |
| gRPC + Protocol Buffers | API asignada — comunicación binaria eficiente |
| Flask | Servidor REST y puente gRPC↔HTTP |
| HTML / CSS / JavaScript | Interfaces de prueba visual |
| requests | Cliente HTTP para la API REST |

<br>

---

## 📁 Estructura del Proyecto

```
finance_kids_api/
│
├── grpc/                          # API gRPC (asignada)
│   ├── finance_kids.proto         # Esquema de datos y servicios gRPC
│   ├── grpc_server.py             # Servidor gRPC (puerto 50051)
│   ├── grpc_client.py             # Cliente interactivo en consola
│   ├── grpc_bridge.py             # Puente HTTP→gRPC para el HTML (puerto 5001)
│   ├── test_grpc.html             # Interfaz visual de prueba
│   └── requirements.txt
│
├── rest/                          # API REST (adicional)
│   ├── rest_server.py             # Servidor REST Flask (puerto 5000)
│   ├── rest_client.py             # Cliente interactivo en consola
│   ├── test_rest.html             # Interfaz visual de prueba
│   └── requirements.txt
│
├── justificacion_REST.md          # Justificación de la API adicional elegida
└── README.md
```

<br>

---

## 📡 APIs Implementadas

### ⚡ gRPC — API Asignada

**gRPC** (Google Remote Procedure Call) es un framework de comunicación de alto rendimiento que usa **Protocol Buffers** como formato de datos binario y **HTTP/2** como protocolo de transporte.

#### ¿Cómo funciona?

En lugar de llamar a una URL como en REST, el cliente llama a **funciones remotas** directamente, como si fueran funciones locales de Python. El esquema de datos se define en un archivo `.proto` que actúa como contrato entre cliente y servidor.

#### ¿Por qué usamos gRPC en Finance Kids?

gRPC es ideal para el **núcleo del aprendizaje** porque:
- Es **muy rápido** — los datos viajan en formato binario, no texto
- Tiene **tipado estricto** — el `.proto` garantiza que los datos siempre tengan el formato correcto
- Es ideal para la **comunicación interna** entre servicios del backend
- Permite **streaming** de datos en tiempo real (útil para actualizar progreso)

#### Servicios gRPC implementados

| Servicio | Descripción |
|---|---|
| `GetLesson` | Obtiene el contenido completo de una lección con su quiz |
| `ListLessons` | Lista todas las lecciones disponibles en la plataforma |
| `SubmitQuizAnswer` | Evalúa la respuesta de un niño y devuelve feedback + monedas |
| `GetProgress` | Retorna el progreso completo de aprendizaje de un estudiante |
| `GetBadges` | Devuelve las insignias desbloqueadas y bloqueadas de un estudiante |

<br>

---

### 🌐 REST — API Adicional

**REST** (Representational State Transfer) es el estándar más utilizado para APIs web. Usa el protocolo **HTTP** con métodos como GET, POST, PUT y DELETE, y devuelve datos en formato **JSON**.

#### ¿Cómo funciona?

El cliente hace peticiones a **URLs específicas** (endpoints) usando métodos HTTP. El servidor responde con JSON que el cliente procesa.

#### ¿Por qué elegimos REST como API adicional?

REST complementa a gRPC cubriendo la **capa pública y accesible** de la plataforma:
- Cualquier navegador, app móvil o herramienta puede consumirla **sin instalar nada especial**
- Es el estándar en aplicaciones educativas como Duolingo, Khan Academy y Brilliant
- Permite implementar el **panel de padres y maestros** fácilmente
- Las respuestas JSON son **legibles y debuggeables** directamente en el navegador

#### Endpoints REST implementados

| Método | Endpoint | Descripción |
|---|---|---|
| `GET` | `/api/health` | Estado del servidor |
| `GET` | `/api/lecciones` | Catálogo de lecciones (acepta filtros) |
| `GET` | `/api/lecciones/<id>` | Detalle de una lección específica |
| `POST` | `/api/estudiantes` | Registrar un nuevo estudiante |
| `GET` | `/api/estudiantes/<kid_id>` | Ver perfil y progreso de un estudiante |
| `POST` | `/api/respuestas` | Enviar respuesta de quiz y recibir feedback |
| `GET` | `/api/ranking` | Tabla de clasificación global |

<br>

---

## 🤔 ¿Por qué estas dos APIs?

La combinación **gRPC + REST** no es redundante — refleja cómo funcionan los sistemas educativos reales:

```
┌─────────────────────────────────────────────────────────┐
│                    FINANCE KIDS                         │
│                                                         │
│   Niño usa app          Panel de padres / maestros      │
│        │                        │                       │
│        ▼                        ▼                       │
│   ┌─────────┐              ┌─────────┐                  │
│   │  gRPC   │              │  REST   │                  │
│   │  rápido │              │ abierto │                  │
│   │ binario │              │  JSON   │                  │
│   └────┬────┘              └────┬────┘                  │
│        │                        │                       │
│        └──────────┬─────────────┘                       │
│                   ▼                                     │
│              Backend / Base de datos                    │
└─────────────────────────────────────────────────────────┘
```

| Característica | gRPC | REST |
|---|---|---|
| Protocolo | HTTP/2 + Binario | HTTP/1.1 + JSON |
| Velocidad | ⚡ Muy alta | 🟢 Alta |
| Legibilidad | Requiere herramientas | Directo en el navegador |
| Caso de uso | Motor de aprendizaje interno | Interfaces públicas y panel |
| Compatibilidad | Clientes gRPC | Cualquier cliente HTTP |

<br>

---

## 🚀 Instalación y Ejecución

### Requisitos Previos

1. Instalar **Python 3.10+** desde [python.org](https://www.python.org/downloads/)
   > ⚠️ Durante la instalación en Windows, marcar la casilla **"Add Python to PATH"**

2. Verificar instalación abriendo PowerShell o CMD:
```bash
python --version
```

3. Extraer el ZIP del proyecto (clic derecho → Extraer todo)

<br>

---

### 1. Ejecutar API REST

La API REST solo requiere **una terminal**.

**Paso 1 —  entrar a la carpeta:**
```bash
cd "cd finance_kids_api\rest"
```

**Paso 2 — Instalar dependencias:**
```bash
pip install flask requests
```

**Paso 3 — Iniciar el servidor:**
```bash
python rest_server.py
```

✅ El servidor está corriendo cuando ves:
```
🐷 FINANCE KIDS — Servidor REST iniciado
📡 URL base: http://localhost:5000
* Debugger is active!
```

> 💡 Ahora abre `test_rest.html` con doble clic en el explorador de archivos para usar la interfaz visual.

**Opcional — Cliente en consola:**
```bash
# En una segunda terminal:
python rest_client.py
```

<br>

---

### 2. Ejecutar API gRPC

La API gRPC requiere **dos terminales** abiertas simultáneamente, más un paso de compilación inicial.

#### Primera vez (compilar el esquema .proto)

**Paso 1 — Terminal 1 — Compilar el proto (solo la primera vez)**
```bash
cd "C:\Users\luisf\Downloads\apis trabajo\finance_kids_api\grpc"
pip install grpcio grpcio-tools
python -m grpc_tools.protoc -I. --python_out=. --grpc_python_out=. finance_kids.proto"

```

**Paso 2 — Cuando termine sin errores, en esa misma terminal::
```bash
python grpc_server.py
```

Esto genera automáticamente:
- `finance_kids_pb2.py` — clases de los mensajes
- `finance_kids_pb2_grpc.py` — clases del servicio

<br>

#### Terminal 2 — El puente (nueva ventana PowerShell):

```bash
cd "cd "C:\Users\luisf\Downloads\apis trabajo\finance_kids_api\grpc"
python grpc_bridge.py"

```

✅ Corriendo cuando ves:
```
🐷 FINANCE KIDS — Servidor gRPC iniciado
📡 Puerto: 50051
```

---

### Resumen — Puertos y Terminales

| Terminal | Archivo | Puerto | Para qué |
|---|---|---|---|
| 1 | `rest_server.py` | `5000` | Servidor REST |
| 2 | `grpc_server.py` | `50051` | Servidor gRPC |
| 3 | `grpc_bridge.py` | `5001` | Puente para el HTML |

> ⚠️ **No cierres las terminales** mientras usas los HTML — los servidores deben permanecer activos.

<br>

---

## 🖥 Interfaces de Prueba (HTML)

Ambos archivos HTML se abren con **doble clic** en el explorador de archivos, sin necesidad de instalar nada adicional.

### `rest/test_rest.html`
Interfaz visual verde para testear todos los endpoints REST:
- Health check del servidor
- Catálogo de lecciones con filtros por categoría y dificultad
- Quiz interactivo con botones que se ponen verde/rojo según la respuesta
- Formulario de registro de nuevos estudiantes
- Perfil con barra de progreso animada
- Tabla de clasificación con medallas 🥇🥈🥉

### `grpc/test_grpc.html`
Interfaz visual morada para testear todos los servicios gRPC (vía puente):
- ListLessons con tarjetas clickeables
- GetLesson con respuesta JSON formateada
- SubmitQuizAnswer con quiz interactivo pregunta por pregunta
- GetProgress con barras de progreso por lección
- GetBadges con tarjetas de insignias desbloqueadas/bloqueadas

<br>

---

## 📚 Lecciones Disponibles

| ID | Título | Categoría | 🪙 Monedas | Dificultad |
|---|---|---|---|---|
| L001 | ¿Qué es el dinero? 💰 | conceptos básicos | 10 | fácil |
| L002 | El superpoder del ahorro 🐷 | ahorro | 15 | fácil |
| L003 | Necesidades vs. Deseos 🍕🎮 | presupuesto | 15 | medio |
| L004 | Mi primer presupuesto 📊 | presupuesto | 20 | medio |
| L005 | ¿Qué es un banco? 🏦 | bancos | 10 | fácil |

<br>

---

## 👤 Estudiantes de Prueba

| kid_id | Nombre | Monedas | Lecciones completadas |
|---|---|---|---|
| `kid_001` | Valentina | 35 🪙 | L001, L002 |
| `kid_002` | Santiago | 10 🪙 | L001 |

<br>

---

## 🏅 Sistema de Insignias

| Insignia | Condición |
|---|---|
| 🎉 Primera lección | Completar al menos 1 lección |
| ⭐ Ahorrador estrella | Completar L002 con 100% de aciertos |
| 🔥 Racha de 3 días | Practicar 3 días seguidos |
| 📊 Experto en presupuesto | Completar L003 y L004 |
| 🏆 Finance Kids Master | Completar las 5 lecciones |

<br>

---

## 👥 Equipo

**Finance Kids** — Proyecto de APIs  
Universidad · 2025

> *"Aprender finanzas desde pequeño es el mejor superpoder."* 🐷

---

<p align="center">
  Hecho con 💚 por el equipo Finance Kids
</p>
