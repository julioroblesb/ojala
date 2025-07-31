# 🚀 GUÍA COMPLETA: Desplegar CRM en Vercel

## 📋 PREPARACIÓN PREVIA

### Requisitos:
- Cuenta en [GitHub](https://github.com)
- Cuenta en [Vercel](https://vercel.com)
- El archivo ZIP del proyecto CRM

---

## 🗂️ PASO 1: SUBIR A GITHUB

### 1.1 Crear repositorio en GitHub
1. Ve a [github.com](https://github.com)
2. Haz clic en el botón verde "New" o "+"
3. Selecciona "New repository"
4. Nombre del repositorio: **"crm-lead-management"**
5. Descripción: **"CRM para gestión de leads con integración a Google Sheets"**
6. Selecciona "Public" (o "Private" si prefieres)
7. **NO** marques "Add a README file"
8. Haz clic en "Create repository"

### 1.2 Subir el código
1. Descarga y extrae el archivo ZIP del proyecto
2. Abre una terminal en la carpeta extraída
3. Ejecuta estos comandos uno por uno:

```bash
# Inicializar git
git init

# Agregar todos los archivos
git add .

# Hacer el primer commit
git commit -m "Initial commit: CRM Lead Management System"

# Conectar con tu repositorio (reemplaza TU_USUARIO)
git remote add origin https://github.com/TU_USUARIO/crm-lead-management.git

# Subir el código
git push -u origin main
```

---

## 🌐 PASO 2: CONFIGURAR VERCEL

### 2.1 Conectar GitHub con Vercel
1. Ve a [vercel.com](https://vercel.com)
2. Haz clic en "Sign Up" o "Log In"
3. Selecciona "Continue with GitHub"
4. Autoriza a Vercel para acceder a tu GitHub

### 2.2 Importar el proyecto
1. En el dashboard de Vercel, haz clic en "New Project"
2. Busca tu repositorio "crm-lead-management"
3. Haz clic en "Import"

### 2.3 Configurar el proyecto
1. **Project Name**: `crm-lead-management`
2. **Framework Preset**: Vercel detectará automáticamente "Vite"
3. **Root Directory**: Deja en blanco (usa la raíz)
4. **Build and Output Settings**:
   - Build Command: `pnpm run build`
   - Output Directory: `dist`
   - Install Command: `pnpm install`

### 2.4 Configurar variables de entorno
1. En la sección "Environment Variables", agrega:

```
VITE_API_URL = https://tu-backend-url.com
```

**NOTA**: Por ahora deja este campo vacío, lo configuraremos después del backend.

### 2.5 Desplegar
1. Haz clic en "Deploy"
2. Espera a que termine el despliegue (2-3 minutos)
3. Verás una URL como: `https://crm-lead-management-tu-usuario.vercel.app`

---

## 🔧 PASO 3: DESPLEGAR EL BACKEND

### Opción A: Railway (Recomendado)

#### 3.1 Preparar el backend para Railway
1. En la carpeta `crm-backend`, crea un archivo `railway.json`:
```json
{
  "build": {
    "builder": "NIXPACKS"
  },
  "deploy": {
    "startCommand": "python src/main.py",
    "healthcheckPath": "/health"
  }
}
```

2. Crea un archivo `Procfile`:
```
web: python src/main.py
```

3. Actualiza `src/main.py` para usar el puerto de Railway:
```python
if __name__ == '__main__':
    port = int(os.environ.get('PORT', 5000))
    app.run(host='0.0.0.0', port=port, debug=False)
```

#### 3.2 Subir backend a GitHub
1. Crea otro repositorio: **"crm-backend"**
2. Sube el código del backend:
```bash
cd crm-backend
git init
git add .
git commit -m "Initial commit: CRM Backend"
git remote add origin https://github.com/TU_USUARIO/crm-backend.git
git push -u origin main
```

#### 3.3 Desplegar en Railway
1. Ve a [railway.app](https://railway.app)
2. Haz clic en "Start a New Project"
3. Selecciona "Deploy from GitHub repo"
4. Conecta tu cuenta de GitHub
5. Selecciona el repositorio "crm-backend"
6. Railway detectará automáticamente que es Python
7. Haz clic en "Deploy"

#### 3.4 Configurar variables de entorno en Railway
1. Ve a tu proyecto en Railway
2. Haz clic en "Variables"
3. Agrega estas variables:
```
SPREADSHEET_ID = tu_spreadsheet_id_aqui
FLASK_ENV = production
```

4. **IMPORTANTE**: Sube tu archivo `credentials.json`:
   - En Railway, ve a "Settings" > "Environment"
   - Crea una variable llamada `GOOGLE_CREDENTIALS`
   - Copia y pega todo el contenido de tu archivo `credentials.json`

#### 3.5 Obtener la URL del backend
1. En Railway, ve a "Settings" > "Domains"
2. Verás una URL como: `https://crm-backend-production-xxxx.up.railway.app`
3. **COPIA ESTA URL**

### Opción B: Render

#### 3.1 Configurar para Render
1. Ve a [render.com](https://render.com)
2. Conecta tu GitHub
3. Crea un "New Web Service"
4. Selecciona tu repositorio "crm-backend"
5. Configuración:
   - Name: `crm-backend`
   - Environment: `Python 3`
   - Build Command: `pip install -r requirements.txt`
   - Start Command: `python src/main.py`

---

## 🔗 PASO 4: CONECTAR FRONTEND CON BACKEND

### 4.1 Actualizar la URL del API
1. Ve a tu proyecto en Vercel
2. Ve a "Settings" > "Environment Variables"
3. Edita `VITE_API_URL` con la URL de tu backend:
```
VITE_API_URL = https://tu-backend-url.railway.app
```

### 4.2 Actualizar el código del frontend
1. En tu repositorio local, edita `src/services/api.js`:
```javascript
const API_BASE_URL = import.meta.env.VITE_API_URL || 'http://localhost:5000'
```

2. Haz commit y push:
```bash
git add .
git commit -m "Update API URL for production"
git push
```

3. Vercel automáticamente redespliegará tu aplicación

---

## 🧪 PASO 5: CONFIGURAR CORS EN PRODUCCIÓN

### 5.1 Actualizar CORS en el backend
1. En `src/main.py`, actualiza la configuración de CORS:
```python
from flask_cors import CORS

app = Flask(__name__)
CORS(app, origins=[
    "http://localhost:5173",  # Para desarrollo
    "https://tu-dominio.vercel.app",  # Tu dominio de Vercel
    "https://*.vercel.app"  # Cualquier subdominio de Vercel
])
```

2. Haz commit y push del backend:
```bash
git add .
git commit -m "Update CORS for production"
git push
```

---

## 🔐 PASO 6: CONFIGURAR GOOGLE SHEETS EN PRODUCCIÓN

### 6.1 Actualizar OAuth redirect URIs
1. Ve a [console.cloud.google.com](https://console.cloud.google.com)
2. Ve a "APIs y servicios" > "Credenciales"
3. Haz clic en tu ID de cliente OAuth 2.0
4. En "URIs de redirección autorizados", agrega:
```
https://tu-backend-url.railway.app/oauth2callback
```
5. Haz clic en "GUARDAR"

### 6.2 Configurar credenciales en producción
Si usas Railway:
1. El archivo `credentials.json` debe estar en la variable `GOOGLE_CREDENTIALS`
2. El backend automáticamente lo usará

Si usas Render:
1. Ve a "Environment" en tu servicio
2. Agrega una variable `GOOGLE_CREDENTIALS` con el contenido del JSON

---

## ✅ PASO 7: VERIFICACIÓN FINAL

### 7.1 Probar el frontend
1. Ve a tu URL de Vercel: `https://tu-app.vercel.app`
2. Deberías ver el CRM cargando
3. Si hay errores, revisa la consola del navegador

### 7.2 Probar el backend
1. Ve a: `https://tu-backend.railway.app/health`
2. Deberías ver: `{"status": "healthy"}`

### 7.3 Probar la integración completa
1. En el CRM, ve a la sección "Leads"
2. Intenta crear un nuevo lead
3. Verifica que aparezca en tu Google Sheet

---

## 🔧 SOLUCIÓN DE PROBLEMAS

### Error: "Network Error" o "Failed to fetch"
- **Causa**: El frontend no puede conectar con el backend
- **Solución**: Verifica que `VITE_API_URL` esté configurado correctamente

### Error: "CORS policy"
- **Causa**: CORS no está configurado para tu dominio
- **Solución**: Agrega tu dominio de Vercel a la configuración de CORS

### Error: "Google Sheets API quota exceeded"
- **Causa**: Demasiadas requests a la API
- **Solución**: Implementa rate limiting o usa caching

### Error: "Credentials not found"
- **Causa**: Las credenciales de Google no están configuradas
- **Solución**: Verifica que `GOOGLE_CREDENTIALS` esté configurado correctamente

---

## 🎉 ¡LISTO!

Tu CRM ahora está desplegado en:
- **Frontend**: Vercel (https://tu-app.vercel.app)
- **Backend**: Railway/Render (https://tu-backend.railway.app)
- **Base de datos**: Google Sheets

### URLs importantes:
- **CRM**: `https://tu-app.vercel.app`
- **API**: `https://tu-backend.railway.app`
- **Documentación**: En tu repositorio de GitHub

### Próximos pasos:
1. Configura un dominio personalizado en Vercel (opcional)
2. Configura monitoreo y alertas
3. Implementa backups automáticos
4. Agrega analytics y métricas

