# Backend con Node.js

## 1. Prerrequisitos

Antes de empezar, asegúrate de tener instalado lo siguiente:

  * **Node.js**: Debes tener una versión reciente de Node.js. Puedes descargarlo desde [nodejs.org](https://nodejs.org/). Para verificar si lo tienes, abre tu terminal y ejecuta:
    ```bash
    node -v
    ```
  * **NPM (Node Package Manager)**: Viene incluido con Node.js. Para verificar la versión, ejecuta:
    ```bash
    npm -v
    ```
  * **Un editor de código**: Se recomienda **Visual Studio Code** por su gran soporte para JavaScript y Node.js.
  * **Una herramienta para probar APIs**: Se recomienda **Postman** o **Insomnia** para enviar peticiones a tu API y probar los endpoints.

-----

## 2. Configuración Inicial del Proyecto

Vamos a crear la estructura básica de nuestro proyecto.

### a. Crear la carpeta del proyecto

Abre tu terminal y crea una nueva carpeta para tu proyecto. Luego, navega hacia ella.

```bash
mkdir mi-proyecto-node
cd mi-proyecto-node
```

### b. Inicializar el proyecto de Node.js

Este comando creará un archivo `package.json`, que es el manifiesto de tu proyecto. Contiene metadatos y gestiona las dependencias.

```bash
npm init -y
```

El flag `-y` acepta todas las configuraciones por defecto.

### c. Instalar las dependencias necesarias

Para este proyecto, usaremos **Express.js**, un framework minimalista que nos facilitará la creación del servidor y las rutas de la API.

```bash
npm install express
```

Para mejorar el flujo de desarrollo, instalaremos **Nodemon**. Esta herramienta reinicia automáticamente el servidor cada vez que detecta un cambio en el código, ahorrándote mucho tiempo. Lo instalaremos como una dependencia de desarrollo (`--save-dev`).

```bash
npm install --save-dev nodemon
```

### d. Configurar el script de inicio

Abre tu archivo `package.json` y modifica la sección `"scripts"` para usar `nodemon`.

```json
"scripts": {
  "start": "node index.js",
  "dev": "nodemon index.js"
},
```

  * `npm start`: Ejecutará el proyecto con Node.
  * `npm run dev`: Ejecutará el proyecto con `nodemon`, ideal para el desarrollo.

### e. Crear el archivo de entrada

Crea un archivo llamado `index.js` en la raíz de tu proyecto. Este será el punto de partida de nuestra aplicación.

```bash
touch index.js
```

### f. Crear un servidor básico con Express

Añade el siguiente código a tu archivo `index.js` para crear un servidor web simple.

```javascript
// 1. Importar el módulo de Express
const express = require('express');

// 2. Crear una instancia de la aplicación Express
const app = express();

// 3. Definir el puerto en el que correrá el servidor
const PORT = process.env.PORT || 3000;

// Middleware para entender JSON
app.use(express.json());

// 4. Definir una ruta de prueba
app.get('/', (req, res) => {
  res.send('¡Hola, mundo! Mi API está funcionando. 🎉');
});

// 5. Iniciar el servidor
app.listen(PORT, () => {
  console.log(`Servidor corriendo en http://localhost:${PORT}`);
});
```

Ahora, ejecuta tu servidor en modo de desarrollo:

```bash
npm run dev
```

Si visitas `http://localhost:3000` en tu navegador, deberías ver el mensaje "¡Hola, mundo\! Mi API está funcionando. 🎉".

-----

## 📂 3. Estructura de Carpetas del Proyecto

Una buena estructura es clave para un proyecto mantenible. Crearemos una arquitectura en capas.

```
mi-proyecto-node/
├── node_modules/
├── src/
│   ├── api/
│   │   ├── controllers/
│   │   │   └── user.controller.js
│   │   ├── routes/
│   │   │   ├── index.js
│   │   │   └── user.routes.js
│   ├── models/
│   │   └── user.model.js
│   └── services/
│       └── user.service.js
├── .gitignore
├── index.js
└── package.json
```

