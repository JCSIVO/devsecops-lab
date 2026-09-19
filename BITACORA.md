### Bitácora de Aprendizaje - DevSecOps Lab

### 2026-09-19 · ~4 h

- * **Hice:** Instalado OrbStack + trivy/gitleaks/semgrep en el Mac. Levantado
  Juice Shop y crAPI (11 contenedores, todos healthy). Montado el pipeline
  de seguridad en GitHub Actions con 3 capas y conseguido el verde.
- * **Se rompió:** Los nombres .orb.local no resuelven (DNS) → uso localhost:3000.
  Pipeline en rojo por una errata mía (`--erro`); el propio log decía la solución.
  Luego rojo otra vez, pero esta con hallazgo real.
- * **Aprendí:** Semgrep encontró 4 fallos **en mi propio pipeline**: las acciones
  estaban fijadas a etiquetas mutables (@v4, @v2, @master). Si comprometen esa
  cuenta, mi CI ejecuta código ajeno con mis secretos delante. Arreglado fijando
  cada acción a un SHA de 40 caracteres. Es la misma lección que los 11
  contenedores que descargué esta mañana sin saber qué había dentro:
  **cadena de suministro**.
  Distinguir "la herramienta ha fallado" de "la herramienta ha encontrado algo"
  es la habilidad clave. Los dos son rojos y no significan lo mismo.
- * **Pendiente:** Pasar Trivy a las imágenes de crAPI y ver qué sale.
  Mirar lo del DNS de .orb.local algún día (no bloquea nada).
  En /rest/user/whoami el cliente decide qué campos pide con `?fields=`.
  ¿Quién valida eso, el cliente o el servidor?
