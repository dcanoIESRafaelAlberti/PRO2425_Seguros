## **Guía de Implementación Paso a Paso**

1. **Crea los paquetes** (`model`, `data`, `service`, `ui`, `utils`).
2. **Implementa los interfaces, clases y enumeraciones en `model`**.
3. **Desarrolla los repositorios en `data`**, diferenciando memoria y ficheros.
4. **Crea los servicios en `service`**, aplicando inyección de dependencias.
5. **Diseña la UI en `ui`** con una implementación en consola.
6. **Gestiona los ficheros en `utils`** y usa la interfaz definida para mantener el código desacoplado.
7. **Implementa el `Main.kt`** para iniciar el programa, gestionar el menú y las dependencias.

***NOTA (20/03/2025 20:15)*** - Por ahora os dejo explicados y guiados casi al 100% los apartados 1, 2 y 6, a menos que se me ocurra algo más o vea algún problema que debemos solucionar.
Cuando tenga otro rato, terminaré mi versión y os subiré el resto de apartados, actualizando esta información.

***NOTA (23/03/2025 17:25)*** - Actualizados los apartados 2 y 3 (model y data). **OJO con las modificaciones en el apartado 2 (revisadlo)**, 

---

Aquí tenéis un desglose del proyecto con **indicaciones detalladas** sobre qué debe hacer cada paquete y cómo implementar cada clase.

### **1. Estructura del Proyecto (paquetes)**  
Debéis crear los siguientes paquetes:

- **📂 `app`** → Contendrá las clases encargadas de gestionar el flujo principal de la aplicación, como el menú y la navegación entre opciones.  
- **📂 `data`** → Maneja la persistencia de datos, con repositorios que almacenan información en memoria o en ficheros.  
- **📂 `model`** → Define la estructura de los datos, incluyendo clases, enumeraciones y estructuras necesarias para representar la información del sistema.  
- **📂 `service`** → Contiene la lógica de negocio, implementando la gestión de seguros y usuarios mediante la interacción con los repositorios.  
- **📂 `ui`** → Se encarga de la interacción con el usuario, implementando la interfaz en consola o cualquier otro medio de entrada/salida.  
- **📂 `utils`** → Agrupa herramientas y utilidades auxiliares, como gestión de ficheros y encriptación.  

**Con esta organización, el código será más modular, mantenible y escalable.**
   
### **2. `model` (Modelo de Datos)**
Este paquete contiene **todas las clases y enumeraciones** que definen los datos que maneja la aplicación.

#### **ENUMERACIONES**

##### **`Perfil`**: Define los roles de usuario.
  
   - Valores: `ADMIN, GESTION, CONSULTA`
  
      * `ADMIN`: Puede gestionar usuarios y seguros.
      * `GESTION`: Puede gestionar seguros, pero no crear/eliminar usuarios.
      * `CONSULTA`: Solo puede ver información.

   - Métodos estáticos: `getPerfil(valor: String): Cobertura` *(Retorna el valor de la enumeración correspondiente a la cadena de caracteres que se pasa por argumento o CONSULTA si no existe. Por ejemplo: getPerfil("gestion") retornaría GESTION)*

##### **`Cobertura`**: Indica el tipo de cobertura de los seguros de automóvil.
    
   - Valores: `TERCEROS, TERCEROS_AMPLIADO, FRANQUICIA_200, FRANQUICIA_300, FRANQUICIA_400, FRANQUICIA_500, TODO_RIESGO`  

   - Propiedades: `desc` *(Terceros, Terceros +, Todo Riesgo con Franquicia de 200€, ... , Todo Riesgo)*

   - Métodos estáticos: `getCobertura(valor: String): Cobertura` *(Retorna el valor de la enumeración correspondiente a la cadena de caracteres que se pasa por argumento o TERCEROS si no existe. Por ejemplo: getCobertura("terceros_ampliado") retornaría TERCEROS_AMPLIADO)*

