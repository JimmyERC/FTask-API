# FTask API 🍅

API REST desarrollada con **Spring Boot** para gestionar proyectos y tareas con la **técnica Pomodoro** integrada. Permite personalizar los tiempos de concentración y descanso, iniciar sesión con contraseña u OTP enviado al correo, y consultar métricas de productividad con una estimación de la hora de finalización.

> 🚧 Proyecto en desarrollo con fines de práctica y portafolio. El frontend se desarrollará en Angular en un repositorio aparte.

---

## 📋 Funcionalidades

### Autenticación y cuenta
- Registro de usuario.
- Verificación de correo electrónico.
- Inicio de sesión con **contraseña** o con **código OTP** enviado al correo.
- Edición de perfil: nombre de usuario y foto de perfil.
- Cambio de contraseña.

### Proyectos
- CRUD de proyectos.
- Estados: `PENDING`, `IN_PROGRESS`, `COMPLETED`.
- El proyecto pasa a `IN_PROGRESS` automáticamente cuando una de sus tareas pasa a `IN_PROGRESS`.
- Un proyecto completado puede reabrirse.
- El proyecto se marca como `COMPLETED` manualmente.
- Eliminación lógica (`deleted_at`): al eliminar un proyecto también se ocultan sus tareas, pero sus sesiones Pomodoro se conservan para las métricas históricas (la confirmación la gestiona el frontend).

### Tareas
- CRUD de tareas asociadas a un proyecto.
- Pomodoros estimados y pomodoros completados por tarea (los completados pueden superar a los estimados).
- Estados: `PENDING`, `IN_PROGRESS`, `COMPLETED`.
- La tarea pasa a `IN_PROGRESS` al iniciar su primer Pomodoro.
- Se puede completar manualmente en cualquier momento, aunque no haya alcanzado su meta de Pomodoros.
- Opcional: completar automáticamente al alcanzar la meta de Pomodoros (configurable por usuario).
- Una tarea completada puede reabrirse: vuelve a `IN_PROGRESS` si tiene Pomodoros registrados (lo que también reabre su proyecto) o a `PENDING` si no tiene.
- Eliminación lógica (`deleted_at`).

### Pomodoro
- Registro de sesiones Pomodoro de concentración con estado `COMPLETED` o `INTERRUPTED`.
- Los descansos no se registran como sesiones; solo se usan para calcular la estimación de finalización.
- Las sesiones interrumpidas conservan su duración real para las métricas de tiempo.
- Configuración personalizable por usuario:

| Parámetro | Valor por defecto |
|---|---|
| Tiempo de concentración | 25 min |
| Descanso corto | 5 min |
| Descanso largo | 15 min |
| Pomodoros antes del descanso largo | 4 |
| Completar tareas automáticamente al alcanzar la meta | Desactivado |

### Métricas
- Filtrables por período: **hoy**, **esta semana** (de lunes a domingo), **este mes** y **este año**, calculados según la zona horaria del usuario.
- Pomodoros completados.
- Tiempo total de concentración (incluye Pomodoros interrumpidos).
- Tareas completadas.
- Proyectos completados y en proceso.
- **Estimación de finalización** (fecha y hora) de las tareas pendientes, incluyendo pomodoros y descansos.

#### Cálculo de la estimación

```
n            = Σ max(0, estimatedPomodoros - completedPomodoros) of unfinished tasks
breaks       = max(0, n - 1)
longBreaks   = floor(breaks / longBreakInterval)
shortBreaks  = breaks - longBreaks

totalMinutes = n * focusMinutes + shortBreaks * shortBreakMinutes + longBreaks * longBreakMinutes
finishAt     = now + totalMinutes
```

---

## 🗂️ Modelo de dominio

| Entidad | Atributos principales |
|---|---|
| **User** | email, username, password, profilePicture, emailVerified, createdAt, updatedAt |
| **Project** | name, description, status, user, createdAt, updatedAt, deletedAt |
| **Task** | name, description, estimatedPomodoros, completedPomodoros, status, project, createdAt, updatedAt, deletedAt |

> Los proyectos y tareas eliminados se ocultan de forma permanente (no hay papelera ni restauración).
| **PomodoroSession** | task, startedAt, endedAt, status (`COMPLETED`, `INTERRUPTED`) |
| **PomodoroSettings** | focusMinutes, shortBreakMinutes, longBreakMinutes, longBreakInterval, autoCompleteTasks, user |
| **OtpCode** | user, code, purpose (`EMAIL_VERIFICATION`, `LOGIN`), expiresAt, used |

---

## 🏛️ Arquitectura

El proyecto sigue **Clean Architecture**, organizado por funcionalidad:

```
com.jimmyerc.ftask
├── task
│   ├── domain          // Domain models and repository ports
│   ├── application     // Use cases
│   ├── infrastructure  // JPA entities, adapters, mappers
│   └── presentation    // REST controllers and DTOs
├── project
├── pomodoro
├── user
└── shared              // Security (JWT), exception handling, config
```

---

## 🛠️ Tecnologías

- Java 25
- Spring Boot 4.1
- Spring Web
- Spring Data JPA (Hibernate)
- Spring Security + JWT
- Spring Mail
- Bean Validation
- PostgreSQL (Supabase)
- Flyway
- Maven

---

## 🚀 Instalación

> Instrucciones pendientes; se completarán cuando el proyecto sea ejecutable.

```bash
git clone https://github.com/JimmyERC/FTask-API.git
cd FTask-API
./mvnw spring-boot:run
```

---

## 🗺️ Roadmap

- [ ] Autenticación (registro, verificación de correo, login con contraseña/OTP)
- [ ] Perfil de usuario
- [ ] CRUD de proyectos
- [ ] CRUD de tareas
- [ ] Sesiones y configuración Pomodoro
- [ ] Métricas y estimación de finalización
- [ ] Documentación de la API (OpenAPI / Swagger)
- [ ] Frontend en Angular (repositorio aparte)

---

## 📄 Licencia

Este proyecto está bajo la licencia [MIT](LICENSE).

## 👤 Autor

**Jimmy Ernesto Ramos Castaneda** — [GitHub](https://github.com/JimmyERC)