**Explicación de cada directorio:**

  * `src/`: Contendrá todo nuestro código fuente.
  * `src/api/`: Relacionado con la capa de la API (rutas y controladores).
      * `controllers/`: Manejan la lógica de las peticiones HTTP (request y response). Llaman a los servicios para realizar las operaciones.
      * `routes/`: Definen los endpoints de nuestra API (ej: `/users`, `/products`).
  * `src/models/`: Define la estructura de nuestros datos (las **entidades**). Por ejemplo, un modelo de `Usuario` con `nombre` y `email`.
  * `src/services/`: Contiene la lógica de negocio. Es el intermediario entre los controladores y los modelos.

Crea estas carpetas y archivos vacíos en tu proyecto para tener la estructura lista.

-----

## 🧱 4. Creando la Entidad (Modelo)

Vamos a crear una entidad `User`. Por simplicidad, no usaremos una base de datos real. Simularemos una con un array en memoria.

**`src/models/user.model.js`**

```javascript
// En una aplicación real, esto interactuaría con una base de datos.
// Aquí simulamos una "base de datos" en memoria con un array.

let users = [
  { id: 1, name: 'Alice', email: 'alice@example.com' },
  { id: 2, name: 'Bob', email: 'bob@example.com' }
];
let nextId = 3;

// Exportamos las funciones que nos permitirán interactuar con los datos.
module.exports = {
  // Función para obtener todos los usuarios
  findAll: () => {
    return users;
  },

  // Función para encontrar un usuario por su ID
  findById: (id) => {
    return users.find(user => user.id === id);
  },

  // Función para crear un nuevo usuario
  create: (userData) => {
    const newUser = { id: nextId++, ...userData };
    users.push(newUser);
    return newUser;
  },

  // Función para actualizar un usuario
  update: (id, updateData) => {
    const userIndex = users.findIndex(user => user.id === id);
    if (userIndex === -1) {
      return null; // No encontrado
    }
    users[userIndex] = { ...users[userIndex], ...updateData };
    return users[userizenIndex];
  },

  // Función para eliminar un usuario
  delete: (id) => {
    const userIndex = users.findIndex(user => user.id === id);
    if (userIndex === -1) {
      return false; // No encontrado
    }
    users.splice(userIndex, 1);
    return true; // Eliminado con éxito
  }
};
```

-----

## ⚙️ 5. Creando el Servicio

El servicio contendrá la lógica de negocio. Usará el modelo para acceder a los datos y realizará las operaciones que el controlador le pida.

**`src/services/user.service.js`**

```javascript
// Importamos el "modelo" que simula la base de datos
const UserModel = require('../models/user.model');

// El servicio actúa como un intermediario que contiene la lógica de negocio.
class UserService {
  
  getAllUsers() {
    // Simplemente llama al método del modelo.
    // En un caso real, aquí podría haber lógica adicional (ej: filtrar datos).
    return UserModel.findAll();
  }

  getUserById(id) {
    const userId = parseInt(id, 10); // Aseguramos que el ID sea un número
    const user = UserModel.findById(userId);
    if (!user) {
      // Manejo de error si el usuario no existe
      throw new Error('Usuario no encontrado');
    }
    return user;
  }

  createUser(userData) {
    const { name, email } = userData;
    if (!name || !email) {
      // Validación básica
      throw new Error('El nombre y el email son requeridos');
    }
    return UserModel.create({ name, email });
  }

  updateUser(id, updateData) {
    const userId = parseInt(id, 10);
    // Lógica para actualizar...
    const updatedUser = UserModel.update(userId, updateData);
    if (!updatedUser) {
      throw new Error('Usuario no encontrado para actualizar');
    }
    return updatedUser;
  }

  deleteUser(id) {
    const userId = parseInt(id, 10);
    const success = UserModel.delete(userId);
    if (!success) {
      throw new Error('Usuario no encontrado para eliminar');
    }
    return { message: 'Usuario eliminado correctamente' };
  }
}

// Exportamos una instancia de la clase para usarla en el controlador
module.exports = new UserService();
```

