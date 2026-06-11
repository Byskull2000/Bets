# Documento de Requisitos Técnicos (PRD): Plataforma de Apuestas Mundial - Uso Interno

## 1. Visión General
Necesito desarrollar una aplicación web para gestionar las apuestas internas de mi empresa durante el Mundial. La plataforma debe ser **100% Mobile-First y ultra-responsiva**, diseñada para que los empleados consulten la información desde sus teléfonos. La estética debe ser similar a los sitios de apuestas deportivas (tema oscuro, banderas de países, diseño emocionante y "mundialero").

## 2. Stack Tecnológico e Infraestructura
* **Frontend:** Next.js (App Router), React, TailwindCSS.
* **Backend:** Go (Golang) estructurado para **AWS Lambda** (Runtime: `provided.al2023`).
* **Framework Web:** Gin o Fiber (usando `aws-lambda-go-api-proxy` para adaptar el enrutador HTTP a Lambda).
* **Base de Datos:** PostgreSQL.
* **Comunicación:** API REST.

## 3. Reglas de Negocio y Lógica de Apuestas
* **Dinámica de Apuesta:** Se apuesta por **cada partido individual**.
* **Monto y Pozo:** El Administrador define el costo de la apuesta por partido. El sistema calcula el "Pozo Total" multiplicando las entradas válidas.
* **Ingreso de Datos Centralizado:** **Solo el Administrador tiene permisos para registrar información.** Los usuarios comunes tienen cuentas de "Solo Lectura" para ver la información de forma transparente.
* **Condición de Victoria:** Solo ganan quienes acierten el **resultado exacto**. Si hay un empate en aciertos, el pozo se divide equitativamente entre los ganadores. Si nadie acierta, se debe dejar la lógica lista para manejar el pozo como "Desierto / Devolución".
* **Visibilidad:** Todas las apuestas registradas son públicas para todos los empleados dentro de la plataforma.

## 4. Roles y Autenticación
* **Autenticación:** Sistema simple de Usuario y Contraseña (sin correos, sin OAuth). Sesiones mediante JWT.
* **Rol Usuario (Lectura):** Puede ver el fixture, el costo por partido, el pozo acumulado, las apuestas de sus compañeros (registradas por el admin) y el Leaderboard global. No puede crear ni modificar apuestas.
* **Rol Admin (Escritura total):**
    * Crea y edita los partidos (equipos, fecha, hora).
    * Fija el costo de la entrada por partido.
    * **Registra las predicciones (apuestas) de cada empleado en el sistema.**
    * Marca si el empleado ya pagó la apuesta (para activar su participación).
    * Introduce el resultado real final del partido para liquidar el pozo.

## 5. Requisitos de la Interfaz y UX (Frontend - Next.js)
* **Diseño Responsivo Obligatorio:** Enfoque Mobile-First para smartphones.
* **Home/Dashboard:** Lista de partidos en formato de "Cards" verticales. Muestra banderas, fecha, pozo acumulado y costo de entrada.
* **Detalle del Partido (Vista Usuario):** Muestra el pozo destacado y una lista con los nombres de sus compañeros y la predicción exacta que el admin les registró.
* **Detalle del Partido (Vista Admin):** Formulario o panel con un listado de todos los usuarios del sistema donde el Admin puede ingresar los goles de la predicción de cada uno y un checkbox para marcar el pago.
* **Leaderboard:** Tabla de posiciones global adaptada a móviles (scrolleable u ocultando datos secundarios) que muestra el dinero total acumulado ganado por cada persona.
* **Estilo Visual:** Dark Mode agresivo, tipografías estilo deportivo, colores vibrantes (verde/dorado/azul mundialista).

## 6. Modelo de Datos Sugerido (PostgreSQL)
* `users`: id, username, password_hash, role (admin/user).
* `matches`: id, team_a, team_b, match_datetime, status (pending, in_progress, finished), score_a, score_b, entry_fee.
* `bets`: id, user_id, match_id, predicted_score_a, predicted_score_b, is_paid (boolean).

## 7. Tareas para el LLM (Claude)
1.  **Backend (Go para AWS Lambda):** Escribe el código del servidor preparado para Lambda.
    * **IMPORTANTE:** No uses el `r.Run()` tradicional. Implementa el Handler de AWS Lambda envolviendo el router (Gin/Fiber) con `aws-lambda-go-api-proxy`.
    * Modelos de datos con GORM o SQL puro para PostgreSQL.
    * Endpoints de autenticación JWT.
    * Endpoints administrativos para el CRUD de partidos, registro masivo/individual de apuestas de los usuarios, y cierre de partido (cálculo de ganadores).
2.  **Frontend (Next.js):** Genera la estructura de páginas y componentes responsivos con TailwindCSS.
    * Vistas diferenciadas por rol (especialmente el detalle de partido para el admin, optimizado para meter las apuestas rápido).
    * Componente de tabla para el Leaderboard.
3.  **Seed Data:** Genera un archivo o script en Go para poblar la base de datos con usuarios de prueba y los primeros partidos de la fase de grupos del Mundial.

Por favor, comienza entregando la arquitectura de carpetas para ambos proyectos y la estructura del `main.go` adaptada para AWS Lambda junto con los modelos de base de datos.