##### **`Auto`**: Tipo de automóvil asegurado.

   - Valores: `COCHE, MOTO, CAMION`

   - Métodos estáticos: `getAuto(valor: String): Auto` *(Retorna el valor de la enumeración correspondiente a la cadena de caracteres que se pasa por argumento o COCHE si no existe. Por ejemplo: getAuto("moto") retornaría MOTO)*

##### **`Riesgo`**: Define los niveles de riesgo en los seguros de vida.

   - Valores: `BAJO, MEDIO, ALTO`

   - Propiedades: `interesAplicado` *(2.0, 5.0, 10.0)*.

   - Métodos estáticos: `getRiesgo(valor: String): Riesgo` *(Retorna el valor de la enumeración correspondiente a la cadena de caracteres que se pasa por argumento o MEDIO si no existe. Por ejemplo: getRiesgo("bajo") retornaría BAJO)*

#### **INTERFACES**

##### **`IExportable`**

- Contiene un único método `serializar(separador: String = ";"): String`

#### **CLASES**

##### **`Usuario`**

- Debe implementar un contrato como una clase de tipo `IExportable`.

- **Atributos:** `nombre`, `clave` y `perfil`. El nombre de usuario debe ser único. Todos los atributos serán públicos, excepto el `set` de `clave` que solo se podrá modificar dentro de la clase.

- **Propiedades y métodos estácticos:**
  - `crearUsuario(datos: List<String>): Usuario`: Retorna una instancia de `Usuario`. El parámetro que recibe, `datos`, contiene el valor de cada propiedad en orden y deben ser convertidos según el tipo de la propiedad si es necesario. Muy atentos a controlar su llamada para evitar EXCEPCIONES por conversiones erróneas *(aunque si almacenamos bien la info no debería ocurir, pero un buen programador/a lo controla SIEMPRE)*

- **Métodos:**
  - `cambiarClave(nuevaClaveEncriptada: String)`: Actualiza la clave del usuario *(este método va a actualizar la clave del usuario directamente, pero en el servicio que gestiona los usuarios debe solicitar la antigua clave, verificarla y pedir la nueva)*.

- **Métodos que sobreescribe:**
  - `serializar(separador: String): String`: Retornar una cadena de caracteres con los valores de los atributos de la clase separados por el valor indicado en `separador`.

##### **`Seguro`**

- Representa cualquier tipo de seguro. Será la clase base de `SeguroHogar`, `SeguroAuto` y `SeguroVida`.

- Debe implementar un contrato como una clase de tipo `IExportable`.

- **Atributos:** `numPoliza` *(pública y única por seguro)*, `dniTitular` *(solo accesible en su propia clase)*, `importe` *(solo accesible desde su propia clase y las clases que la extienden)*.

- **Métodos abstractos:**
  - `calcularImporteAnioSiguiente(interes: Double): Double`
  - `tipoSeguro(): String`

- **Métodos que sobreescribe:**
  - `serializar(): String`: Retornar una cadena de caracteres con los valores de los atributos de la clase separados por `;` *(por ejemplo: "100001;44027777K;327.40")*
  - `toString(): String`: Retornar la información del seguro con el siguiente formato *"Seguro(numPoliza=100001, dniTitular=44027777K, importe=327.40)"*. El `importe`siempre con dos posiciones decimales.
  - `hashCode(): Int`: Cómo `numPoliza`será único por cada seguro, retornar el valor de hashCode de un seguro en base solo a la dicha propiedad *(sin utilizar ningún número primo, ni más propiedades)*.
  - `equals(other: Any?): Boolean`: Utilizad igual que en el anterior método, solo la propiedad `numPoliza` para su comparación *(por supuesto, hacedlo bien, antes debéis realizar la comparación por referencia y verificar también si se trata de un `Seguro`)*

##### **CLASES QUE HEREDAN DE `Seguro`**

##### **`SeguroHogar`**

- **Atributos específicos:** `metrosCuadrados`, `valorContenido`, `direccion`, `anioConstruccion`. No serán accesibles desde fuera de la clase.

