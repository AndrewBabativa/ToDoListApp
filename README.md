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

#### **1. Resumen del Proyecto**
El proyecto ToDoListApp es una aplicación web que permite a los usuarios gestionar sus tareas mediante la creación, actualización, eliminación y consulta de tareas. La aplicación sigue la arquitectura de CQRS (Command Query Responsibility Segregation) y usa MediatR para manejar las peticiones de manera eficiente. El sistema está dividido en capas para mantener un código limpio y escalable, utilizando ASP.NET Core para la API, Entity Framework Core para la persistencia de datos y JWT para la autenticación y autorización.

#### **2. Estructura del Proyecto**
##### **2.1. Capas del Proyecto**
El proyecto está dividido en varias capas para mantener una arquitectura limpia:

- **Controllers:** Controladores que exponen los endpoints de la API.
- **Application:** Contiene la lógica de negocio, servicios, DTOs y comandos/consultas de MediatR.
- **Domain:** Contiene las entidades del dominio de la aplicación, las cuales representan las tareas y su estado.
- **Infrastructure:** Implementa los repositorios, la persistencia de datos (usando Entity Framework Core) y la unidad de trabajo (UnitOfWork).

##### **2.2. Controladores**
Existen dos controladores principales en el sistema:

- **AuthController:** Exponiendo el endpoint para la autenticación del usuario y la generación de un token JWT.
  - **POST /api/auth/login:** Permite a los usuarios iniciar sesión proporcionando un nombre de usuario y contraseña.
  
- **TaskController:** Exponiendo los endpoints para gestionar las tareas.
  - **POST /api/task:** Crear una nueva tarea.
  - **PUT /api/task/{id}:** Actualizar una tarea existente.
  - **DELETE /api/task/{id}:** Eliminar una tarea por su ID.
  - **GET /api/task:** Consultar todas las tareas.
  - **GET /api/task/{id}:** Consultar una tarea por su ID.
  - **PUT /api/task/completetask/{id}:** Marcar una tarea como completada.

##### **2.3. Servicios y Comandos**
- **TaskService:** Servicio que encapsula la lógica de negocio de las tareas. Maneja la creación, actualización, eliminación y consulta de tareas.
- **MediatR:** Implementado para manejar los comandos y consultas. Cada comando o consulta está representado por una clase que implementa la interfaz IRequest de MediatR. Los controladores usan MediatR para enviar estas solicitudes y recibir respuestas.

**Commands:**

- **CreateTaskCommand:** Comando para crear una nueva tarea.
- **UpdateTaskCommand:** Comando para actualizar una tarea existente.
- **DeleteTaskCommand:** Comando para eliminar una tarea.
- **CompleteTaskCommand:** Comando para marcar una tarea como completada.

**Queries:**

- **GetAllTasksQuery:** Consulta para obtener todas las tareas.
- **GetTaskQuery:** Consulta para obtener una tarea por su ID.

##### **2.4. Repositorio y Unidad de Trabajo**
- **ITaskRepository:** Interfaz que define las operaciones de acceso a datos relacionadas con las tareas.
- **TaskRepository:** Implementación concreta de ITaskRepository usando Entity Framework Core para interactuar con la base de datos.
- **UnitOfWork:** Gestiona la transacción y garantiza que las operaciones de la base de datos se realicen de forma atómica.

#### **3. Autenticación y Seguridad**
La aplicación utiliza JWT (JSON Web Tokens) para la autenticación y autorización:

- El endpoint **/api/auth/login** permite a los usuarios autenticarse y obtener un token JWT.
- El token debe ser enviado en el encabezado **Authorization** de cada solicitud que requiera autenticación.
- La autenticación está protegida por el atributo **[Authorize]** de ASP.NET Core, que asegura que solo los usuarios autenticados puedan acceder a ciertos recursos.

#### **4. Manejo de Excepciones**
Se ha implementado un sistema básico de manejo de excepciones. En caso de error, la API devuelve un mensaje de error detallado con un código de estado HTTP adecuado.