-----

## 🔌 6. Creando el Controlador y las Rutas (API)

Finalmente, expondremos nuestra lógica a través de una API REST.

### a. El Controlador

El controlador recibe las peticiones HTTP y utiliza el servicio para procesarlas.

**`src/api/controllers/user.controller.js`**

```javascript
// Importamos el servicio para poder usar su lógica
const UserService = require('../../services/user.service');

// El controlador maneja las peticiones (req) y respuestas (res) HTTP.
const getAllUsers = (req, res) => {
  try {
    const users = UserService.getAllUsers();
    res.status(200).json(users); // 200 OK
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
};

const getUserById = (req, res) => {
  try {
    const user = UserService.getUserById(req.params.id);
    res.status(200).json(user);
  } catch (error) {
    // Si el error es "Usuario no encontrado", devolvemos 404
    res.status(404).json({ message: error.message });
  }
};

const createUser = (req, res) => {
  try {
    const newUser = UserService.createUser(req.body);
    res.status(201).json(newUser); // 201 Created
  } catch (error) {
    res.status(400).json({ message: error.message }); // 400 Bad Request
  }
};

const updateUser = (req, res) => {
  try {
    const updatedUser = UserService.updateUser(req.params.id, req.body);
    res.status(200).json(updatedUser);
  } catch (error) {
    res.status(404).json({ message: error.message });
  }
};

const deleteUser = (req, res) => {
  try {
    const result = UserService.deleteUser(req.params.id);
    res.status(200).json(result);
  } catch (error) {
    res.status(404).json({ message: error.message });
  }
};

module.exports = {
  getAllUsers,
  getUserById,
  createUser,
  updateUser,
  deleteUser
};
```

### b. Las Rutas

Las rutas definen los endpoints (URLs) y los métodos HTTP (GET, POST, PUT, DELETE) que se asociarán a cada función del controlador.

**`src/api/routes/user.routes.js`**

```javascript
const express = require('express');
const router = express.Router();
const userController = require('../controllers/user.controller');

// Definimos las rutas para la entidad "users"
router.get('/', userController.getAllUsers);         // GET /api/users
router.get('/:id', userController.getUserById);     // GET /api/users/1
router.post('/', userController.createUser);        // POST /api/users
router.put('/:id', userController.updateUser);      // PUT /api/users/1
router.delete('/:id', userController.deleteUser);   // DELETE /api/users/1

module.exports = router;
```

### c. Archivo de Rutas Principal

Crearemos un archivo `index.js` en `src/api/routes/` para centralizar todas las rutas de nuestra aplicación. Esto es muy útil cuando tienes múltiples entidades (usuarios, productos, etc.).

**`src/api/routes/index.js`**

```javascript
const express = require('express');
const router = express.Router();
const userRoutes = require('./user.routes');

// Cuando una petición llegue con "/users", usará las rutas definidas en userRoutes
router.use('/users', userRoutes);

// Aquí podrías añadir más rutas:
// router.use('/products', productRoutes);

module.exports = router;
```

-----

## 🔗 7. Conectando Todo en `index.js`

Ahora, volvamos a nuestro archivo principal `index.js` para conectar las rutas que hemos creado.

**`index.js` (versión final)**

