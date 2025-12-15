# Sistema Automatizado de Reservas de Salas

> **Universidad Andrés Bello** - Agente Automatizado para Solicitud y Gestión de Salas de Reuniones

---

## 📋 Descripción

Sistema automatizado que permite solicitar, confirmar y cancelar reservas de salas de reuniones de forma simple y rápida. Está diseñado para apoyar la organización de reuniones internas, asegurando el uso correcto de los espacios disponibles.

### 🎯 Objetivo

Reducir conflictos de agenda y mejorar la eficiencia en la gestión de espacios compartidos, eliminando procesos manuales y mejorando la comunicación con los usuarios mediante notificaciones automáticas.

---

## ⚙️ Tecnologías Utilizadas

| Tecnología |                              Uso                                            |
|------------|-----------------------------------------------------------------------------|
| **n8n** | Automatización del flujo de reservas (con JavaScript)                          |
| **Google Sheets API** | Base de datos para almacenar reservas y verificar disponibilidad |
| **Gmail API** | Envío de notificaciones, confirmaciones y cancelaciones                  |
| **HTML/CSS** | Formulario web para solicitud de reservas                                 |
| **Google Calendar** | Crear evento en el calendario del usuario                          |
| **ngrok** | Túnel para vincular URIs de redireccionamiento con Google OAuth              |
| **nginx** | Servidor web para alojar el formulario y páginas del sistema                 |
| **Portainer.io** | Gestión y visualización de contenedores Docker en el servidor         |

---

## 🚀 Funcionalidades

### 📝 Solicitud de Reserva
- Formulario web conectado a webhook de n8n
- Captura de datos del solicitante y detalles de la reunión
- Almacenamiento automático en Google Sheets

### ✅ Validación Automática
- Verificación de disponibilidad en Google Sheets (base de datos)
- Validación de fechas y horarios (zona horaria Chile)
- Control de capacidad según número de participantes
- Sugerencia automática de alternativas si la sala está ocupada

### 📅 Integración con Google Calendar
- Creación automática de evento en el calendario del usuario al confirmar reserva
- El usuario puede ver la reunión reflejada en su Google Calendar
- Facilita la organización personal de las reuniones

### 📧 Sistema de Notificaciones
- **Confirmación de reserva** con detalles completos
- **Alternativas disponibles** cuando la sala solicitada está ocupada
- **Recordatorio automático** 1 hora antes de la reunión
- **Notificación de cancelación** al solicitante y encargado
- **Resumen diario** para el equipo administrativo

### ❌ Cancelación de Reservas
- Cancelación mediante enlace en correo de confirmación
- Liberación automática de la sala en la agenda
- Notificación al encargado de infraestructura

---

## 📊 Arquitectura del Sistema

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Formulario    │────▶│    Webhook      │────▶│   Validación   │
│      HTML       │     │      n8n        │     │  Disponibilidad │
└─────────────────┘     └─────────────────┘     └────────┬────────┘
                                                         │
                        ┌────────────────────────────────┼────────────────────────────────┐
                        │                                │                                │
                        ▼                                ▼                                ▼
              ┌─────────────────┐            ┌─────────────────┐            ┌─────────────────┐
              │  Sala Disponible│           │  Sala Ocupada   │             │ Datos Inválidos │
              └────────┬────────┘            └────────┬────────┘            └────────┬────────┘
                       │                              │                              │
                       ▼                              ▼                              ▼
              ┌─────────────────┐            ┌─────────────────┐            ┌─────────────────┐
              │   Confirmar     │            │    Enviar       │            │    Rechazar     │
              │    Reserva      │            │  Alternativas   │            │   Solicitud     │
              └────────┬────────┘            └─────────────────┘            └─────────────────┘
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
   ┌────────────┐ ┌────────────┐ ┌────────────┐
   │  Google    │ │   Gmail    │ │  Notificar │
   │  Sheets    │ │   (correo) │ │  Encargado │
   └────────────┘ └────────────┘ └────────────┘
```

---

## 📁 Estructura del Proyecto

```
practicaN-1/
│
├── 📄 README.md                          # Documentación del proyecto
├── 📄 Guia-Comandos-Git.md               # Guía de comandos Git
│
├── 📂 Formulario-html/                   # Formulario de reservas
│   └── index.html
│
├── 📂 Server/                            # Configuración del servidor y archivos alojados en este
│
├── 📂 Pagina-web/                        # Página web del sistema
│
├── 📂 Cancelacion-reserva/               # Lógica de cancelación
│
├── 📂 Confirmacion-reserva-encargado/    # Confirmación para encargados
│
├── 📂 Notificacion-de-cancelacion/       # Sistema de notificaciones
│
├── 📂 Resumen-de-reservas-al-encargado/  # Resúmenes automáticos
│
└── 📂 Sanitizacion-y-seguridad/          # Validación y seguridad
```

---

## 📝 Datos Capturados en el Formulario

### Datos Personales
| Campo |           Descripción                      |
|-------|--------------------------------------------|
| Nombre y Apellido | Identificación del solicitante |
| Correo electrónico|  Para recibir notificaciones   |
| Área/Departamento | Organización interna           |

### Datos de la Reunión
| Campo |              Descripción                     |
|-------|----------------------------------------------|
| Fecha de reunión | Día programado                    |
| Hora de inicio | Comienzo de la reunión              |
| Hora de término | Fin de la reunión                  |
| N° de participantes | Para validar capacidad de sala |

---

## ✔️ Reglas de Validación

El sistema verifica automáticamente:

- ✅ La fecha no sea anterior al día actual
- ✅ La hora de término sea posterior a la hora de inicio
- ✅ El número de participantes no supere la capacidad de la sala
- ✅ Todos los horarios correspondan a la zona horaria de Chile

---

## 📧 Tipos de Correos del Sistema

| Tipo | Destinatario | Descripción |
|------|--------------|-------------|
| 🟢 Confirmación | Solicitante | Detalles de reserva + enlace cancelación |
| 🟡 Alternativas | Solicitante | Opciones de salas/horarios disponibles |
| 🔴 Rechazo | Solicitante | Error en datos, solicitar reenvío |
| ⏰ Recordatorio | Solicitante | 1 hora antes de la reunión |
| ❌ Cancelación | Solicitante + Encargado | Confirmación de cancelación |
| 📊 Resumen | Equipo administrativo | Resumen diario/semanal de reservas |

---

## 👥 Personas y Roles

| Rol |                              Descripción                        |
|-----|-----------------------------------------------------------------|
| **Solicitantes** | Docentes, DGDE, administrativos que reservan salas |
| **Encargado de Infraestructura** | Personal que gestiona los espacios |

---

## 🛠️ Configuración

### Requisitos Previos
1. Cuenta de Google con acceso a Google Sheets y Gmail
2. Instancia de n8n configurada
3. APIs habilitadas:
   - Google Sheets API
   - Gmail API
   - Google Calendar API

### Variables de Entorno
```env
GOOGLE_SHEETS_ID=tu_id_de_spreadsheet
GMAIL_CREDENTIALS=credenciales_oauth
N8N_WEBHOOK_URL=url_del_webhook
```

---