- **Constructores:** Esta clase no implementa un constructor primaro. En su lugar, tiene dos constructores secundarios, los cuales llaman al constructor de la **clase padre `Seguro`** con `super(...)`.
  - Primer constructor secundario: Lo usaremos en la Contratacíon de un **NUEVO** seguro *(genera un número de póliza automáticamente, gracias a una propiedad privada numPolizasAuto que comienza en el número 100000)*
  - Segundo constructor secundario: Lo usaremos para crear una póliza ya existente *(es decir, cuando recuperamos los seguros desde la persistencia de datos)*. Este segundo constructor no se podrá llamar desde fuera de la clase.

- **Propiedades y métodos estácticos:**
  - `numPolizasHogar: Int`: Nos ayuda a generar `numPoliza` de los nuevos seguros. No será accesible desde fuera de la clase.
  - `crearSeguro(datos: List<String>): SeguroHogar`: Retorna una instancia de `SeguroHogar`. El parámetro que recibe, `datos`, contiene el valor de cada propiedad en orden y deben ser convertidos según el tipo de la propiedad si es necesario. Muy atentos a controlar su llamada para evitar EXCEPCIONES por conversiones erróneas *(aunque si almacenamos bien la info no debería ocurir, pero un buen programador/a lo controla SIEMPRE)*
  - Yo crearía también dos constantes: PORCENTAJE_INCREMENTO_ANIOS = 0.02 y CICLO_ANIOS_INCREMENTO = 5.

- **Métodos que sobreescribe:**
  - `calcularImporteAnioSiguiente()`: Retornar el importe del año siguiente basándose en el interés que se pasa por parámetro, sumándole un interés residual de 0.02% por cada 5 años de antiguedad del hogar *(Ej: 4.77 años de antiguedad no incrementa, pero 23,07 sumará al interés el valor de 4 x 0.02 = 0.08)*.
  - `tipoSeguro(): String`: Retornar el nombre de la clase usando `this::class.simpleName` y el operador elvis para indicar al compilador que si `simpleName` es `null` *(cosa que nunca debe pasar, ya que la clase tiene un nombre)*, entonces deberá retornar el valor "Desconocido".
  - `serializar(): String`: Modificar el comportamiento de este método heredado, para retornar una cadena de caracteres con los valores de los atributos de la clase separados por `;`.
  - `toString(): String`: Retornar la información del seguro de hogar con el siguiente formato *"Seguro Hogar(numPoliza=100001, dniTitular=44027777K, importe=327.40, ...)"*. ¿Cómo lo podéis hacer si no tenéis accesible los atributos de la clase base `numPoliza` y `dniTitular`?

##### **`SeguroAuto`**

- **Atributos específicos:** `descripcion`, `combustible`, `tipoAuto`, `cobertura`, `asistenciaCarretera`, `numPartes`. No serán accesibles desde fuera de la clase.

- **Constructores:** Esta clase no implementa un constructor primaro. En su lugar, tiene dos constructores secundarios, los cuales llaman al constructor de la **clase padre `Seguro`** con `super(...)`.
  - Primer constructor secundario: Lo usaremos en la Contratacíon de un **NUEVO** seguro *(genera un número de póliza automáticamente, gracias a una propiedad privada numPolizasAuto que comienza en el número 400000)*
  - Segundo constructor secundario: Lo usaremos para crear una póliza ya existente. Este segundo constructor no se podrá llamar desde fuera de la clase.

- **Propiedades y métodos estácticos:**
  - `numPolizasAuto: Int`: Nos ayuda a generar `numPoliza` de los nuevos seguros. No será accesible desde fuera de la clase.
  - `crearSeguro(datos: List<String>): SeguroAuto`: Retorna una instancia de `SeguroAuto`. El parámetro que recibe, `datos`, contiene el valor de cada propiedad en orden y deben ser convertidos según el tipo de la propiedad si es necesario.
  - Yo crearía una constante PORCENTAJE_INCREMENTO_PARTES = 2.

