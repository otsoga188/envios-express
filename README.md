# 📦 Envios Express — Check Cashing App

Sistema de gestión de clientes para cambio de cheques, conectado a Google Sheets como base de datos y publicado en GitHub Pages.

---

## 🗂 Estructura del proyecto

```
envios-express/
├── index.html        ← La app completa (frontend)
├── Code.gs           ← El backend en Google Apps Script
└── README.md         ← Este archivo
```

---

## ✅ PASO 1 — Subir el Excel a Google Sheets

1. Ve a [sheets.google.com](https://sheets.google.com)
2. Clic en **"+"** para crear un Sheet nuevo
3. Nómbralo: `Envios Express - Check Cashing`
4. En el menú: **File → Import → Upload**
5. Sube tu archivo `CheckCashing2026.xlsx`
6. Selecciona:
   - Import location: **Replace current sheet**
   - Separator: **Detect automatically**
7. Clic **Import data**
8. Renombra la pestaña que se creó como **`Clientes`**
   - Clic derecho en la pestaña → Rename → `Clientes`

> ⚠️ La fila de headers debe ser la fila 1. Los headers exactos que necesita el sistema son:
> `id | fecha_registro | nombre | tel | dir | maker | vip | auto3k | cheque_tipo | id_tipo | id_fisico | id_exp | contrato | fecha_trabajo | antiguedad | patron | notas | foto | foto_id | foto_firma | estado`

---

## ✅ PASO 2 — Mapear las columnas del Excel al formato del sistema

Tu Excel tiene estos headers originales y deben mapearse así:

| Tu Excel | Nombre en el sistema |
|----------|---------------------|
| Customer | nombre |
| TEL | tel |
| Address | dir |
| MAKER | maker |
| VIP | vip |
| AUTORIZADO +3000 | auto3k |
| CHEQUE MANUAL O COMPUTALIZADO | cheque_tipo |
| IDENTIFICACION | id_tipo |
| ID FISICO | id_fisico |
| ID EXPIRACION | id_exp |
| FECHA FIRMADO CONTRATO | contrato |
| Fecha aproximada de entrada al trabajo | fecha_trabajo |
| ANTIGUEDAD | antiguedad |
| NOMBRE DEL PATRON/TEL | patron |
| Notas o Comentarios | notas |

> En Google Sheets puedes renombrar los headers directamente en la fila 1, o usar la función IMPORTRANGE / copiar-pegar con los nombres correctos.

---

## ✅ PASO 3 — Crear el Apps Script (backend API)

1. Con tu Google Sheet abierto, ve a **Extensions → Apps Script**
2. Borra todo el código que viene por default
3. Copia y pega todo el contenido de `Code.gs`
4. **Obtén el ID de tu Spreadsheet:**
   - Mira la URL de tu Sheet: `https://docs.google.com/spreadsheets/d/`**`1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs74OgVE2upms`**`/edit`
   - El ID es la parte en negrita entre `/d/` y `/edit`
5. En el `Code.gs`, reemplaza en la línea 7:
   ```javascript
   const SPREADSHEET_ID = 'TU_SPREADSHEET_ID_AQUI';
   ```
   por tu ID real, por ejemplo:
   ```javascript
   const SPREADSHEET_ID = '1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs74OgVE2upms';
   ```
6. Guarda con **Ctrl+S** (o el ícono de disco)
7. Nombra el proyecto: `EnviosExpressAPI`

---

## ✅ PASO 4 — Setup inicial de las hojas

1. En Apps Script, en el menú de funciones arriba, selecciona: **`setupInitialData`**
2. Clic en **▶ Run**
3. Te pedirá permisos → clic **Review permissions → Allow**
4. Esto crea automáticamente las hojas:
   - `Clientes` (si no existe)
   - `NSF_Bloqueados`
   - `Cheques`
   - `Empresas`

---

## ✅ PASO 5 — Publicar el Apps Script como API web

1. En Apps Script, clic en **Deploy → New deployment**
2. Clic en el ícono de engrane ⚙️ → **Web app**
3. Configura:
   - **Description:** `Envios Express API v1`
   - **Execute as:** `Me`
   - **Who has access:** `Anyone` ← Importante para que la app pueda conectarse
4. Clic **Deploy**
5. **Copia la URL** que aparece, se ve así:
   ```
   https://script.google.com/macros/s/AKfycbw.../exec
   ```
6. Guárdala, la necesitas en el siguiente paso

> ⚠️ Cada vez que modifiques el `Code.gs`, debes hacer **New deployment** (o **Manage deployments → Edit**) para que los cambios surtan efecto.

---

## ✅ PASO 6 — Conectar la app (index.html) al API

1. Abre `index.html` en tu editor de texto
2. Busca esta línea cerca del inicio del `<script>`:
   ```javascript
   const API_URL = 'https://script.google.com/macros/s/TU_DEPLOYMENT_ID_AQUI/exec';
   ```
3. Reemplaza con tu URL real:
   ```javascript
   const API_URL = 'https://script.google.com/macros/s/AKfycbw.../exec';
   ```
4. Guarda el archivo

---

## ✅ PASO 7 — Publicar en GitHub Pages

### Si no tienes repo todavía:

1. Ve a [github.com](https://github.com) → **New repository**
2. Nombre: `envios-express` (o el que prefieras)
3. Visibility: **Private** (recomendado — datos de clientes)
4. Clic **Create repository**

### Subir los archivos:

**Opción A — Desde la web de GitHub (más fácil):**
1. En tu repo, clic en **Add file → Upload files**
2. Arrastra `index.html` y `README.md`
3. Commit message: `Initial commit - Envios Express app`
4. Clic **Commit changes**

**Opción B — Con Git desde terminal:**
```bash
cd /ruta/a/tu/carpeta/envios-express
git init
git add index.html README.md
git commit -m "Initial commit - Envios Express app"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/envios-express.git
git push -u origin main
```

### Activar GitHub Pages:

1. En tu repo → **Settings → Pages** (en el menú izquierdo)
2. Source: **Deploy from a branch**
3. Branch: **main** → folder: **/ (root)**
4. Clic **Save**
5. Espera ~2 minutos
6. Tu app estará en: `https://TU_USUARIO.github.io/envios-express/`

---

## ✅ PASO 8 — Probar la conexión

1. Abre tu URL de GitHub Pages
2. El indicador en la esquina superior derecha debe mostrar **"Google Sheets ✓"** en verde
3. Busca un cliente por teléfono o nombre
4. Si aparece **"Sin conexión — modo local"** en rojo, revisa:
   - Que el `API_URL` en `index.html` sea correcto
   - Que el Apps Script esté publicado como "Anyone"
   - Que hayas guardado y subido el `index.html` actualizado a GitHub

---

## 🔒 Seguridad recomendada (opcional pero importante)

Como el sistema tiene datos sensibles de clientes, considera:

1. **Password básico en la app** — Puedes agregar un login simple con localStorage
2. **Repo privado** — Ya lo tienes si seguiste el paso 7
3. **Restricción por dominio en Apps Script** — En el `Code.gs` cambia:
   ```javascript
   const CORS_ORIGIN = 'https://TU_USUARIO.github.io';
   ```

---

## 🔄 Flujo de uso diario

```
Cliente llega con cheque
        ↓
Buscar por teléfono en la app
        ↓
¿Encontrado?
  ├── SÍ → Ver banner de estado
  │         ├── Verde ✔ → Proceder
  │         ├── Amarillo ⚠ → ID por vencer, verificar
  │         └── Rojo ⛔ → NSF o ID vencida, NO CAMBIAR
  │
  └── NO → ¿Empresa conocida?
              ├── SÍ + empresa OK → Riesgo medio, registrar cliente nuevo
              └── NO → Riesgo alto, solicitar más info antes de proceder
        ↓
Monto > $3,000 → Firmar contrato antes de procesar
        ↓
Registrar cheque en el sistema (+ Cheque)
```

---

## 🛠 Actualizaciones futuras

Para cambios en la lógica del backend (`Code.gs`):
1. Edita en Apps Script
2. **Deploy → Manage deployments → Edit → Version: New version → Deploy**

Para cambios en la app (`index.html`):
1. Edita el archivo
2. Sube a GitHub (reemplaza el anterior)
3. GitHub Pages se actualiza automáticamente en ~1 minuto

---

## 📞 Soporte

Sistema desarrollado para **Envios Express** — Detroit, MI  
Stack: Google Apps Script + Google Sheets + GitHub Pages  
Sin costos de servidor — 100% gratuito
