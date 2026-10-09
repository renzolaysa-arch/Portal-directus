# portal-directus

Configuración de Directus (API/CMS) para el [Portal de Registros Administrativos](https://github.com/renzolaysa-arch/Portal_RA) del INEI.

Este repositorio versiona solo la **configuración** necesaria para levantar el mismo Directus en cualquier máquina. Las credenciales reales, los archivos subidos por los editores y los respaldos de datos se copian aparte.

## Qué hay aquí

- `docker-compose.yml`: la definición del contenedor de Directus, con la versión fijada por digest (ver los comentarios del archivo).
- `directus.env.example`: plantilla de variables de entorno. Se copia a `directus.env` con los valores reales; `directus.env` nunca se sube a git.

## Qué no se versiona

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

Directus queda escuchando en `http://localhost:8055` (o en el `PUBLIC_URL` configurado).

El puerto se publica solo en `127.0.0.1`. Desde otra máquina se llega a Directus a través del proxy `/directus` del servidor web del portal, que además bloquea el panel `/admin` (ver `docs/DESPLIEGUE.md` en el repositorio del portal). Si el servidor web está en otra máquina, el puerto se publica en la IP interna y se limita con el cortafuegos a ese servidor.

## Restaurar datos y archivos

Estos tres elementos no están en git; se copian a mano desde el respaldo correspondiente:

1. **Base de datos**: se restaura el `.bak` de SQL Server más reciente.
2. **Archivos subidos**: el contenido de `uploads/` se copia junto a este `docker-compose.yml` antes de levantar el contenedor (está montado como volumen).
3. **Esquema de Directus** (colecciones, campos, permisos): en una instalación nueva sin base de datos previa, las colecciones se recrean a mano o desde un snapshot del esquema; este repositorio no incluye uno.

## Portal que consume esta API

El frontend vive en [renzolaysa-arch/Portal_RA](https://github.com/renzolaysa-arch/Portal_RA) y se conecta vía la variable `VITE_DIRECTUS_URL`.