- **Métodos que sobreescribe:**
  - `calcularImporteAnioSiguiente()`: Retornar el importe del año siguiente basándose en el interés que se pasa por parámetro, sumándole un interés residual del 2% por cada parte declarado.
  - `tipoSeguro(): String`: Retornar el nombre de la clase usando `this::class.simpleName` y el operador elvis para indicar al compilador que si `simpleName` es `null`, entonces deberá retornar el valor "Desconocido".
  - `serializar(): String`: Modificar el comportamiento de este método heredado, para retornar una cadena de caracteres con los valores de los atributos de la clase separados por `;`.
  - `toString(): String`: Retornar la información del seguro de auto con un formato similar al del seguro de hogar.

##### **`SeguroVida`**

- **Atributos específicos:** `fechaNac`, `nivelRiesgo`, `indemnizacion`. Usad el tipo de datos `LocalDate` para `fechaNac`. No serán accesibles desde fuera de la clase.

- **Constructores:** Esta clase no implementa un constructor primaro. En su lugar, tiene dos constructores secundarios, los cuales llaman al constructor de la **clase padre `Seguro`** con `super(...)`.
  - Primer constructor secundario: Lo usaremos en la Contratacíon de un **NUEVO** seguro *(genera un número de póliza automáticamente, gracias a una propiedad privada numPolizasAuto que comienza en el número 800000)*
  - Segundo constructor secundario: Lo usaremos para crear una póliza ya existente. Este segundo constructor no se podrá llamar desde fuera de la clase.

- **Propiedades y métodos estácticos:**
  - `numPolizasVida: Int`: Nos ayuda a generar `numPoliza` de los nuevos seguros. No será accesible desde fuera de la clase.
  - `crearSeguro(datos: List<String>): SeguroVida`: Retorna una instancia de `SeguroVida`. El parámetro que recibe, `datos`, contiene el valor de cada propiedad en orden y deben ser convertidos según el tipo de la propiedad si es necesario.

- **Métodos que sobreescribe:**
  - `calcularImporteAnioSiguiente()`: Retornar el importe del año siguiente basándose en el interés que se pasa por parámetro, sumándole un interés residual del 0.05% por cada año cumplido y el interés de su nivel de riesgo *(Ver clase enumerada `Riesgo`)*.
  - `tipoSeguro(): String`: Retornar el nombre de la clase usando `this::class.simpleName` y el operador elvis para indicar al compilador que si `simpleName` es `null`, entonces deberá retornar el valor "Desconocido".
  - `serializar(): String`: Modificar el comportamiento de este método heredado, para retornar una cadena de caracteres con los valores de los atributos de la clase separados por `;`.
  - `toString(): String`: Retornar la información del seguro de auto con un formato similar al del seguro de hogar.

##### **VALIDACIONES**

Las realizaremos todas en una clase que gestionará el menú de la app, fuera de las clases del modelo, para controlar la introducción de cada dato justo después de su introducción.

---

### **3. `data` (Repositorios y Persistencia)**

Este paquete será el encargado de almacenar y recuperar datos, tanto en memoria como desde archivos. Aquí gestionaremos todo lo relacionado con la persistencia de usuarios y seguros.

#### **Interfaces:**

##### `IRepoUsuarios`  
Define las operaciones básicas que deben poder realizarse con usuarios, como añadir, buscar, eliminar o listar.

- **¿Para qué sirve?**  
  Para acceder, modificar y consultar los usuarios del sistema.

- **¿Quién lo usará?**  
  Lo utilizará la clase `GestorUsuarios` (de `service`) para gestionar las operaciones de usuario.

- **¿Qué deberías implementar?**  
  Métodos como:
  - Agregar un usuario si no existe otro con el mismo nombre.
  - Buscar un usuario por su nombre.
  - Eliminarlo por nombre o por objeto.
  - Cambiar su clave.
  - Obtener todos los usuarios o filtrarlos por perfil.

```kotlin
interface IRepoUsuarios {
    fun agregar(usuario: Usuario): Boolean
    fun buscar(nombreUsuario: String): Usuario?
    fun eliminar(usuario: Usuario): Boolean
    fun eliminar(nombreUsuario: String): Boolean
    fun obtenerTodos(): List<Usuario>
    fun obtener(perfil: Perfil): List<Usuario>
    fun cambiarClave(usuario: Usuario, nuevaClave: String): Boolean
}
```

