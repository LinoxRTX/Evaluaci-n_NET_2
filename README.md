Por supuesto, aquí te dejo la documentación con los errores indicados claramente separados con bloques de código para que puedas pegar o agregar capturas fácilmente, y con espacios reservados para imágenes o códigos corregidos:

---

# Documentación del Proyecto: Web API RESTful para Biblioteca Municipal “Letras Libres”

## 1. Contexto Inicial

La Biblioteca Municipal “Letras Libres” utiliza actualmente un sistema manual basado en hojas de cálculo para la gestión de libros, usuarios y préstamos. Esto ocasiona ineficiencias, falta de escalabilidad y riesgo de pérdida o inconsistencia de datos. Por esta razón, se contrató a nuestro equipo para desarrollar una Web API RESTful usando ASP.NET Core y Entity Framework Core que permita digitalizar y modernizar estos procesos, sentando las bases para futuras aplicaciones móviles y web.

---

## 2. Necesidades y Requerimientos

El sistema debe cubrir los siguientes requisitos funcionales:

* Gestión completa de **Libros** (CRUD).
* Gestión de **Usuarios** (registro y consulta).
* Registro y control de **Préstamos** y **Devoluciones**.
* Uso obligatorio de Entity Framework Core para persistencia con SQL Server.
* Correcta modelación de las relaciones entre entidades (uno-a-muchos).
* Validaciones para garantizar integridad y evitar datos inválidos.
* Documentación y prueba de endpoints mediante Swagger.
* Control de errores con respuestas adecuadas.
* Capacidad para futuras extensiones y mantenibilidad.

---

## 3. Errores Encontrados y Descripción

### 3.1 En los Modelos

#### Libro.cs — Falta validación obligatoria en `Titulo`

```csharp
public class Libro
{
    public int Id { get; set; }

    // ❌ ERROR: Falta validación obligatoria
    public string Titulo { get; set; }

    public string Autor { get; set; }

    public bool EstaPrestado { get; set; }

    public ICollection<Prestamo> Prestamos { get; set; }
}
```

> **Espacio para agregar captura o código corregido con \[Required]**

---

#### Usuario.cs — Falta validación obligatoria en `Nombre`

```csharp
public class Usuario
{
    public int Id { get; set; }

    // ❌ ERROR: Campo obligatorio sin validación
    public string Nombre { get; set; }

    public ICollection<Prestamo> Prestamos { get; set; }
}
```

> **Espacio para agregar captura o código corregido con \[Required]**

---

#### Prestamo.cs — Falta validación obligatoria en `FechaPrestamo`

```csharp
public class Prestamo
{
    public int Id { get; set; }

    public int LibroId { get; set; }

    public Libro Libro { get; set; }

    public int UsuarioId { get; set; }

    public Usuario Usuario { get; set; }

    // ❌ ERROR: Campo obligatorio sin validación
    public DateTime FechaPrestamo { get; set; }

    public DateTime? FechaDevolucion { get; set; }
}
```

> **Espacio para agregar captura o código corregido con \[Required]**

---

### 3.2 En los Controladores

#### LibrosController.cs — No valida que el título esté vacío en POST

```csharp
[HttpPost]
public async Task<ActionResult<Libro>> PostLibro(Libro libro)
{
    // ❌ ERROR: No se valida si el título está vacío
    _context.Libros.Add(libro);
    await _context.SaveChangesAsync();
    return CreatedAtAction(nameof(GetLibro), new { id = libro.Id }, libro);
}
```

> **Espacio para agregar captura o código corregido con validación y respuesta 400**

---

#### LibrosController.cs — Permite eliminar libro aunque esté prestado

```csharp
[HttpDelete("{id}")]
public async Task<IActionResult> DeleteLibro(int id)
{
    var libro = await _context.Libros.FindAsync(id);
    if (libro == null)
        return NotFound();

    // ❌ ERROR: Se permite eliminar aunque esté prestado
    _context.Libros.Remove(libro);
    await _context.SaveChangesAsync();
    return NoContent();
}
```

> **Espacio para agregar captura o código corregido con validación que impida eliminar si está prestado**

---

#### PrestamosController.cs — No verifica existencia de libro o usuario en POST préstamo

```csharp
[HttpPost]
public async Task<IActionResult> Post(Prestamo prestamo)
{
    // ❌ ERROR INTENCIONAL: No se verifica existencia de libro o usuario
    _context.Prestamos.Add(prestamo);
    await _context.SaveChangesAsync();

    return Ok(prestamo);
}
```

> **Espacio para agregar captura o código corregido con validación de existencia y reglas de negocio**

---

#### UsuariosController.cs — No valida que el nombre sea obligatorio en POST

```csharp
[HttpPost]
public async Task<IActionResult> Post(Usuario usuario)
{
    _context.Usuarios.Add(usuario);
    await _context.SaveChangesAsync();
    return CreatedAtAction(nameof(Get), new { id = usuario.Id }, usuario);
}
```

> **Espacio para agregar captura o código corregido con validación y respuesta adecuada**

---

## 4. Corrección de Errores

Para cada error detectado se aplicaron las siguientes medidas:

* **Validaciones en Modelos:** Se agregaron atributos `[Required]` y otras validaciones a campos obligatorios como `Titulo`, `Nombre` y `FechaPrestamo`.
* **Validaciones en Controladores:** Se incorporaron chequeos para validar entradas, existencia de entidades relacionadas y reglas de negocio antes de operaciones críticas (ej. impedir eliminar libros prestados).
* **Manejo de Estados de Préstamo:** Se agregó lógica para evitar prestar un libro ya prestado y para validar condiciones en devoluciones.
* **Respuesta Adecuada:** Se mejoró el manejo de respuestas HTTP, retornando códigos 400, 404 o 409 según el caso, con mensajes claros.
* **Uso de Include en Consultas:** Para obtener datos relacionados en consultas como el historial de préstamos.

> **Agregar captura/código actualizado para cada corrección.**

---

## 5. Conclusión

Se ha desarrollado una Web API RESTful que cumple con los requisitos funcionales definidos, utilizando ASP.NET Core y Entity Framework Core para la persistencia en SQL Server. El proyecto inicialmente contenía errores comunes relacionados con validaciones insuficientes y reglas de negocio no implementadas, lo que podría provocar inconsistencias y datos inválidos.

Mediante la identificación y corrección de estos errores, el sistema garantiza la integridad y consistencia de la información, mejora la experiencia de usuario y establece una base sólida para futuras ampliaciones. La documentación con Swagger facilita pruebas y mantenimiento.

La entrega incluye el código fuente con comentarios sobre errores intencionales para fines educativos y un plan claro de corrección, aportando un valioso recurso para la comprensión y mejora continua del sistema.

---

¿Quieres que te prepare este documento en PDF o Word listo para entregar?
