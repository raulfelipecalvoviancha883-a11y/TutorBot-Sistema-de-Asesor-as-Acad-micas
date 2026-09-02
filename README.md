# TutorBot – Sistema de Asesorías Académicas 🤖📚

**TutorBot** es una solución automatizada desarrollada en **n8n** diseñada para optimizar, centralizar y agilizar el proceso de coordinación de asesorías académicas. Conecta a estudiantes y tutores en tiempo real mediante un bot conversacional de Telegram, utilizando Google Sheets como base de datos persistente.

---

## 📌 Tabla de Contenidos
- [Introducción](#-introducción)
- [Objetivos del Proyecto](#-objetivos-del-proyecto)
- [Arquitectura del Sistema](#-arquitectura-del-sistema)
- [Modelo de Datos (Google Sheets)](#-modelo-de-datos-google-sheets)
- [Flujo Conversacional (Wizard)](#-flujo-conversacional-wizard)
- [Resultados Esperados](#-resultados-esperados)
- [Requisitos e Instalación](#-requisitos-e-instalación)
- [Estructura del Repositorio](#-estructura-del-repositorio)

---

## 📖 Introducción
En el entorno educativo actual, la coordinación de asesorías suele ser caótica y manual, generando cruces de horarios, desatención de materias críticas y falta de trazabilidad.

**TutorBot** resuelve este problema ofreciendo un motor de asignación inteligente que gestiona desde la solicitud inicial hasta la finalización de la asesoría. Garantiza que cada materia cuente con el tutor adecuado en el horario disponible, optimizando el recurso humano y mejorando el rendimiento académico.

---

## 🎯 Objetivos del Proyecto
* **Automatización:** Desarrollar un sistema integrado con Telegram, Google Sheets y lógica avanzada en n8n.
* **Motor de Matching:** Implementar la asociación automática entre **materia**, **tutor** y **horario disponible**.
* **Interfaz Conversacional:** Diseñar un bot en Telegram para autogestión del estudiante (Solicitar, consultar, cancelar).
* **Control de Estados:** Automatizar el ciclo de vida de la tutoría (`Solicitada` ➔ `Asignada` ➔ `Confirmada` ➔ `Finalizada`).
* **Reportes & Trazabilidad:** Generar registros automáticos para la coordinación académica.
* **Validación en Tiempo Real:** Evitar cruces de agenda o doble reserva.

---

## 🏗️ Arquitectura del Sistema

El sistema consta de 3 componentes principales:

1. **Interfaz en Telegram:** Punto de contacto único donde el estudiante se registra, interactúa mediante menús numéricos, consulta estados y recibe alertas.
2. **Motor de Automatización (n8n):** Orquestador central que:
   * Mantiene el estado del usuario (Gestión de Sesiones / Wizard).
   * Ejecuta el motor de matching cruzando materia, fecha y disponibilidad.
   * Envía notificaciones automáticas a estudiantes y tutores.
3. **Modelo de Datos (Google Sheets):** Base de datos relacional `TutorBot_DB`.

---

## 🗄️ Modelo de Datos (`TutorBot_DB`)

La base de datos está organizada en 4 hojas de cálculo dentro de Google Sheets:

### 1. Hoja: `TUTORES`
| Campo | Tipo | Descripción | Ejemplo |
| :--- | :--- | :--- | :--- |
| `id_tutor` | String (PK) | Identificador único del tutor | `TUT-001` |
| `nombre` | String | Nombre completo | `Carlos Mendoza` |
| `especialidad_materias` | String (CSV) | Materias que imparte | `Python, SQL, Algoritmos` |
| `estado` | Enum | Estado del tutor | `Activo` / `Inactivo` |

### 2. Hoja: `DISPONIBILIDAD`
| Campo | Tipo | Descripción | Ejemplo |
| :--- | :--- | :--- | :--- |
| `id_dispo` | String (PK) | ID de la franja horaria | `DSP-102` |
| `id_tutor` | String (FK) | ID del tutor referenciado | `TUT-001` |
| `dia_semana` | String | Día de atención | `Lunes` |
| `hora_inicio` | Time | Hora inicial | `14:00` |
| `hora_fin` | Time | Hora final | `16:00` |
| `estado` | Enum | Estado del cupo | `Libre` / `Ocupado` |

### 3. Hoja: `TUTORIAS`
| Campo | Tipo | Descripción | Ejemplo |
| :--- | :--- | :--- | :--- |
| `id_tutoria` | String (PK) | Código de la cita | `TUT-2026-001` |
| `id_estudiante` | String (FK) | ID de Telegram del estudiante | `987654321` |
| `id_tutor` | String (FK) | ID del tutor asignado | `TUT-001` |
| `materia` | String | Materia seleccionada | `Python` |
| `fecha` | Date | Fecha programada | `2026-09-10` |
| `hora` | Time | Hora acordada | `14:00` |
| `estado` | Enum | Estado del servicio | `Asignada` |

### 4. Hoja: `SESSIONS`
| Campo | Tipo | Descripción | Ejemplo |
| :--- | :--- | :--- | :--- |
| `telegram_user` | String (PK) | ID de chat de Telegram | `987654321` |
| `pantalla_actual` | String | Estado actual dentro del bot | `SOLICITAR_FECHA` |
| `paso_actual` | Integer | Paso numérico del Wizard | `2` |
| `datos_parciales` | JSON | Variables temporales acumuladas | `{"materia":"Python"}` |

---

## 🔄 Flujo Conversacional (Wizard)

```text
[Inicio] ➔ 1. Seleccionar Materia ➔ 2. Ingresar Fecha (YYYY-MM-DD)
                ↓
    3. Motor Búsqueda (n8n) ➔ Valida disponibilidad en Google Sheets
                ↓
4. Confirmar Tutor y Horario ➔ 5. Notificación automática + Registro
Ciclo de Estados de la Tutoría:
Solicitada ➔ Asignada ➔ Confirmada ➔ Finalizada

🚀 Resultados Esperados
⚡ Reducción del 90% en el tiempo de asignación de tutorías.

📊 Trazabilidad total: Historial detallado de solicitudes, atención y cierre.

📈 Escalabilidad: Soporte para cientos de estudiantes y tutores en simultáneo.

👤 Experiencia de usuario fluida: Interfaz conversacional sin necesidad de manuales.

<img width="1416" height="433" alt="image" src="https://github.com/user-attachments/assets/1d94b946-89d7-49b8-97ee-eaf66230421b" />

