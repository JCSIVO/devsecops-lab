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

## Endurecimiento de la imagen

| Métrica | Inicial (SDK) | Runtime Ubuntu | Runtime Alpine |
|---|---|---|---|
| Tamaño | 1,36 GB | 369 MB | 186 MB (−86%) |
| Vulnerabilidades (Trivy) | 40 | 13 | 0 |
| Usuario de ejecución | root | `app` | `app` |

Ninguna vulnerabilidad se parcheó: se eliminó lo que sobraba. Las 27 de
severidad media del primer salto pertenecían al toolchain de compilación,
que no tiene razón para viajar a producción. Las 13 restantes eran del
sistema base y desaparecen al cambiar a Alpine.

Advertencias sobre el resultado:
- Cero hallazgos no significa seguro. Trivy analiza paquetes y dependencias,
  no el código, la configuración ni el control de acceso. La aplicación es
  todavía una plantilla mínima.
- Alpine usa musl en lugar de glibc. Es una decisión con contrapartidas,
  no una mejora sin coste.