```javascript
const express = require('express');
const apiRoutes = require('./src/api/routes'); // Importamos nuestro enrutador principal

const app = express();
const PORT = process.env.PORT || 3000;

// Middleware para que el servidor entienda peticiones con cuerpo en formato JSON
app.use(express.json());

// Ruta de bienvenida
app.get('/', (req, res) => {
  res.send('¡Hola, mundo! Mi API está funcionando. 🎉');
});

// Usamos nuestro enrutador principal para todas las rutas que empiecen con /api
app.use('/api', apiRoutes);

// Iniciar el servidor
app.listen(PORT, () => {
  console.log(`Servidor corriendo en http://localhost:${PORT}`);
});
```

-----

## ✅ 8. ¡A Probar la API\!

Reinicia tu servidor (`npm run dev`) y usa Postman o una herramienta similar para probar los endpoints que hemos creado:

  * **`GET http://localhost:3000/api/users`**
      * Debería devolver la lista de todos los usuarios.
  * **`GET http://localhost:3000/api/users/1`**
      * Debería devolver el usuario con ID 1.
  * **`POST http://localhost:3000/api/users`**
      * En el "Body", selecciona "raw" y "JSON". Envía algo como:
        ```json
        {
            "name": "Charlie",
            "email": "charlie@example.com"
        }
        ```
      * Debería devolver el nuevo usuario creado con su ID.
  * **`PUT http://localhost:3000/api/users/1`**
      * En el "Body" (JSON), envía:
        ```json
        {
            "name": "Alice Smith"
        }
        ```
      * Debería devolver el usuario actualizado.
  * **`DELETE http://localhost:3000/api/users/2`**
      * Debería devolver un mensaje de confirmación.
   

¡Excelente\! Aquí tienes una guía completa para un segundo proyecto, un poco más elaborado.

Vamos a crear una API para gestionar **Autores** y sus **Libros**, demostrando una relación "uno a muchos" (un autor puede tener muchos libros). Mantendremos la misma estructura clara y organizada.

-----

# Guía 2: Proyecto Node.js con Entidades Relacionadas (Autores y Libros)

## 📋 1. Prerrequisitos

Asegúrate de tener todo lo necesario, igual que en la guía anterior:

  * **Node.js** y **NPM** instalados.
  * Un editor de código como **VS Code**.
  * Una herramienta para probar APIs como **Postman**.

-----

## 🏗️ 2. Configuración Inicial del Proyecto

Empezaremos un proyecto nuevo desde cero.

### a. Crear y acceder a la carpeta del proyecto

```bash
mkdir autores-y-libros-api
cd autores-y-libros-api
```

### b. Inicializar el proyecto y agregar dependencias

```bash
# Inicializar el proyecto
npm init -y

# Instalar Express para el servidor
npm install express

# Instalar Nodemon para el desarrollo
npm install --save-dev nodemon
```

### c. Configurar el script de desarrollo

Abre tu `package.json` y asegúrate de que la sección `scripts` incluya el comando `dev`:

```json
"scripts": {
  "start": "node index.js",
  "dev": "nodemon index.js"
},
```

### d. Crear la estructura de carpetas

Crearemos una estructura similar, pero ahora duplicada para nuestras dos entidades: `Author` y `Book`.

```
autores-y-libros-api/
├── node_modules/
├── src/
│   ├── api/
│   │   ├── controllers/
│   │   │   ├── author.controller.js
│   │   │   └── book.controller.js
│   │   ├── routes/
│   │   │   ├── index.js
│   │   │   ├── author.routes.js
│   │   │   └── book.routes.js
│   ├── models/
│   │   ├── author.model.js
│   │   └── book.model.js
│   └── services/
│       ├── author.service.js
│       └── book.service.js
├── index.js
└── package.json
```

Crea todas estas carpetas y archivos vacíos para tener todo listo.

### e. Crear el servidor básico en `index.js`

Este archivo será el punto de entrada que conectará todo.

**`index.js`**