##### `IRepoSeguros`  
Define las operaciones con los seguros: añadir, buscar, eliminar y listar por tipo.

- **¿Para qué sirve?**  
  Para tener acceso a los seguros y sus datos durante la ejecución del programa.

- **¿Quién lo usará?**  
  Lo utilizará la clase `GestorSeguros` (de `service`).

- **¿Qué deberías implementar?**  
  - Añadir un seguro.
  - Buscarlo por número de póliza.
  - Eliminarlo por objeto o número.
  - Obtener todos o por tipo (`SeguroHogar`, `SeguroAuto`, `SeguroVida`).

```kotlin
interface IRepoSeguros {
    fun agregar(seguro: Seguro): Boolean
    fun buscar(numPoliza: Int): Seguro?
    fun eliminar(seguro: Seguro): Boolean
    fun eliminar(numPoliza: Int): Boolean
    fun obtenerTodos(): List<Seguro>
    fun obtener(tipoSeguro: String): List<Seguro>
}
```

##### `ICargarUsuariosIniciales`
Define la operación necesaria para cargar los usuarios desde el fichero de texto al iniciar la aplicación.

- **¿Para qué sirve?**  
  Para cargar los datos de usuarios desde un fichero al iniciar el programa, si se elige el modo de persistencia.

- **¿Quién lo usará?**  
  Será llamada desde `Main.kt` si el usuario elige trabajar en modo persistente.

- **¿Qué deberías hacer?**  
  Leer el fichero y crear objetos `Usuario` a partir de cada línea del fichero. Se debe comprobar que el fichero existe y tiene contenido.

```kotlin
interface ICargarUsuariosIniciales {
    fun cargarUsuarios(): Boolean
}
```

##### `ICargarSegurosIniciales`
Define la operación necesaria para cargar los seguros desde el fichero de texto al iniciar la aplicación.

- **¿Para qué sirve?**  
  Para cargar los seguros al inicio desde el fichero correspondiente.

- **¿Quién lo usará?**  
  También será usada en el `Main.kt`, cuando se cargan los datos desde almacenamiento.

- **¿Qué debes tener en cuenta?**  
  Cada línea del fichero de seguros indica el tipo de seguro al final de la línea. Usa ese dato para saber qué tipo de objeto seguro debes construir. Para eso, se proporciona un mapa que relaciona el tipo (`"SeguroHogar"`, `"SeguroAuto"`, etc.) con una función constructora.

```kotlin
interface ICargarSegurosIniciales {
    fun cargarSeguros(mapa: Map<String, (List<String>) -> Seguro>): Boolean
}
```

#### **Clases:**

##### `RepoUsuariosMem`
Esta clase implementa la interfaz `IRepoUsuarios` y almacena los usuarios en una lista mutable. Se utiliza en modo simulación *(sin persistencia)*, por lo que todos los cambios son temporales.

- **¿Qué hace?**  
  Gestiona los usuarios en una lista en memoria (no guarda en ficheros).

- **¿Cuándo se usa?**  
  Cuando el programa se ejecuta en modo "simulación".

- **¿Qué debes implementar?**  
  Los métodos definidos por `IRepoUsuarios`, usando una lista mutable interna.

- **¿Cómo hacer cada método?**

   * `agregar(usuario: Usuario): Boolean`
      Antes de añadirlo, comprueba si ya existe un usuario con el mismo nombre usando `buscar(...)`. Así evitaras duplicados. El nombre de usuario debe ser único.

   * `buscar(nombreUsuario: String): Usuario?`
      Utiliza la función `find` sobre la lista.

   * `eliminar(usuario: Usuario): Boolean`
      Usa `remove(...)` sobre la lista.

   * `eliminar(nombreUsuario: String): Boolean`
      Llama a `buscar(...)` y, si existe, usa la función anterior. Por si es necesario eliminar usuarios indicando su nombre.

   * `obtenerTodos(): List<Usuario>`
      Simplemente retorna la lista.

   * `obtener(perfil: Perfil): List<Usuario>`
      Usa `filter(...)` para obtener usuarios según su perfil *(Admin, Gestión, Consulta)*.

   * `cambiarClave(usuario: Usuario, nuevaClave: String): Boolean`
      Llama a `cambiarClave(...)` del usuario.

