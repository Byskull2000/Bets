# Documento de Requisitos Técnicos (PRD): Plataforma de Apuestas Mundial - Uso Interno

## 1. Visión General
Necesito desarrollar una aplicación web para gestionar las apuestas internas de mi empresa durante el Mundial. La plataforma debe ser **100% Mobile-First y ultra-responsiva**, diseñada específicamente para que los empleados la usen desde sus teléfonos celulares. La estética debe ser similar a los sitios de apuestas deportivas (tema oscuro, banderas de países, diseño emocionante y "mundialero").

## 2. Stack Tecnológico
* **Frontend:** Next.js (App Router), React, TailwindCSS (para diseño fluido, responsivo y adaptado a móviles).
* **Backend:** Go (Golang) usando un framework ligero como Gin, Fiber o Chi.
* **Base de Datos:** PostgreSQL.
* **Comunicación:** API REST.

## 3. Reglas de Negocio y Lógica de Apuestas
* **Dinámica de Apuesta:** Se apuesta por **cada partido individual**, no hay un pozo global para el torneo.
* **Monto y Pozo:** El Administrador define cuánto cuesta la entrada/apuesta para cada partido. El sistema debe sumar todas las entradas pagadas para calcular el "Pozo Total" (Total Pot) de ese partido.
* **Condición de Victoria:** Solo ganan quienes acierten el **resultado exacto** (ej. si apuestan 2-1, el partido debe terminar 2-1). No hay puntos parciales por acertar al ganador sin el resultado exacto.
* **Distribución del Premio:** Si una persona acierta, se lleva todo el pozo. Si varias personas aciertan el resultado exacto, el pozo total del partido se divide en partes iguales entre los ganadores. *(Nota para el desarrollador: deja preparada la lógica por si nadie acierta, ej. "Pozo desierto / Devolución").*
* **Visibilidad:** Todas las apuestas de todos los usuarios son públicas. El frontend debe mostrar quién apostó a qué en cada partido.
* **Gestión de Pagos:** Los pagos reales ocurren fuera de la plataforma, pero el sistema debe tener un registro visual (un toggle/checkbox controlado por el admin) para confirmar quién ya pagó su entrada al partido para inflar el pozo.

## 4. Roles y Autenticación
* **Autenticación:** Sistema simple de Usuario y Contraseña (sin correos, sin OAuth). JWT para manejo de sesiones.
* **Rol Usuario:** Puede ver los partidos, hacer sus predicciones (hasta antes de que empiece el partido), ver el pozo, ver las apuestas de sus compañeros y ver el Leaderboard.
* **Rol Admin:** * Crea/edita los partidos.
    * Fija el costo de la apuesta por partido.
    * Marca quién ha pagado la apuesta.
    * Carga el resultado final real del partido para ejecutar el cálculo de ganadores.

## 5. Requisitos de la Interfaz y UX (Frontend - Next.js)
* **Diseño Responsivo Obligatorio:** Todos los componentes deben estar optimizados para pantallas táctiles y móviles. Usa layouts verticales inteligentes, menús hamburguesa o barras de navegación inferiores estilo app nativa.
* **Home/Dashboard:** Lista de partidos próximos en formato de "Cards" verticales para móviles, con sus banderas, fecha/hora, costo de entrada y pozo actual acumulado.
* **Detalle del Partido:** Vista móvil optimizada donde se ve:
    * El input táctil para poner el resultado rápidamente (ej. botones `+` y `-` para los goles, o inputs numéricos cómodos).
    * Lista desplegable o scrolleable de usuarios que ya entraron a este partido y sus predicciones.
    * El pozo total destacado en la parte superior.
* **Leaderboard:** Una tabla de posiciones global que se adapte a pantallas angostas (ocultando columnas secundarias en mobile si es necesario) que muestre quién ha ganado más dinero acumulado o quién ha tenido más aciertos exactos.
* **Estilo Visual:** Estética "Mundialera", tipo casa de apuestas deportivas. Uso de tarjetas (cards), tipografía clara y colores vibrantes sobre fondos oscuros (Dark Mode).

## 6. Modelo de Datos Sugerido (PostgreSQL)
Por favor, genera los scripts de SQL o la estructura de migración/GORM (si usas ORM en Go) basándote en esta idea:

* `users`: id, username, password_hash, role (admin/user).
* `matches`: id, team_a, team_b, match_datetime, status (pending, in_progress, finished), score_a, score_b, entry_fee.
* `bets`: id, user_id, match_id, predicted_score_a, predicted_score_b, is_paid (boolean).

## 7. Tareas para el LLM (Claude)
1.  **Backend (Go):** Escribe el código del servidor, incluyendo la conexión a PostgreSQL, el modelo de datos, la autenticación JWT y los controladores para:
    * Registro/Login.
    * CRUD de partidos (Admin).
    * Endpoint para colocar apuesta (Usuario).
    * Endpoint de "Liquidación" (Admin setea el resultado final y el backend calcula los ganadores y reparte el pozo virtualmente).
2.  **Frontend (Next.js):** Genera la estructura de páginas y componentes de React con Tailwind (utilizando clases responsivas como `md:`, `lg:` pero priorizando la vista base mobile) para:
    * Layout principal con Navbar adaptada a móviles.
    * Card de Partido optimizada.
    * Vista de Detalle de Partido (donde se ven las apuestas de todos).
    * Tabla de Posiciones (Leaderboard) responsiva.
3.  **Seed Data:** Proporciona un script o función en Go para poblar la base de datos con al menos los partidos de la fase de grupos de un Mundial para no empezar con la BD vacía.

Por favor, comienza entregando la arquitectura de carpetas para ambos proyectos y el código base del servidor en Go.