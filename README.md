# launcher-content

Contenido remoto del **AurusTeam Launcher**. Este repo es la "fuente de verdad":
el launcher lee `manifest.json` y muestra lo que aqui publiques (banners, novedades,
juegos y versiones). Editas este repo → el launcher se actualiza solo (con internet).

## Estructura

```
launcher-content/
├── manifest.json                 # <- el launcher apunta a la URL RAW de este archivo
├── README.md
├── .github/workflows/validate.yml  # valida el manifest en cada push (opcional)
└── assets/
    ├── banners/                  # imagenes del carrusel y de cada juego
    │   ├── tsot-promo.png
    │   ├── entorno.png
    │   └── linea-mala.png
    └── logos/                    # iconos de la barra izquierda
        ├── tsot.png
        └── entorno.png
```

> Los **.zip de los juegos NO van en el repo** (pesan demasiado). Van en **GitHub Releases**.
> Las imagenes ligeras (banners/logos) si pueden vivir aqui.

## Como conectarlo al launcher

1. Crea un repo en GitHub llamado `launcher-content` (publico) y sube estos archivos.
2. Reemplaza **`TU-USUARIO`** por tu usuario de GitHub en TODO `manifest.json`
   (find & replace). Son las URLs de banners, logos y `downloadUrl`.
3. Copia la URL **RAW** del manifest:
   ```
   https://raw.githubusercontent.com/TU-USUARIO/launcher-content/main/manifest.json
   ```
4. En el launcher → **Configuracion** → pega esa URL → **Guardar y recargar**.
   Pasara a "En linea" y mostrara tu contenido.

## Patron de URLs (asi se conecta cada cosa)

| En el manifest        | De donde sale el archivo                                                                 |
|-----------------------|------------------------------------------------------------------------------------------|
| `launcher.banners[]`  | `raw.githubusercontent.com/TU-USUARIO/launcher-content/main/assets/banners/<archivo>.png`|
| `games[].logo`        | `raw.githubusercontent.com/TU-USUARIO/launcher-content/main/assets/logos/<archivo>.png`  |
| `games[].banner`      | igual que los banners                                                                     |
| `games[].downloadUrl` | un **Release**: `github.com/TU-USUARIO/launcher-content/releases/download/<tag>/<zip>`    |

## Publicar el .zip de un juego (GitHub Releases)

1. Empaqueta tu juego en un `.zip` (con el `.exe` o `index.html` dentro).
2. En el repo → pestana **Releases** → **Draft a new release**.
3. **Tag** = algo unico por version, ej. `tsot-v1.3.0`.
4. Arrastra el `.zip` a "Attach binaries" y publica.
5. Click derecho en el archivo subido → copiar enlace → ese es tu `downloadUrl`.
   Queda asi: `.../releases/download/tsot-v1.3.0/tsot-redux-1.3.0.zip`

Limite: hasta **2 GB por archivo**, gratis. Si tu juego pesa mas, usa tu servidor
o Cloudflare R2 y solo pones esa URL en `downloadUrl` (el launcher acepta cualquier URL).

## Tareas comunes

- **Agregar un juego** → nuevo objeto en `games[]` con `id` unico, su release en `downloadUrl`,
  y `executable` (para juegos web Three.js/HTML usa `"index.html"`).
- **Cambiar banners del carrusel** → edita `launcher.banners` (sube los PNG a `assets/banners/`).
- **Publicar una novedad** → agrega entrada a `launcher.news`.
- **Lanzar una actualizacion** → sube el nuevo `.zip` como Release, cambia `downloadUrl`
  y **sube el numero de `version`** (ej. `1.3.0` → `1.4.0`). El launcher mostrara ACTUALIZAR.

## Notas

- `raw.githubusercontent.com` cachea ~5 min: tras editar, el cambio tarda un poco en verse.
- Manten los `id` de cada juego **estables** (no los cambies): con ellos el launcher sabe
  que version tiene instalada cada usuario.
- El Action de `.github/workflows` valida el JSON en cada push y te avisa si algo esta mal
  (campo faltante, version mal escrita, id duplicado).