```javascript
const express = require('express');
const apiRoutes = require('./src/api/routes'); // Aún no existe, pero lo importaremos

const app = express();
const PORT = process.env.PORT || 4000; // Usaremos otro puerto para no chocar con el proyecto anterior

// Middleware para entender JSON
app.use(express.json());

// Ruta principal de bienvenida
app.get('/', (req, res) => {
  res.send('API de Autores y Libros está funcionando correctamente. 📖');
});

// Conectar todas las rutas de la API bajo el prefijo /api
app.use('/api', apiRoutes);


app.listen(PORT, () => {
  console.log(`Servidor escuchando en http://localhost:${PORT}`);
});
```

-----

## 🧱 3. Creando las Entidades (Modelos)

Aquí definiremos la estructura de nuestros datos y la **relación** entre ellos. Un `Book` tendrá un `authorId` para saber a quién pertenece.

#### `src/models/author.model.js`

```javascript
// Simulamos la tabla "authors"
let authors = [
  { id: 1, name: 'Gabriel García Márquez', nationality: 'Colombiano' },
  { id: 2, name: 'J.K. Rowling', nationality: 'Británica' }
];
let nextId = 3;

module.exports = {
  findAll: () => authors,
  findById: (id) => authors.find(author => author.id === id),
  create: (authorData) => {
    const newAuthor = { id: nextId++, ...authorData };
    authors.push(newAuthor);
    return newAuthor;
  },
  delete: (id) => {
    const authorIndex = authors.findIndex(author => author.id === id);
    if (authorIndex === -1) return false;
    authors.splice(authorIndex, 1);
    return true;
  }
};
```

#### `src/models/book.model.js`

```javascript
// Simulamos la tabla "books"
let books = [
  { id: 1, title: 'Cien años de soledad', year: 1967, authorId: 1 },
  { id: 2, title: 'El amor en los tiempos del cólera', year: 1985, authorId: 1 },
  { id: 3, title: 'Harry Potter y la piedra filosofal', year: 1997, authorId: 2 }
];
let nextId = 4;

module.exports = {
  findAll: () => books,
  findById: (id) => books.find(book => book.id === id),
  // ¡Importante! Esta función nos permite encontrar libros por autor
  findByAuthorId: (authorId) => books.filter(book => book.authorId === authorId),
  create: (bookData) => {
    // bookData debe incluir title, year y authorId
    const newBook = { id: nextId++, ...bookData };
    books.push(newBook);
    return newBook;
  },
};
```

-----

## ⚙️ 4. Creando los Servicios

Los servicios contendrán la lógica de negocio. El servicio de autor, por ejemplo, podría obtener un autor y también todos sus libros.

#### `src/services/author.service.js`

```javascript
const AuthorModel = require('../models/author.model');
const BookModel = require('../models/book.model'); // Importamos el modelo de libros

class AuthorService {
  getAllAuthors() {
    return AuthorModel.findAll();
  }

  getAuthorById(id) {
    const authorId = parseInt(id, 10);
    const author = AuthorModel.findById(authorId);
    if (!author) {
      throw new Error('Autor no encontrado');
    }
    // ¡Lógica de relación! Buscamos los libros de este autor
    const books = BookModel.findByAuthorId(authorId);
    return { ...author, books }; // Devolvemos el autor junto con sus libros
  }

  createAuthor(authorData) {
    const { name, nationality } = authorData;
    if (!name || !nationality) {
      throw new Error('El nombre y la nacionalidad son requeridos');
    }
    return AuthorModel.create({ name, nationality });
  }
}

module.exports = new AuthorService();
```

#### `src/services/book.service.js`

```javascript
const BookModel = require('../models/book.model');
const AuthorModel = require('../models/author.model'); // Requerido para validación

class BookService {
  getAllBooks() {
    return BookModel.findAll();
  }
  
  createBook(bookData) {
    const { title, year, authorId } = bookData;
    if (!title || !year || !authorId) {
      throw new Error('Título, año y authorId son requeridos');
    }
    // Verificamos que el autor exista antes de crear el libro
    const authorExists = AuthorModel.findById(parseInt(authorId, 10));
    if (!authorExists) {
      throw new Error('El autor especificado no existe');
    }
    return BookModel.create({ title, year, authorId: parseInt(authorId, 10) });
  }
}

module.exports = new BookService();
```

-----

## 🔌 5. Creando Controladores y Rutas

Ahora expondremos la lógica de los servicios a través de endpoints en nuestra API.

#### `src/api/controllers/author.controller.js`

```javascript
const AuthorService = require('../../services/author.service');