- **500 - Internal Server Error:** Se devuelve cuando ocurre un error inesperado.
- **401 - Unauthorized:** Se devuelve cuando las credenciales son incorrectas o el token JWT no es válido.
- **404 - Not Found:** Se devuelve cuando no se encuentra el recurso solicitado.

#### **5. Diagrama de Flujo y Secuencia**

##### **5.1. Flujo de Autenticación**
- El usuario envía un POST a **/api/auth/login** con las credenciales.
- Si las credenciales son correctas, se genera un token JWT y se devuelve al usuario.
- El usuario utiliza este token para realizar solicitudes a endpoints protegidos de la API.

##### **5.2. Flujo de Tareas**
- El usuario envía un POST a **/api/task** para crear una tarea. La tarea se guarda en la base de datos.
- El usuario puede **GET** todas las tareas o **GET** una tarea por su ID.
- El usuario puede actualizar o eliminar tareas mediante los endpoints **PUT** y **DELETE**.
- Para marcar una tarea como completada, el usuario envía un PUT a **/api/task/completetask/{id}**.

#### **6. Persistencia de Datos**
La persistencia de datos se maneja a través de Entity Framework Core y se utilizan las siguientes entidades:

**Entidades:**

```csharp
namespace ToDoListApp.Domain.Entities
{
    public class TaskEntity
    {
        public int Id { get; set; }
        public string Description { get; set; }
        public bool IsCompleted { get; set; }
    }
}
```

**DTOs:**

```csharp
namespace ToDoListApp.Application.DTOs
{
    public class TaskDto
    {
        public int Id { get; set; }
        public string Description { get; set; }
        public bool IsCompleted { get; set; }
    }
}
```

**Operaciones CRUD:**  
Las operaciones CRUD son realizadas por el **TaskRepository** utilizando los métodos de **DbContext** de Entity Framework Core.

#### **7. Despliegue**
Para desplegar la aplicación, se utiliza Docker para contenerizar tanto la API como la base de datos SQL Server.

**Docker Compose Configuration**  
El archivo **docker-compose.yml** se utiliza para configurar los contenedores de la aplicación y la base de datos SQL Server:

```yaml
volumes:
  data:
  
services:
  todolistapp:
    container_name: todolistapp.api
    build: .
    restart: always
    ports:
      - 8080:8080
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
      - ConnectionStrings__DefaultConnection=Server=mssql;Database=ToDoListDb;user id=sa;password=S3cur3P@ssw0rd;TrustServerCertificate=True;
  
  sql_server:
    container_name: mssql
    image: mcr.microsoft.com/mssql/server:2022-latest
    environment:
      - ACCEPT_EULA=Y
      - SA_PASSWORD=S3cur3P@ssw0rd
      - MSSQL_PID=Express
    ports:
      - 1434:1433
    volumes:
      - data:/var/opt/mssql
```

**Descripción de los servicios:**

- **todolistapp:** Este contenedor ejecuta la API. Se construye a partir del **Dockerfile** en el directorio raíz del proyecto, mapeando el puerto 8080 en el contenedor al puerto 8080 en el host. Utiliza una cadena de conexión a SQL Server, que es configurada en el entorno de la aplicación.
  
- **sql_server:** Este contenedor ejecuta una instancia de SQL Server 2022. El contenedor está configurado con las credenciales de administrador necesarias para acceder a la base de datos, y se asegura que los datos sean persistentes mediante un volumen llamado **data**.

#### **8. Tecnologías y Herramientas**
- **ASP.NET Core:** Framework para crear la API.
- **Entity Framework Core:** ORM para interactuar con la base de datos.
- **MediatR:** Librería para implementar el patrón CQRS.
- **JWT (JSON Web Token):** Para autenticación y autorización.
- **Docker:** Para contenerizar la aplicación y la base de datos.
- **SQL Server:** Base de datos para almacenar las tareas.

---
