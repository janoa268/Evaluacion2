# Guía Técnico-Práctica: Integración de API REST y Automatización en Python

**Asignatura:** Telepresencia y Entornos Innovadores de Colaboración Humana (CUY6142)  
**Proyecto:** Gestión del Catálogo Digital de la Biblioteca Nacional  
**Desarrollador:** Jason Noa  
**RUT:** 258647432  
**Entorno de Desarrollo:** Máquina Virtual DEVASC (`devasc@labvm`)  

---

## 1. Requisitos del Entorno

Para llevar a cabo las actividades descritas en esta guía, se requieren los siguientes componentes activos dentro de la VM DEVASC:
* **Cliente API:** Postman versión Desktop / Web Agent.
* **Intérprete de Lenguaje:** Python 3.x con el paquete `requests` instalado.
* **Servidor Web / API:** Servicio local de biblioteca escuchando en `http://library.demo.local`.
* **Navegador Web:** Para la verificación visual del catálogo frontend.

---

## 2. Paso 1: Creación e Inserción de Libros Individuales vía Postman (HTTP POST)

### Objetivo
Agregar tres nuevos libros al catálogo de la biblioteca digital utilizando el método HTTP `POST` a través de la interfaz de Postman.

### Procedimiento
1. Abra **Postman** dentro de la máquina virtual DEVASC.
2. Seleccione la pestaña para crear una nueva solicitud y configure los siguientes campos:
   * **Método HTTP:** `POST`
   * **URL de Destino:** `http://library.demo.local/api/v1/books`
3. En la pestaña **Headers**, verifique que el encabezado `Content-Type` esté configurado como `application/json`.
4. En la pestaña **Body**, seleccione la opción **raw** y elija la sintaxis **JSON**.
5. Ingrese la estructura JSON con los datos requeridos (sustituyendo los datos del autor e ISBN según las instrucciones):

```json
{
  "id": 5,
  "title": "Guía de Estudio Para la Certificación Ccna Security",
  "author": "Jason Noa",
  "isbn": "258647432"
},
{
  "id": 6,
  "title": "Redes Cisco. CCNA Routing y Switching",
  "author": "Jason Noa",
  "isbn": "258647432"
},
{
  "id": 7,
  "title": "Redes Avanzadas 1. DEVNET",
  "author": "Jason Noa",
  "isbn": "258647432"
}
```

6. Presione el botón **Send**.
7. **Verificación de Respuesta:** Confirme que el servidor retorne un código de estado **`Status: 200 OK`** (o `201 Created`) con un tiempo de respuesta óptimo ($\approx 22\text{ ms}$).

---

## 3. Paso 2: Consulta, Ordenamiento e Inclusión de ISBN (HTTP GET)

### Objetivo
Realizar una petición `GET` a la API para recuperar el catálogo de libros ordenado por el nombre del autor y asegurando la inclusión del parámetro ISBN en la respuesta JSON.

### Procedimiento
1. En Postman, cree una nueva pestaña de solicitud.
2. Configure los parámetros de la petición:
   * **Método HTTP:** `GET`
   * **URL Base:** `http://library.demo.local/api/v1/books`
3. En la pestaña **Params** (Query Parameters), añada los siguientes pares clave-valor:
   * Key: `includeISBN` | Value: `true`
   * Key: `sortBy` | Value: `author`
4. La URL final generada será:
   `http://library.demo.local/api/v1/books?includeISBN=true&sortBy=author`
5. Ejecute la petición presionando **Send**.
6. **Verificación:** La respuesta devuelta debe mostrar una lista JSON ordenada alfabéticamente por el campo `"author"`, mostrando los registros agregados con sus respectivos ISBN (`"258647432"`).

---

## 4. Paso 3: Actualización de Título de Libro Existente

### Objetivo
Modificar el título del libro con `id: 5` para actualizar su versión a "V2.0".

### Procedimiento
1. Configure una solicitud HTTP `PUT` o `POST` apuntando al recurso específico: `http://library.demo.local/api/v1/books/5`.
2. En el cuerpo de la petición (**Body > raw > JSON**), especifique los datos actualizados:
```json
{
  "id": 5,
  "title": "Guía de Estudio para la Certificación CCNA Security V2.0",
  "author": "Jason Noa",
  "isbn": "258647432"
}
```
3. Envíe la solicitud y confirme que el servidor responda con código `200 OK`.

---

## 5. Paso 4: Poblado Masivo Automático con Script en Python

### Objetivo
Automatizar la inserción de múltiples registros (hasta 100 libros) consumiendo la API REST desde un script en Python utilizando la librería `requests`.

### Procedimiento
1. Abra la terminal bash en la máquina virtual DEVASC.
2. Navegue al directorio de trabajo scripts:
   ```bash
   cd /home/devasc/labs/devnet-src/python
   ```
3. Inicie el script de automatización `add100RandomBooks.py` mediante el comando:
   ```bash
   python3 /home/devasc/labs/devnet-src/school-library/add100RandomBooks.py
   ```
4. Verifique la salida en consola. El script iterará generando registros continuos e imprimiendo el estado de confirmación:
   ```text
   Book {'id': 4, 'title': 'Jason Noa', 'author': '258647432'} added.
   Book {'id': 5, 'title': 'Jason Noa', 'author': '258647432'} added.
   ...
   Book {'id': 50, 'title': 'Jason Noa', 'author': '258647432'} added.
   ...
   Book {'id': 100, 'title': 'Jason Noa', 'author': '258647432'} added.
   ```

---

## 6. Paso 5: Confirmación y Listado en Interfaz Web Frontend

### Objetivo
Validar que todos los registros procesados a través de la API REST se hayan visualizado correctamente en la aplicación web para usuarios finales.

### Procedimiento
1. Abra el navegador web en la VM DEVASC.
2. Navegue a la dirección de la biblioteca: `http://library.demo.local` o la interfaz web del proyecto.
3. Diríjase a la sección de catálogo de libros ("Welcome to the School Library").
4. Desplácese hasta la parte inferior del listado para corroborar la existencia de los libros indexados numéricamente (ejemplo: ID 91 al 100) asociados al autor **Jason Noa** y código de identificación **258647432**.
