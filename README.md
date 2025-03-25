# PRO2425_Seguros

**Práctica: Gestión de Seguros con Ficheros TXT**

## [Guía de Implementación Paso a Paso](Guia.md) ***(Última actualización 20/03/2025 20:15)***

## 1. Introducción

En esta práctica vamos a construir una aplicación en **Kotlin** donde aplicaremos **POO (Programación Orientada a Objetos)**, el **principio SOLID**, y trabajaremos con **ficheros** para almacenar información de manera persistente.

La aplicación servirá para gestionar **seguros** de diferentes tipos y también manejará la **autenticación de usuarios**. Vamos a dividir el código en **paquetes organizados**, cada uno con una función específica para que el proyecto esté bien estructurado y sea fácil de mantener.

- POO y principios SOLID.
- Almacenamiento en ficheros TXT con datos separados por ;.
- Gestión de usuarios con claves encriptadas (BCrypt).
- Carga y validación de clientes al registrar seguros y alquileres.
- Modo de ejecución: SIMULACIÓN (memoria) o ALMACENAMIENTO (persistencia en ficheros).

## 2. Ficheros de Datos TXT

Se utilizarán dos archivos TXT, cada uno con datos separados por `;`. Todos los seguros se almacenarán en un único archivo Seguros.txt, cada línea representa un seguro y el último campo indica el tipo de seguro (SeguroHogar, SeguroAuto, SeguroVida) que coincidirá con el nombre de las clases internas creadas en el programa.

📂 Usuarios.txt (contiene usuarios con contraseña encriptada)

```
usuario;clave_encriptada;perfil
```

- Clave encriptada con BCrypt.
- Perfiles: admin, gestion, consulta.
- Al ejecutar el programa se comprobará si el fichero está vacío, entonces preguntará si se desea crear un usuario admin.
- Pedirá el nombre de usuario y la contraseña del usuario admin que se va a crear.
- Si responde negativamente, el programa finalizará y no se podrá continuar.

📂 Seguros.txt (almacena seguros contratados)

```
id;dniTitular;numPoliza;importe;[datos específicos];tipoSeguro
```

Ejemplo de Seguro de Hogar:

```
100001;12345678A;500.0;80;150000;Calle Mayor, 12;SeguroHogar
```

Ejemplo de Seguro de Auto:

```
400001;98765432B;700.0;"Toyota Corolla Azul";Gasolina;Turismo;Todo Riesgo;true;1;SeguroAuto
```

Ejemplo de Seguro de Vida:

```
800001;87654321C;300.0;1985-05-12;Medio;100000;SeguroVida
```

## 3. Jerarquía de Clases

La aplicación sigue una estructura basada en herencia, asegurando una organización clara y extensible.

📌 Clases Principales

1. Seguro (Base de todos los seguros, no se puede instanciar)
2. SeguroHogar
3. SeguroAuto
4. SeguroVida

📌 Propiedades y Métodos

- Clase Seguro (Abstracta)
    * `numPoliza`: Int
    * `dniTitular`: String
    * `importe`: Double (privado)
    * Métodos abstractos:
        - `calcularImporteAnioSiguiente(interes: Double): Double`
        - `tipoSeguro(): String`
        - `serializar(): String` (Convierte el objeto a formato TXT)

- Clase SeguroHogar
    * `metrosCuadrados`: Int
    * `valorContenido`: Double
    * `direccion`: String
    * `calcularImporteAnioSiguiente(interes: Double)`: Double (Aplica el porcentaje proporcionado al importe para generar la predicción del importe del siguiente año).

Ejemplo de serializar():

```
100001;12345678A;500.0;80;150000;Calle Mayor, 12;SeguroHogar
```

- Clase SeguroAuto
    * `descripcion`: String (Ejemplo: "Toyota Corolla Azul")
    * `combustible`: String (Gasolina, Diésel, Eléctrico, Híbrido)
    * `tipoAuto`: Enumerado (Coche, Moto, Camion)
    * `tipoCobertura`: String (Terceros, Todo Riesgo, etc.)
    * `asistenciaCarretera`: Boolean
    * `numPartes`: Int
    * `calcularImporteAnioSiguiente(interes: Double)`: Aumenta un 2% por cada parte el interés pasado como argumento, si hubo partes, sino solo usa el interés dado.

Ejemplo de serializar():

```
400001;98765432B;700.0;"Toyota Corolla Azul";Gasolina;Turismo;Todo Riesgo;true;1;SeguroAuto
```

- Clase SeguroVida
    * `fechaNac`: String
    * `nivelRiesgo`: Enumerado (Bajo, Medio, Alto)
    * `indemnizacion`: Double
    * `calcularImporteAnioSiguiente(interes: Double)`: Aumenta según el nivel de riesgo (Bajo 2%, Medio 5%, Alto 10%).

Ejemplo de serializar():

```
800001;87654321C;300.0;1985-05-12;Medio;100000;SeguroVida
```

## 4. Generación del id de los seguros

Los ids de los seguros se generarán automáticamente:

