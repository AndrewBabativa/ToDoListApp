# **ToDoListApp**

### **Prueba Técnica**
Creado en .Net 9.0 - Microsoft Visual Studio Community 2022 (64-bit)

---

## **Pasos para ejecutar el proyecto**

1. **Clonar el repositorio en un directorio local**
   - Comando para clonar el repositorio:  
     ```bash
     git clone https://github.com/AndrewBabativa/ToDoListApp.git
     ```
   - Deberías descargar el siguiente contenido:
     ![image](https://github.com/user-attachments/assets/1c4f8bc7-5310-41ed-9471-5033eac5d259)

2. **Tener Docker activo en la máquina de prueba**

3. **Abrir un terminal e ir a la carpeta de descarga**
   ![image](https://github.com/user-attachments/assets/c8b66585-c8cc-4318-9ad1-6f08d8aa9e23)

4. **Ejecutar el comando para levantar los contenedores**  
   - Comando:
     ```bash
     docker-compose up -d
     ```
   - Esto creará los contenedores necesarios. La configuración ya está incluida en el archivo `docker-compose.yml`. Debería aparecer algo como esto:
     ![image](https://github.com/user-attachments/assets/8da5657a-472f-422b-915d-00546a4e941b)

5. **Verificar los contenedores**
   - Los dos contenedores están expuestos al host por los puertos **8080** y **8081**.
   - Las rutas de acceso quedan configuradas así:
     - **API**: [http://localhost:8080/swagger/index.html](http://localhost:8080/swagger/index.html)  
       _(Swagger se deja habilitado para fines de pruebas)_

---

## **Manual de Usuario para Pruebas en Postman**

### **Introducción**
Este documento describe cómo utilizar las pruebas de los servicios REST de la aplicación mediante Postman. Asegúrate de tener instalado Postman antes de comenzar. Este manual incluye pasos para configurar el token de autenticación y una lista detallada de los servicios disponibles en la API.

---

### **Configuración Inicial**

1. **Importar la Colección Postman**:
   - Descarga la colección `\docs\postman\Tasks.postman_collection.json` del repositorio del proyecto.
   - Abre Postman y haz clic en el botón **Import**.
   - Selecciona el archivo de la colección y cárgalo.

2. **Requisitos Previos**:
   - La aplicación debe estar en ejecución localmente en **http://localhost:8080**.
   - Asegúrate de que Docker esté ejecutando los contenedores y que el endpoint de autenticación esté disponible para generar el token de seguridad.

---

### **Paso 1: Obtener el Token de Seguridad**

El token de autenticación es necesario para consumir los demás servicios de la API. Sigue estos pasos para obtener el token:

1. Abre Postman y selecciona la solicitud `Login` de la colección.
2. Configura los valores del cuerpo de la solicitud (**Body**) como tipo `raw` con formato `JSON`:
   ```json
   {
       "user": "test",
       "password": "password"
   }
   ```
3. Haz clic en **Send**.
4. El servicio devolverá un token de seguridad en el campo `authToken`. Este token se guarda automáticamente en la variable de entorno `authToken`.

---

### **Paso 2: Configuración de la Variable de Entorno**

1. Asegúrate de que la variable de entorno `authToken` esté configurada:
   - Ve al menú **Environments** en Postman.
   - Selecciona el entorno correspondiente (por ejemplo: `Local`).
   - Verifica que la variable `authToken` tenga un valor asignado.

2. Los servicios de la API utilizan esta variable automáticamente en el encabezado (**Headers**) con la clave `Authorization` y el valor:  
   `Bearer {{authToken}}`.

---

### **Lista de Servicios Disponibles**

#### **1. Crear una tarea**
- **Método**: POST  
- **URL**: `http://localhost:8080/api/task`  
- **Cuerpo (Body)**:
  ```json
  {
      "description": "Tarea de ejemplo",
      "isCompleted": false
  }
  ```
- **Requiere Token**: Sí  

#### **2. Actualizar una tarea**
- **Método**: PUT  
- **URL**: `http://localhost:8080/api/task/3`  
- **Cuerpo (Body)**:
  ```json
  {
      "description": "Tarea actualizada",
      "isCompleted": true
  }
  ```
- **Requiere Token**: Sí  

#### **3. Eliminar una tarea**
- **Método**: DELETE  
- **URL**: `http://localhost:8080/api/task/2`  
- **Encabezado (Headers)**:
  - `Authorization`: `Bearer {{authToken}}`
- **Requiere Token**: Sí  

#### **4. Obtener todas las tareas**
- **Método**: GET  
- **URL**: `http://localhost:8080/api/task`  
- **Requiere Token**: Sí  

#### **5. Completar una tarea**
- **Método**: POST  
- **URL**: `http://localhost:8080/api/task/completetask/2`  
- **Requiere Token**: Sí  

---

### **Notas Adicionales**
- Si el token ha expirado, repite el **Paso 1** para generar un nuevo token.
- Asegúrate de que la variable `authToken` esté correctamente configurada antes de ejecutar los servicios.
- Verifica que Docker esté corriendo y que los contenedores estén levantados antes de iniciar las pruebas.

---

