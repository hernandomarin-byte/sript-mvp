# Conectar Cuenta de NotebookLM

Skill para autenticar y gestionar cuentas de Google NotebookLM con Claude Code via MCP.

## Uso

```
/connect-notebooklm [nombre-cuenta]
```

- Sin argumento: conecta la cuenta por defecto
- Con argumento: conecta una cuenta con alias (ej: `work`, `personal`, `cliente`)

---

## Instrucciones para Claude

Cuando el usuario ejecute este comando, sigue estos pasos:

### 1. Verificar el MCP server

Comprueba que el servidor notebooklm-mcp está disponible:

```bash
/opt/node22/bin/notebooklm-mcp --version 2>/dev/null || echo "MCP no encontrado"
```

Si no está instalado:
```bash
npm install -g notebooklm-mcp@latest
```

### 2. Autenticar cuenta

**Cuenta por defecto:**
```bash
BROWSER_CHANNEL=chromium PLAYWRIGHT_BROWSERS_PATH=/opt/pw-browsers HEADLESS=false /opt/node22/bin/notebooklm-mcp
```

**Cuenta con alias (reemplaza NOMBRE por el argumento del usuario):**
```bash
BROWSER_CHANNEL=chromium PLAYWRIGHT_BROWSERS_PATH=/opt/pw-browsers HEADLESS=false NOTEBOOKLM_ACCOUNT=NOMBRE /opt/node22/bin/notebooklm-mcp
```

En la ventana del navegador que se abre:
1. Inicia sesión con la cuenta de Google deseada
2. Autoriza el acceso a NotebookLM
3. Cierra la ventana cuando se complete la autenticación

La sesión queda guardada en:
- `~/.local/share/notebooklm-mcp/chrome_profile/` (cuenta por defecto)
- `~/.local/share/notebooklm-mcp/chrome_profile_NOMBRE/` (cuenta con alias)

### 3. Verificar conexión

Tras la autenticación, confirma que funciona:

```bash
BROWSER_CHANNEL=chromium PLAYWRIGHT_BROWSERS_PATH=/opt/pw-browsers HEADLESS=true /opt/node22/bin/notebooklm-mcp
```

Deberías ver:
```
✅ NotebookLM MCP Server initialized
✅ Loaded library with N notebooks
```

### 4. Cambiar entre cuentas

Para usar una cuenta específica en una sesión, establece la variable de entorno en `.claude/settings.json`:

```json
{
  "mcpServers": {
    "notebooklm": {
      "command": "/opt/node22/bin/notebooklm-mcp",
      "env": {
        "NOTEBOOKLM_ACCOUNT": "NOMBRE",
        "BROWSER_CHANNEL": "chromium",
        "PLAYWRIGHT_BROWSERS_PATH": "/opt/pw-browsers",
        "HEADLESS": "true",
        "NOTEBOOKLM_PROFILE": "full"
      }
    }
  }
}
```

### 5. Configuración multi-cuenta (múltiples MCP servers)

Para tener varias cuentas disponibles simultáneamente, añade entradas separadas en `.claude/settings.json`:

```json
{
  "mcpServers": {
    "notebooklm-work": {
      "command": "/opt/node22/bin/notebooklm-mcp",
      "args": ["--account", "work"],
      "env": {
        "BROWSER_CHANNEL": "chromium",
        "PLAYWRIGHT_BROWSERS_PATH": "/opt/pw-browsers",
        "HEADLESS": "true",
        "NOTEBOOKLM_PROFILE": "full"
      }
    },
    "notebooklm-personal": {
      "command": "/opt/node22/bin/notebooklm-mcp",
      "args": ["--account", "personal"],
      "env": {
        "BROWSER_CHANNEL": "chromium",
        "PLAYWRIGHT_BROWSERS_PATH": "/opt/pw-browsers",
        "HEADLESS": "true",
        "NOTEBOOKLM_PROFILE": "full"
      }
    }
  }
}
```

---

## Herramientas disponibles tras conectar

| Herramienta | Descripción |
|---|---|
| `ask_question` | Consultar libretas con respuestas citadas de Gemini |
| `list_notebooks` | Listar libretas guardadas |
| `select_notebook` | Establecer libreta activa |
| `add_source` | Agregar URL o texto como fuente |
| `add_notebook` | Agregar libreta compartida por URL |
| `generate_audio` | Generar resumen de audio (podcast) |
| `setup_auth` | Iniciar autenticación interactiva |
| `re_auth` | Limpiar y reintentar autenticación |
| `cleanup_data` | Eliminar todos los datos almacenados |

## Requisitos del sistema

- Node.js ≥ 18 (instalado: `/opt/node22/bin/node`)
- Chromium (disponible en `/opt/pw-browsers`)
- Paquete `notebooklm-mcp` (instalado en `/opt/node22/bin/notebooklm-mcp`)
