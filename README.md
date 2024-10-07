# S0_BackEnd_Biometria_Y_Medio_Ambiente

# -----------------------------------------------------------------------------------------------------------------------------------------------------------------------

###  Descripción del Problema
El proyecto aborda la necesidad de monitorear los niveles de ozono en el ambiente, especialmente en áreas urbanas donde la contaminación atmosférica puede ser un problema grave. El sistema proporciona una solución automatizada para medir y almacenar estos datos, facilitando el análisis y la toma de decisiones en tiempo real.

  # El sistema permite a los usuarios:

- Medir los niveles de ozono y temperatura usando sensores conectados a la placa SparkFun.
- Enviar los datos a una aplicación móvil mediante Bluetooth.
- Transmitir los datos desde la app a un servidor REST, donde se almacenan en una base de datos MariaDB.
- Visualizar los datos a través de una API REST o en un sitio web conectado.

# -----------------------------------------------------------------------------------------------------------------------------------------------------------------------

###  Requisitos de instalacion

- Docker instalado en tu máquina.
- npm y Node.js.

# -----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## ¿Cómo funciona la Version 1 del backend?

Esta primera versión del proyecto utiliza  `Docker` para contenerizar dos servicios clave: una base de datos MariaDB y un backend basado en Node.js. Ambos servicios se coordinan mediante un archivo  `docker-compose.yml `, lo que automatiza su configuración y puesta en marcha, sin necesidad de ejecutar comandos manualmente.

# -----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Configuración y Diseño de MariaDB

- `Imagen Base`: Utiliza la última versión de MariaDB disponible en Docker Hub. Para obtenerla, ejecuta el comando docker pull mariadb.

- `Configuración de Contraseña`: Se establece la contraseña del usuario root como `1234` en esta versión inicial.

- `Inicialización de la Base de Datos`: El Dockerfile ejecuta el script SQL que inicializa la base de datos. Este archivo se encuentra en el directorio /sql del contenedor MariaDB y configura la estructura necesaria para almacenar las mediciones.

# -----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### SQL: Estructura de la Base de Datos
El archivo ejemploBBDD.sql contiene el código para crear la base de datos y una tabla llamada mediciones, que almacenará las lecturas de sensores. Esta tabla cuenta con los siguientes campos:

- `id`: Identificador único (Primary Key), con auto-incremento.
- `hora`: Almacena la hora de la medición (TIME), no puede ser nulo (NOT NULL).
- `lugar` El lugar donde se tomó la medición (VARCHAR(255)), no puede ser nulo.
- `id_sensor`: Un identificador del sensor (INT).
- `valorGas`: El valor medido del gas (DECIMAL(5,2)), no puede ser nulo.
- `valorTemperatura`: En el futuro, este campo almacenará la temperatura registrada por el sensor (también DECIMAL(5,2)).

# -----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Configuración de Node.js
Para inicializar el proyecto con npm, se ejecutó el comando npm init -y, lo que genera los archivos de configuración estándar de Node.js.

### Dockerfile de Node.js :

- `Imagen Base`: Utiliza la última imagen oficial de Node.js desde Docker Hub. Para obtener esta imagen, se ejecuta el comando docker pull node.

- `Directorio de Trabajo`: Se define un directorio de trabajo en el contenedor donde se ejecutará el código (main.js).

- `Copiar Archivos de Dependencias`: Se copian los archivos package.json y package-lock.json desde la máquina host al contenedor Docker, lo que permite gestionar las dependencias de Node.js.

- `Instalación de Dependencias`: Instala el paquete mariadb para poder interactuar con la base de datos (`npm install mariadb`).

- `Copiar Código`: El resto de los archivos del proyecto se copian desde el host al contenedor.
- `Exponer Puerto`: Se abre el puerto 8080 en la máquina local para poder acceder a la API en http://localhost:8080.
- `Ejecutar la Aplicación`: Se ejecuta el archivo principal (main.js) con el siguiente comando (`CMD ["node", "main.js"]`).

