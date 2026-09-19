# devsecops-lab
Laboratorio personal de DevSecOps. Pipeline de seguridad en GitHub Actions
sobre un repositorio de pruebas. Iniciado el 19/09/2026.

## Capas de análisis

| Capa | Herramienta | Qué detecta | Qué NO detecta |
|---|---|---|---|
| Secretos | gitleaks | Claves, tokens y contraseñas en el código **y en el historial de Git** | Secretos que nunca llegaron a commitearse; secretos con formato no reconocible |
| Dependencias (SCA) | Trivy | Vulnerabilidades conocidas (CVE) en dependencias y ficheros de infraestructura | Fallos en código propio; vulnerabilidades aún no publicadas |
| Código (SAST) | Semgrep | Patrones peligrosos en el código fuente: inyección, `shell=True`, acciones de CI sin fijar | Fallos de lógica de negocio; problemas que solo aparecen en ejecución |

Ninguna de las tres detecta fallos de **control de acceso** (IDOR y similares),
que siguen requiriendo revisión manual. Ninguna herramienta sustituye a leer el código.

## Hallazgos reales del primer día

Semgrep detectó que **este mismo pipeline** usaba referencias mutables
(`actions/checkout@v4`, `trivy-action@master`). Si la cuenta propietaria se
compromete, la etiqueta se repunta y el CI ejecuta código ajeno con los
secretos del repositorio delante. Corregido fijando cada acción a un SHA
de 40 caracteres.
