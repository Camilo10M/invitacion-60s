# Instrucciones para Configurar Google Sheets

## Paso 1: Crear Google Sheet

1. Ve a [Google Sheets](https://sheets.google.com)
2. Crea una nueva hoja de cálculo
3. Nómbrala algo como "Confirmaciones de Asistencia"
4. En la primera fila, agrega estos encabezados:
   - A1: `Nombre`
   - B1: `Personas`
   - C1: `Mensaje`
   - D1: `Fecha`

## Paso 2: Crear Google Apps Script

1. En tu Google Sheet, ve a **Extensiones** → **Apps Script**
2. Borra el código que aparece y pega este código:

```javascript
function doPost(e) {
  try {
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    var data = JSON.parse(e.postData.contents);
    
    // Agregar nueva fila con los datos
    sheet.appendRow([
      data.name,
      data.guests,
      data.message,
      data.timestamp
    ]);
    
    return ContentService
      .createTextOutput(JSON.stringify({status: 'success'}))
      .setMimeType(ContentService.MimeType.JSON);
      
  } catch(error) {
    return ContentService
      .createTextOutput(JSON.stringify({status: 'error', message: error.toString()}))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

3. Guarda el proyecto (Ctrl+S o Cmd+S)
4. Dale un nombre como "Formulario de Confirmación"

## Paso 3: Desplegar el Script

1. Haz clic en **Implementar** → **Nueva implementación**
2. En "Seleccionar tipo", elige **Aplicación web**
3. Configura así:
   - **Descripción**: "Formulario de confirmación"
   - **Ejecutar como**: "Yo"
   - **Quién tiene acceso**: "Cualquier persona"
4. Haz clic en **Implementar**
5. Te pedirá autorización - haz clic en **Revisar permisos**
6. Elige tu cuenta de Google
7. Si aparece "Google no ha verificado esta app", haz clic en **Avanzado** → **Ir a Formulario de confirmación (no seguro)**
8. Haz clic en **Permitir**
9. Copia la **URL de la aplicación web** que te proporcionan

## Paso 4: Conectar con tu Proyecto

1. Abre el archivo `src/App.vue` en tu proyecto
2. Busca esta línea (aprox línea 42):
   ```javascript
   const GOOGLE_SCRIPT_URL = 'TU_URL_DE_GOOGLE_APPS_SCRIPT_AQUI'
   ```
3. Reemplaza `'TU_URL_DE_GOOGLE_APPS_SCRIPT_AQUI'` con la URL que copiaste
4. Guarda los cambios

## Paso 5: Probar

1. Abre tu invitación en el navegador
2. Haz clic en "Confirma tu asistencia aquí"
3. Llena el formulario y envía
4. Verifica que los datos aparezcan en tu Google Sheet

## Solución de Problemas

### Error de CORS
Si tienes problemas con CORS, el script ya está configurado con `mode: 'no-cors'` que debería funcionar.

### Error de autorización
Si tienes problemas de autorización:
- Asegúrate de elegir "Cualquier persona" en "Quién tiene acceso"
- Verifica que los permisos del script sean correctos

### Datos no aparecen
- Verifica que la URL esté correcta en el código
- Revisa la consola del navegador para errores
- Asegúrate de que el script esté guardado y desplegado correctamente

## Seguridad

- Los datos se envían de forma segura a Google
- Tu Google Sheet debe estar configurado con los permisos adecuados
- Considera limitar el acceso al script si es necesario