# -----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Servidor REST
El archivo `servidorREST.js` implementa un pequeño servidor REST que se conecta a la base de datos MariaDB utilizando los parámetros configurados en el código. También incluye una API documentada con Swagger, lo que facilita la interacción con los datos desde una interfaz gráfica.

La API puede realizar operaciones GET y POST sobre la tabla mediciones, lo que permite obtener datos almacenados o insertar nuevas mediciones en la base de datos.

Puedes explorar la API visualmente accediendo a http://localhost:3000/api-docs/.

# -----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### docker-compose.yml
El archivo docker-compose.yml es el corazón de la configuración del proyecto, ya que define y coordina los servicios de MariaDB y Node.js. Este archivo facilita la gestión de los contenedores, evitando la necesidad de comandos manuales.

- `MariaDB`:
- Se exponen el puerto 3306 para conectarse a la base de datos.
- Se crea una red interna llamada "redsprint0" que permite la comunicación entre los contenedores.
- Se incluye un healthcheck que verifica el estado de MariaDB, asegurando que el contenedor está funcionando correctamente.

- `Node.js`:
- Se expone el puerto 8080 para el servidor de API REST.
- Se conecta también a la red "redsprint0" para interactuar con el contenedor de MariaDB.

# -----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### Preguntas Frecuentes (FAQ)
1. ¿Qué tecnologías se utilizan en este proyecto?
El proyecto usa Arduino para la lectura de sensores, Node.js para manejar la lógica de negocio y la API REST, y MariaDB para almacenar los datos. Docker se utiliza para la contenedorización de los servicios.

2. ¿Cómo puedo visualizar los datos en la web?
Puedes acceder a los datos a través de la API en la URL http://localhost:8080, o utilizar herramientas como Swagger para explorar las API en http://localhost:3000/api-docs.

3. ¿Qué puedo hacer si los contenedores no se comunican entre sí?
Verifica que ambos contenedores (Node.js y MariaDB) están en la misma red de Docker. Usa docker network ls para confirmar que la red está creada correctamente.

4. ¿Cómo cambio la contraseña de MariaDB?
Puedes modificar la variable de entorno MARIADB_ROOT_PASSWORD en el archivo docker-compose.yml para cambiar la contraseña.

5. ¿Qué hace docker-compose up --build?
Sirve para : 
- `Construcción de Imágenes `: Si has modificado alguno de los archivos Dockerfile o los archivos de tu proyecto (como servidorREST.js o main.js en Node.js), este comando vuelve a construir las imágenes de Docker. Esto asegura que cualquier cambio reciente en el código o en la configuración se refleje en el contenedor.

- `Levantamiento de Contenedores`: Después de construir las imágenes, este comando levanta los servicios definidos en tu archivo docker-compose.yml. Esto significa que ambos contenedores, el de MariaDB y el de Node.js, se inician y comienzan a ejecutarse. Comenzarán a ejecutarse y estarán accesibles en los puertos especificados (MariaDB en el puerto 3306 y Node.js en el 8080).

6. ¿Qué hace docker-compose down?
- `Detener los Contenedores`: Detiene la ejecución de todos los contenedores que fueron iniciados por docker-compose up, terminando los procesos de MariaDB y Node.js.
Los servicios, como la base de datos o el servidor API, dejarán de estar disponibles en los puertos expuestos.

- `Eliminar Contenedores`: Todos los contenedores creados para el proyecto se eliminan. Esto asegura que no queden restos de contenedores corriendo en segundo plano que consuman recursos innecesariamente.

- `Eliminar Redes`: Las redes internas creadas para la comunicación entre los contenedores también se eliminan. Esto es importante para evitar conflictos con otros proyectos Docker que puedan utilizar las mismas configuraciones de red.

- `Eliminar Volúmenes (opcional)`: Si usas la opción --volumes, también se eliminarán los volúmenes persistentes donde se almacenan los datos de MariaDB. Esto es útil si quieres asegurarte de que no quede ninguna información de la base de datos.

