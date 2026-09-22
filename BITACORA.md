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

-  **Hice:** Primer apartado "Fundamentos de Docker" del curso de Platzi. 
-  **Se rompió:** Nada.
-  **Aprendí:** Los comandos básicos de Docker (`--version`, `images`, `run`,
  `info`, `ps`) y sobre todo el reflejo del `--help`, que sirve igual para
  trivy, semgrep, terraform y kubectl.  
-  **Pendiente:** `docker info` tiene un apartado de Security Options.

### 2026-09-22 · ~50 min

-  **Hice:** Descargar el dotnet en brew, crear la API, y el archivo "Dockerfile" 
-  **Se rompió:** El build falló (NETSDK1045): mi SDK local es .NET 10 y el
           contenedor traía el SDK 8. Deriva de versiones entre mi máquina
           y el entorno de build. Arreglado alineando la imagen base.
-  **Aprendí:** Los comandos básicos de Docker para compilar la imagen(`docker build .`, `images`, `docker build -t nombreImagen:latest .`)  y para eliminar la imagen `docker rmi -f` nombre de la imagen   
-  **Pendiente:** Nada.  
