# portal-directus

Configuración de Directus (API/CMS) para el [Portal de Registros Administrativos](https://github.com/renzolaysa/Portal_RA) del INEI.

Este repositorio versiona solo la **configuración**: la receta para levantar el mismo Directus en cualquier máquina. No versiona las credenciales reales, los archivos subidos por los editores ni los respaldos de datos — eso viaja aparte, por copia directa.

## Qué hay aquí

- `docker-compose.yml`: la definición del contenedor de Directus, con la versión fijada por digest (ver los comentarios del archivo).
- `directus.env.example`: plantilla de variables de entorno. Cópiala a `directus.env` y completa los valores reales; `directus.env` nunca se sube a git.

## Qué NO hay aquí (y por qué)

| Excluido | Motivo |
|---|---|
| `directus.env` | Credenciales reales: clave de cifrado, contraseña de la base de datos. |
| `uploads/` | Imágenes, PDF y Excel subidos desde el panel. Pesa cientos de MB y no es código. |
| `backup*`, `respaldo*` | Respaldos y exportaciones puntuales de datos (tablas, indicadores, el `.bak` de SQL Server). |

## Levantarlo en una máquina nueva

Requisitos: Docker, y una base de datos SQL Server accesible desde el contenedor (local o remota).

```bash
cp directus.env.example directus.env
# completa directus.env: KEY, SECRET, datos de conexion a la base, etc.

docker compose up -d
```

Directus queda escuchando en `http://localhost:8055` (o el `PUBLIC_URL` que hayas puesto).

## Restaurar datos y archivos

Estos tres elementos no están en git; se copian a mano desde el respaldo correspondiente:

1. **Base de datos**: restaura el `.bak` de SQL Server más reciente en el motor que uses.
2. **Archivos subidos**: copia el contenido de `uploads/` al lado de este `docker-compose.yml` antes de levantar el contenedor (está montado como volumen).
3. **Esquema de Directus** (colecciones, campos, permisos): si es una instalación nueva sin base de datos previa, hay que recrear las colecciones a mano o desde un snapshot del esquema; este repositorio no incluye uno todavía.

## Portal que consume esta API

El frontend vive en [renzolaysa/Portal_RA](https://github.com/renzolaysa/Portal_RA) y se conecta vía la variable `VITE_DIRECTUS_URL`.
