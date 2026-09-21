### Bitácora de Aprendizaje - DevSecOps Lab

### 2026-09-19 · ~4 h

-  **Hice:** Instalado OrbStack + trivy/gitleaks/semgrep en el Mac. Levantado
  Juice Shop y crAPI (11 contenedores, todos healthy). Montado el pipeline
  de seguridad en GitHub Actions con 3 capas y conseguido el verde.
-  **Se rompió:**
  - (mañana, OrbStack) Los nombres .orb.local no resuelven por DNS.
    Uso localhost:3000 y sigo.
  - (tarde, GitHub Actions) Pipeline en rojo por una errata mía (`--erro`).
    El propio log decía la solución.
  - (tarde, GitHub Actions) Pipeline en rojo otra vez, este con hallazgo
    real de Semgrep. Los contenedores no se tocaron por la tarde.
-  **Aprendí:** Semgrep encontró 4 fallos **en mi propio pipeline**: las acciones
  estaban fijadas a etiquetas mutables (@v4, @v2, @master). Si comprometen esa
  cuenta, mi CI ejecuta código ajeno con mis secretos delante. Arreglado fijando
  cada acción a un SHA de 40 caracteres. Es la misma lección que los 11
  contenedores que descargué esta mañana sin saber qué había dentro:
  **cadena de suministro**.
  Distinguir "la herramienta ha fallado" de "la herramienta ha encontrado algo"
  es la habilidad clave. Los dos son rojos y no significan lo mismo.
-  **Pendiente:** Pasar Trivy a las imágenes de crAPI y ver qué sale.
  Mirar lo del DNS de .orb.local algún día (no bloquea nada).
  En /rest/user/whoami el cliente decide qué campos pide con `?fields=`.
  ¿Quién valida eso, el cliente o el servidor?

### 2026-09-21 · ~50 min

-  **Hice:** Ver el primer apartado "Fundamentos de Docker", del curso "Curso de Docker: Fundamentos", impartidos por Platzi. 
-  **Se rompió:** Nada.
-  **Aprendí:** Los comandos mas comunes que se emplean en Docker -> "docker --version", docker images, docker run,  docker info, docker ps" y prestar especial atención al "--help" por todas     las ayudas que nos brinda dentro de los comandos anteriormente citados,  
-  **Pendiente:** Docker info, dispone de un apartado de Security Options
