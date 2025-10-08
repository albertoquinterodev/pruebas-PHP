pruebas-PHP
Prácticas de PHP en Refineria


DB Class - Sistema de Base de Datos MySQLi

Una clase PHP simple y eficiente para operaciones CRUD con MySQL usando MySQLi orientado a objetos.

📋 Características

- ✅ Conexión automática a base de datos MySQL
- ✅ Operaciones CRUD completas (Create, Read, Update, Delete)
- ✅ Protección contra inyecciones SQL usando prepared statements
- ✅ Manejo de errores robusto
- ✅ Soporte para múltiples tipos de datos (string, integer, float)
- ✅ Configuración centralizada

🚀 Instalación

Requisitos
- PHP 7.4 o superior
- Extensión MySQLi habilitada
- Base de datos MySQL

Configuración

1. Configurar el archivo `config.php`:
```php
<?php

define('DB_HOST', 'localhost');        // Host de la base de datos
define('DB_USER', 'tu_usuario');       // Usuario de MySQL
define('DB_PASS', 'tu_contraseña');    // Contraseña de MySQL
define('DB_NAME', 'nombre_bd');        // Nombre de la base de datos
```

2. Incluir la clase en tu proyecto:
```php
require_once 'DB.php';
```

📖 Uso

Inicialización
```php
$db = new DB();
```

Operaciones CRUD

🔍 READ - Consultar datos
```php
// Consulta simple
$usuarios = $db->read("SELECT * FROM usuarios WHERE activo = 1");

// Consulta con parámetros (usar prepared statements)
$usuarios = $db->read("SELECT * FROM usuarios WHERE edad > ? AND ciudad = ?");
```

➕ CREATE - Insertar datos
```php
$nuevoUsuario = [
    'nombre' => 'Juan Pérez',
    'email' => 'juan@example.com',
    'edad' => 30,
    'activo' => 1
];

$id = $db->create('usuarios', $nuevoUsuario);

if ($id) {
    echo "Usuario creado con ID: " . $id;
}
```

✏️ UPDATE - Actualizar datos
```php
$datosActualizar = [
    'nombre' => 'Juan Carlos Pérez',
    'email' => 'juancarlos@example.com'
];

$exito = $db->update('usuarios', $datosActualizar, "id = 5");

if ($exito) {
    echo "Usuario actualizado correctamente";
}
```

🗑️ DELETE - Eliminar datos
```php
$exito = $db->delete('usuarios', "id = 10 AND activo = 0");

if ($exito) {
    echo "Usuario eliminado correctamente";
}
```

📚 Ejemplos Completos

Sistema de Usuarios
```php
<?php
require_once 'config.php';
require_once 'DB.php';

$db = new DB();

// Crear usuario
$usuarioData = [
    'nombre' => 'María García',
    'email' => 'maria@example.com',
    'password' => password_hash('123456', PASSWORD_DEFAULT),
    'fecha_creacion' => date('Y-m-d H:i:s')
];

$userId = $db->create('usuarios', $usuarioData);

// Leer usuarios
$usuarios = $db->read("SELECT id, nombre, email FROM usuarios WHERE activo = 1");
foreach ($usuarios as $usuario) {
    echo "Usuario: " . $usuario['nombre'] . " - " . $usuario['email'] . "\n";
}

// Actualizar usuario
$updateData = ['nombre' => 'María Elena García'];
$db->update('usuarios', $updateData, "id = $userId");

// Eliminar usuario
$db->delete('usuarios', "id = $userId AND activo = 0");

// Cerrar conexión
$db->close();
?>
```

Sistema de Productos
```php
<?php
require_once 'DB.php';

$db = new DB();

// Agregar producto
$producto = [
    'nombre' => 'Laptop Gaming',
    'precio' => 999.99,
    'stock' => 10,
    'categoria_id' => 2
];

$productoId = $db->create('productos', $producto);

// Consultar productos
$productos = $db->read("
    SELECT p.*, c.nombre as categoria 
    FROM productos p 
    LEFT JOIN categorias c ON p.categoria_id = c.id 
    WHERE p.stock > 0
");

// Actualizar stock
$db->update('productos', ['stock' => 5], "id = $productoId");

$db->close();
?>
```

⚙️ Métodos Disponibles

`__construct()`
- Establece conexión automática con la base de datos
- Configura charset UTF-8

`create($table, $data)`
- **Parámetros:**
  - `$table`: Nombre de la tabla
  - `$data`: Array asociativo con datos a insertar
- **Retorna:** ID del registro insertado o `false` en error

`read($query)`
- **Parámetros:**
  - `$query`: Consulta SQL
- **Retorna:** Array de resultados o `false` en error

`update($table, $data, $where)`
- **Parámetros:**
  - `$table`: Nombre de la tabla
  - `$data`: Array asociativo con datos a actualizar
  - `$where`: Condición WHERE
- **Retorna:** `true` en éxito, `false` en error

`delete($table, $where)`
- **Parámetros:**
  - `$table`: Nombre de la tabla
  - `$where`: Condición WHERE
- **Retorna:** `true` en éxito, `false` en error

`close()`
- Cierra la conexión a la base de datos

🛡️ Seguridad

- **Prepared Statements:** Todas las operaciones de escritura usan prepared statements
- **Validación de tipos:** Detección automática de tipos de datos (string, integer, float)
- **Manejo de errores:** Logging de errores sin exponer información sensible
- **Conexión segura:** Validación de conexión antes de operaciones

❗ Manejo de Errores

La clase incluye manejo robusto de errores:

```php
$resultado = $db->create('tabla', $datos);

if ($resultado === false) {
    // La operación falló
    echo "Error en la operación de base de datos";
    // Los detalles se registran en el log de errores
} else {
    // Operación exitosa
    echo "Operación completada: ID " . $resultado;
}
```

🔧 Mejores Prácticas

1. Siempre cierra la conexión:**
```php
$db = new DB();
// ... operaciones ...
$db->close();
```

2. Usa transacciones para operaciones múltiples:**
```php
$db->connection->begin_transaction();
try {
    $db->create('tabla1', $data1);
    $db->update('tabla2', $data2, $condition);
    $db->connection->commit();
} catch (Exception $e) {
    $db->connection->rollback();
}
```

3. Valida datos antes de insertar:**
```php
// Validar y sanitizar datos antes de usar la clase
$datosLimpios = [
    'nombre' => filter_var($nombre, FILTER_SANITIZE_STRING),
    'email' => filter_var($email, FILTER_VALIDATE_EMAIL),
    'edad' => filter_var($edad, FILTER_VALIDATE_INT)
];
```

🐛 Solución de Problemas

Error común: "Class 'DB' not found"
- Verifica que el archivo `DB.php` esté en la ruta correcta
- Confirma que `config.php` existe y tiene las constantes definidas

Error de conexión
- Verifica credenciales en `config.php`
- Confirma que el servidor MySQL esté ejecutándose
- Verifica permisos de usuario de base de datos

Problemas con caracteres especiales
- La clase configura automáticamente charset UTF-8
- Verifica que tu base de datos use collation UTF-8

📄 Licencia

Este proyecto está bajo la Licencia MIT.

🤝 Contribuciones

Las contribuciones son bienvenidas. Por favor:
1. Haz fork del proyecto
2. Crea una rama para tu feature
3. Commit tus cambios
4. Push a la rama
5. Abre un Pull Request