##### `RepoUsuariosFich`
Esta clase extiende `RepoUsuariosMem`, por lo que reutiliza toda la lógica de gestión en memoria, pero **añade persistencia en fichero** usando un objeto de tipo `IUtilFicheros`.  

Recibirá como argumentos de entrada la ruta del archivo (String) y una instancia del tipo IUtilFicheros *(aquí estamos usando DIP)*.

- **¿Qué hace?**  
  Hereda de `RepoUsuariosMem`, pero además **escribe y lee en un archivo `.txt`**. También implementa el contrato con `ICargarUsuariosIniciales`.

- **¿Cuándo se usa?**  
  Cuando el programa se ejecuta en modo persistente.

- **¿Qué responsabilidades tiene?**
  - Añadir y eliminar usuarios también en el fichero, es decir, que sobrrescribe los métodos agregar y eliminar. Os pongo un ejemplo:

    ```kotlin
    override fun eliminar(usuario: Usuario): Boolean {
        if (fich.escribirArchivo(rutaArchivo, usuarios.filter { it != usuario })) {
            return super.eliminar(usuario)
        }
        return false
    }
    ```

  - Actualizar el fichero si se cambia la clave de un usuario.
  - Cargar usuarios al inicio si existe el fichero *(ICargarSegurosIniciales)*

- **¿Cómo hacer cada método?**

   * `agregar(usuario: Usuario): Boolean` *(Debe mantener sincronizados el fichero y la lista de usuarios)*
      1. Comprueba que no existe ya, si es así retorna false.
      2. Si no existe, lo guarda en el fichero *(usa `agregarLinea`)*.
      3. Solo si el guardado en fichero es exitoso, lo añade a la lista en memoria.

   * `eliminar(usuario: Usuario): Boolean` *(Debe evitar inconsistencias entre memoria y almacenamiento persistente)*
      1. Filtra la lista para excluir al usuario eliminado.
      2. Sobrescribe el fichero con el contenido actualizado *(`escribirArchivo`)*.
      3. Si la escritura fue correcta, elimina el usuario de la lista.

   * `cargarUsuarios(): Boolean` *(Es imprescindible para tener en memoria los usuarios guardados en ejecuciones anteriores)*
      1. Lee el archivo línea a línea.
      2. Divide cada línea por `;` para obtener los campos.
      3. Usa la función `Usuario.crearUsuario(datos)` para crear la instancia.
      4. Añade los usuarios a la lista.

##### `RepoSegurosMem`
Esta clase implementa la interfaz `IRepoSeguros` y almacena los seguros en una lista mutable. Se utiliza en modo simulación *(sin persistencia)*, por lo que todos los cambios son temporales.

- **¿Qué hace?**  
  Gestiona seguros usando una lista en memoria.

- **¿Cuándo se usa?**  
  Igual que `RepoUsuariosMem`, en modo simulación.

- **¿Qué responsabilidades tiene?**  
  Implementar los métodos definidos por `IRepoSeguros`.

- **¿Cómo hacer cada método?**

   * `agregar(seguro: Seguro): Boolean`
      Llama a `seguros.add(seguro)`.

   * `buscar(numPoliza: Int): Seguro?`
      Recorre la lista y devuelve el primer seguro que cumpla la condición usando `find { ... }`.

   * `eliminar(seguro: Seguro): Boolean`
      Llama a `seguros.remove(seguro)`.

   * `eliminar(numPoliza: Int): Boolean`
      1. Llama a `buscar(numPoliza)` para encontrar el seguro.
      2. Si lo encuentra, llama al método `eliminar(seguro)`.

   * `obtenerTodos(): List<Seguro>`
      Retorna directamente la lista `seguros`.

   * `obtener(tipoSeguro: String): List<Seguro>`
      Usa `filter` comparando con `tipoSeguro() de cada seguro`.