- Seguros de Hogar → desde 100000.
- Seguros de Auto → desde 400000.
- Seguros de Vida → desde 800000.

## 5. Validación de Datos

- Cada campo será validado antes de continuar.
- Métodos estáticos en Seguro y Alquiler manejarán las validaciones.

Ejemplo:

```
class Seguro {
    companion object {
        fun validarDni(dni: String): Boolean {
            return dni.matches(Regex("^[0-9]{8}[A-Z]$"))
        }
    }
}
```

Si el usuario ingresa un dato incorrecto:

DNI inválido. Inténtelo nuevamente o escriba "CANCELAR" para salir.

## 6. Menús y Permisos

Los usuarios verán opciones según su perfil.

📌 Menú de admin
```
1. Usuarios
    1. Nuevo
    2. Eliminar
    3. Cambiar contraseña
    4. Consultar
    5. Volver
2. Seguros
    1. Contratar
        1. Hogar
        2. Auto
        3. Vida
        4. Volver
    2. Eliminar
    3. Consultar
        1. Todos
        2. Hogar
        3. Auto
        4. Vida
        5. Volver
3. Salir
```

📌 Menú de gestión (Accede a todos los seguros pero no puede gestionar usuarios)
```
1. Seguros
    1. Contratar
        1. Hogar
        2. Auto
        3. Vida
        4. Volver
    2. Eliminar
    3. Consultar
        1. Todos
        2. Hogar
        3. Auto
        4. Vida
        5. Volver
2. Salir
```

📌 Menú de consulta (Accede solo a la consulta de seguros)
```
1. Seguros
    1. Consultar
        1. Todos
        2. Hogar
        3. Auto
        4. Vida
        5. Volver
2. Salir
```

## 7. Modo de Ejecución

Al iniciar, después de validar al usuario, se realiza la pregunta:

```
Seleccione el modo de ejecución:
1. SIMULACIÓN (solo en memoria)
2. ALMACENAMIENTO (usar ficheros)
```

* SIMULACIÓN: Todos los datos se manejan en memoria.
* ALMACENAMIENTO: Se guardan y cargan desde Seguros.txt.

## 8. Mapa de Creación de Seguros

El mapa de funciones se usará para instanciar dinámicamente los seguros cuando se carguen desde el fichero Seguros.txt.

```kotlin
val mapaSeguros: Map<String, (List<String>) -> Seguro> = mapOf(
    "SeguroHogar" to { datos ->
        SeguroHogar(
            datos[0].toInt(), datos[1], datos[2].toInt(), datos[3].toDouble(),
            datos[4].toInt(), datos[5].toDouble(), datos[6]
        )
    },
    "SeguroAuto" to { datos ->
        SeguroAuto(
            datos[0].toInt(), datos[1], datos[2].toInt(), datos[3].toDouble(),
            datos[4], datos[5], datos[6], datos[7], datos[8].toBoolean(), datos[9].toInt()
        )
    },
    "SeguroVida" to { datos ->
        SeguroVida(
            datos[0].toInt(), datos[1], datos[2].toInt(), datos[3].toDouble(),
            datos[4], datos[5], datos[6].toDouble()
        )
    }
)
```

## 9. RepositorioSegurosFicheros (Lectura y Escritura de Seguros en Fichero)

```kotlin
class RepositorioSegurosFicheros(private val archivo: String, private val mapaSeguros: Map<String, (List<String>) -> Seguro>) {

    // Guardar un seguro en el fichero
    fun guardarSeguro(seguro: Seguro) {
        File(archivo).appendText(seguro.serializar() + "\n")
    }

    // Cargar todos los seguros del fichero
    fun cargarSeguros(): List<Seguro> {
        val seguros = mutableListOf<Seguro>()
        val file = File(archivo)

        if (!file.exists()) return seguros

        file.forEachLine { linea ->
            val datos = linea.split(";")
            val tipoSeguro = datos.last() // El último campo indica el tipo de seguro

            val seguro = mapaSeguros[tipoSeguro]?.invoke(datos.dropLast(1)) // Pasamos la lista de datos SIN el tipoSeguro
            if (seguro != null) {
                seguros.add(seguro)
            }
        }
        return seguros
    }
}
```


## 10. main() con inicialización del repositorio

```
fun main() {
    val archivo = "Seguros.txt"
    val repo = RepositorioSegurosFicheros(archivo, mapaSeguros)

    // Crear seguros y guardarlos
    val seguroHogar = SeguroHogar(1, "12345678A", 101, 500.0, 80, 150000.0, "Calle Mayor, 12")
    val seguroAuto = SeguroAuto(2, "98765432B", 102, 700.0, "Toyota Corolla Azul", "Gasolina", "Turismo", "Todo Riesgo", true, 1)

    repo.guardarSeguro(seguroHogar)
    repo.guardarSeguro(seguroAuto)

    // Cargar seguros desde el fichero
    val segurosCargados = repo.cargarSeguros()
    segurosCargados.forEach { println(it.tipoSeguro() + ": " + it.serializar()) }
}
```