const getAllAuthors = (req, res) => {
  try {
    const authors = AuthorService.getAllAuthors();
    res.status(200).json(authors);
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
};

const getAuthorById = (req, res) => {
  try {
    const author = AuthorService.getAuthorById(req.params.id);
    res.status(200).json(author);
  } catch (error) {
    res.status(404).json({ message: error.message });
  }
};

const createAuthor = (req, res) => {
  try {
    const newAuthor = AuthorService.createAuthor(req.body);
    res.status(201).json(newAuthor);
  } catch (error) {
    res.status(400).json({ message: error.message });
  }
};

module.exports = {
  getAllAuthors,
  getAuthorById,
  createAuthor
};
```

#### `src/api/controllers/book.controller.js`

```javascript
const BookService = require('../../services/book.service');

const getAllBooks = (req, res) => {
  try {
    const books = BookService.getAllBooks();
    res.status(200).json(books);
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
};

const createBook = (req, res) => {
  try {
    const newBook = BookService.createBook(req.body);
    res.status(201).json(newBook);
  } catch (error) {
    // Si el autor no existe o faltan datos, puede ser un 404 o 400
    if (error.message.includes('no existe')) {
        return res.status(404).json({ message: error.message });
    }
    res.status(400).json({ message: error.message });
  }
};

module.exports = {
  getAllBooks,
  createBook
};
```

#### `src/api/routes/author.routes.js`

```javascript
const express = require('express');
const router = express.Router();
const authorController = require('../controllers/author.controller');

router.get('/', authorController.getAllAuthors);
router.get('/:id', authorController.getAuthorById);
router.post('/', authorController.createAuthor);

module.exports = router;
```

#### `src/api/routes/book.routes.js`

```javascript
const express = require('express');
const router = express.Router();
const bookController = require('../controllers/book.controller');

router.get('/', bookController.getAllBooks);
router.post('/', bookController.createBook);

module.exports = router;
```

-----

## 🔗 6. Conectando Todas las Rutas

Finalmente, unimos todas nuestras rutas en el enrutador principal.

#### `src/api/routes/index.js`

```javascript
const express = require('express');
const router = express.Router();

const authorRoutes = require('./author.routes');
const bookRoutes = require('./book.routes');

// Las peticiones a /api/authors serán manejadas por authorRoutes
router.use('/authors', authorRoutes);

// Las peticiones a /api/books serán manejadas por bookRoutes
router.use('/books', bookRoutes);

module.exports = router;
```

-----

## ✅ 7. ¡A Probar la API Relacional\!

Inicia tu servidor con `npm run dev` y usa Postman para probar los siguientes endpoints:

1.  **Obtener todos los autores:**

      * `GET http://localhost:4000/api/authors`
      * Debería devolver la lista de autores.

2.  **Obtener un autor y sus libros (la magia de la relación):**

      * `GET http://localhost:4000/api/authors/1`
      * Debería devolver a Gabriel García Márquez **junto con un array de sus libros**.

3.  **Crear un nuevo autor:**

      * `POST http://localhost:4000/api/authors`
      * Body (raw, JSON):
        ```json
        {
            "name": "Julio Verne",
            "nationality": "Francés"
        }
        ```
      * Debería devolver el nuevo autor con `id: 3`.

4.  **Crear un libro para un autor existente:**

      * `POST http://localhost:4000/api/books`
      * Body (raw, JSON):
        ```json
        {
            "title": "Viaje al centro de la Tierra",
            "year": 1864,
            "authorId": 3
        }
        ```
      * Debería crear el libro y asociarlo a Julio Verne.

5.  **Intentar crear un libro para un autor que no existe:**

      * `POST http://localhost:4000/api/books`
      * Body (raw, JSON):
        ```json
        {
            "title": "Libro Fantasma",
            "year": 2025,
            "authorId": 99
        }
        ```
      * Debería devolver un error `404` con el mensaje "El autor especificado no existe".