##### `RepoSegurosFich`
Esta clase hereda de `RepoSegurosMem`, pero se encarga también de guardar los datos en fichero de forma persistente. Implementa la interfaz `ICargarSegurosIniciales`.

- **¿Qué hace?**  
  Extiende `RepoSegurosMem` y añade escritura y lectura de seguros en un fichero.

- **¿Qué añade respecto a `RepoSegurosMem`?**
  - Guarda cada seguro en el archivo cada vez que se agrega.
  - Elimina del fichero cuando se borra un seguro.
  - Permite cargar los seguros al inicio del programa (`ICargarSegurosIniciales`).
  - **Importante:** al cargar los seguros, también debe actualizar los contadores de cada tipo *(`SeguroHogar`, `SeguroAuto`, etc.)*, para no generar duplicados en los números de póliza.

- **¿Cómo hacer cada método?**

   * `agregar(seguro: Seguro): Boolean`
      1. Llama a `fich.agregarLinea(...)` para añadirlo al fichero.
      2. Si se guarda correctamente, llama a `super.agregar(...)`.

   * `eliminar(seguro: Seguro): Boolean`
      1. Genera una nueva lista sin el seguro a eliminar.
      2. Llama a `fich.escribirArchivo(...)` con la nueva lista.
      3. Si se actualiza el fichero, llama a `super.eliminar(...)`.

   * `cargarSeguros(mapa: Map<String, (List<String>) -> Seguro>): Boolean`
      1. Usa `fich.leerSeguros(...)`, que recorre el fichero línea por línea.
      2. Cada línea se transforma en un seguro usando el mapa de funciones de creación por tipo.
      3. Se actualiza la lista `seguros` y se llama a `actualizarContadores(...)`.

   * `actualizarContadores(seguros: List<Seguro>)`
      1. Filtra los seguros por tipo usando `tipoSeguro()`.
      2. Calcula el mayor número de póliza de cada tipo.
      3. Asigna ese valor al contador correspondiente del `companion object` de cada clase.
      4. Es esencial para que no se generen números de póliza repetidos al contratar nuevos seguros. Este último método os lo proporciono yo, para que lo uséis en `cargarSeguros`.

   ```kotlin
    private fun actualizarContadores(seguros: List<Seguro>) {
        // Actualizar los contadores de polizas del companion object según el tipo de seguro
        val maxHogar = seguros.filter { it.tipoSeguro() == "SeguroHogar" }.maxOfOrNull { it.numPoliza }
        val maxAuto = seguros.filter { it.tipoSeguro() == "SeguroAuto" }.maxOfOrNull { it.numPoliza }
        val maxVida = seguros.filter { it.tipoSeguro() == "SeguroVida" }.maxOfOrNull { it.numPoliza }

        if (maxHogar != null) SeguroHogar.numPolizasHogar = maxHogar
        if (maxAuto != null) SeguroAuto.numPolizasAuto = maxAuto
        if (maxVida != null) SeguroVida.numPolizasVida = maxVida
    }
   ```

##### Recomendaciones finales

- Usa clases abiertas (`open class`) cuando vayas a heredarlas.
- Separa bien la lógica en memoria y la lógica de ficheros.
- Usa `serializar()` y `crearXxx()` en cada tipo de seguro para guardar y leer los datos fácilmente.
- No mezcles la lógica de presentación ni la de negocio dentro de estos repositorios.

---

### **4. `service` (Lógica de Negocio)**
Aquí se implementan las **operaciones principales** que la interfaz de usuario ejecutará.

#### **Interfaces (`IServUsuarios`, `IServSeguros`)**

```kotlin
interface IServSeguros {
}
```

```kotlin
interface IServUsuarios {
}
```

#### **Servicios (`GestorUsuarios`, `GestorSeguros`)**
- Implementan las interfaces y usan los repositorios.
- `GestorUsuarios` maneja la autenticación, creación de nuevos usuarios y cambios de contraseña.
- `GestorSeguros` se encarga de contratar, listar y eliminar seguros.

