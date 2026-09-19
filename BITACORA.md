### Bitácora de Aprendizaje - DevSecOps Lab

### 2026-09-19 · 50 min

* **Hice:** Dockerfile de la API, build OK a la tercera.
* **Se rompió:** El COPY fallaba por el .dockerignore.
* **Aprendí:** Las capas se cachean de arriba abajo.

 ### 2026-09-19 · ~2 h

* **Hice:** Instalado OrbStack + trivy/gitleaks/semgrep. Levantado Juice Shop y crAPI (11 contenedores, todo healthy).
* **Se rompió:** crAPI tardó bastante en descargar. los nombres .orb.local no resuelven (DNS). Uso localhost:3000 y sigo.  
* **Aprendí:** He ejecutado 11 imágenes descargadas de internet sin saber qué hay dentro de ninguna. Eso es el problema de cadena de suministro. 
* **Pediente:** pasarles Trivy más adelante y ver qué sale y pendiente de mirar algún día, no bloquea nada (DNS).