##### **GestorUsuarios:**

##### **GestorSeguros:**

---

### **5. `ui` (Interfaz de Usuario)**
Este paquete maneja **cómo interactúa el usuario** con el sistema.

#### **Interfaz `IUserInterface`**
- Define métodos como `mostrar(mensaje: String)`, etc.

#### **`Consola`** (Implementación de `IUserInterface`)
- Imprime mensajes en la terminal y recibe entradas del usuario.

---

### **6. `utils` (Utilidades)**
Contiene herramientas para operaciones repetitivas.

#### **Interfaz `IUtilFicheros`**
- Define métodos de lectura y escritura en archivos.

```kotlin
interface IUtilFicheros {
    fun leerArchivo(ruta: String): List<String>
    fun leerSeguros(ruta: String, mapaSeguros: Map<String, (List<String>) -> Seguro>): List<Seguro>
    fun agregarLinea(ruta: String, linea: String): Boolean
    fun <T: IExportable> escribirArchivo(ruta: String, elementos: List<T>): Boolean
    fun existeFichero(ruta: String): Boolean
    fun existeDirectorio(ruta: String): Boolean
}
```

#### **Clase `Ficheros`**
- Implementa `IUtilFicheros` y maneja el acceso a los `.txt`.

#### **Interfaz `IUtilSeguridad`**
- Define métodos para encriptar y verificar claves.

```kotlin
interface IUtilSeguridad {
    fun encriptarClave(clave: String, nivelSeguridad: Int = 12): String
    fun verificarClave(claveIngresada: String, hashAlmacenado: String): Boolean
}
```

#### **Clase `Seguridad`

- Incluir la implementación de la librería externa BCrypt en el fichero `build.gradle`:

```kotlin
dependencies {
    testImplementation(kotlin("test"))
    implementation("at.favre.lib:bcrypt:0.9.0")
}
```

- Contenido de la clase `Seguridad`:

```kotlin
import at.favre.lib.crypto.bcrypt.BCrypt

class Seguridad : IUtilSeguridad {

    /**
     * Genera un hash seguro de la clave utilizando el algoritmo BCrypt.
     *
     * BCrypt es un algoritmo de hashing adaptativo que permite configurar un nivel de seguridad (coste computacional),
     * lo que lo hace ideal para almacenar contraseñas de forma segura.
     *
     * @param clave La contraseña en texto plano que se va a encriptar.
     * @param nivelSeguridad El factor de coste utilizado en el algoritmo BCrypt. Valores más altos aumentan la seguridad
     * pero también el tiempo de procesamiento. El valor predeterminado es `12`, que se considera seguro para la mayoría
     * de los casos.
     * @return El hash de la clave en formato String, que puede ser almacenado de forma segura.
     */
    override fun encriptarClave(clave: String, nivelSeguridad: Int = 12): String {
        return BCrypt.withDefaults().hashToString(nivelSeguridad, clave.toCharArray())
    }

    /**
     * Verifica si una contraseña ingresada coincide con un hash almacenado previamente usando BCrypt.
     *
     * Esta función permite autenticar a un usuario comprobando si la clave ingresada,
     * tras ser procesada con BCrypt, coincide con el hash almacenado en la base de datos.
     *
     * @param claveIngresada La contraseña en texto plano que se desea comprobar.
     * @param hashAlmacenado El hash BCrypt previamente generado contra el que se verificará la clave ingresada.
     * @return `true` si la clave ingresada coincide con el hash almacenado, `false` en caso contrario.
     */
    override fun verificarClave(claveIngresada: String, hashAlmacenado: String): Boolean {
        return BCrypt.verifyer().verify(claveIngresada.toCharArray(), hashAlmacenado).verified
    }

}
```

---

### **7. `Main.kt` (Punto de Entrada)**
- Inicializa repositorios y servicios.
- Pide credenciales o permite crear un `ADMIN` si no hay usuarios.
- Carga el **menú principal** para gestionar usuarios y seguros.



