## **Guía de Implementación Paso a Paso**

1. **Crea los paquetes** (`model`, `data`, `service`, `ui`, `utils`).
2. **Implementa los interfaces, clases y enumeraciones en `model`**.
3. **Desarrolla los repositorios en `data`**, diferenciando memoria y ficheros.
4. **Crea los servicios en `service`**, aplicando inyección de dependencias.
5. **Diseña la UI en `ui`** con una implementación en consola.
6. **Gestiona los ficheros en `utils`** y usa la interfaz definida para mantener el código desacoplado.
7. **Desarrolla las clases que controlan el flujo de la aplicación en `app`**.
8. **Implementa el `Main.kt`** para iniciar el programa, gestionar el menú y las dependencias.

***NOTA (20/03/2025 20:15)*** - Por ahora os dejo explicados y guiados casi al 100% los apartados 1, 2 y 6, a menos que se me ocurra algo más o vea algún problema que debemos solucionar.
Cuando tenga otro rato, terminaré mi versión y os subiré el resto de apartados, actualizando esta información.

***NOTA (23/03/2025 17:25)*** - Actualizados los apartados 2 y 3 (model y data). **OJO con las modificaciones en el apartado 2 (revisadlo)**.

***NOTA (24/03/2025 10:15)*** - Actualizados los apartados 4 y 5 (service y ui).

***NOTA (25/03/2025 12:15)*** - Actualizados los apartados 7 y 8 (app y Main).

***NOTA (26/03/2025 22:30)*** - Modificación en las clase `Seguro` y las clases que la extienden *(el método tipoSeguro se resuelve solo en la clase Seguro)*. Actualización del apartado 7 para incluir una nueva clase `CargadorInicial`. **De todas formas este paquete y el Main lo vamos a trabajar en clase la semana próxima.**

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

- **Métodos que sobreescribe:**
  - `serializar(): String`: Retornar una cadena de caracteres con los valores de los atributos de la clase separados por `;` *(por ejemplo: "100001;44027777K;327.40")*
  - `toString(): String`: Retornar la información del seguro con el siguiente formato *"Seguro(numPoliza=100001, dniTitular=44027777K, importe=327.40)"*. El `importe`siempre con dos posiciones decimales.
  - `hashCode(): Int`: Cómo `numPoliza`será único por cada seguro, retornar el valor de hashCode de un seguro en base solo a la dicha propiedad *(sin utilizar ningún número primo, ni más propiedades)*.
  - `equals(other: Any?): Boolean`: Utilizad igual que en el anterior método, solo la propiedad `numPoliza` para su comparación *(por supuesto, hacedlo bien, antes debéis realizar la comparación por referencia y verificar también si se trata de un `Seguro`)*

- **Métodos implementados:**
  - `tipoSeguro(): String`: Retornar el nombre de la clase usando `this::class.simpleName` y el operador elvis para indicar al compilador que si `simpleName` es `null` *(cosa que nunca debe pasar, ya que la clase tiene un nombre)*, entonces deberá retornar el valor "Desconocido".

##### **CLASES QUE HEREDAN DE `Seguro`**

##### **`SeguroHogar`**

- **Atributos específicos:** `metrosCuadrados`, `valorContenido`, `direccion`, `anioConstruccion`. No serán accesibles desde fuera de la clase.

- **Constructores:** Esta clase no implementa un constructor primaro. En su lugar, tiene dos constructores secundarios, los cuales llaman al constructor de la **clase padre `Seguro`** con `super(...)`.
  - Primer constructor secundario: Lo usaremos en la Contratacíon de un **NUEVO** seguro *(genera un número de póliza automáticamente, gracias a una propiedad privada numPolizasHogar que comienza en el número 100000)*
  - Segundo constructor secundario: Lo usaremos para crear una póliza ya existente *(es decir, cuando recuperamos los seguros desde la persistencia de datos)*. Este segundo constructor no se podrá llamar desde fuera de la clase.

- **Propiedades y métodos estácticos:**
  - `numPolizasHogar: Int`: Nos ayuda a generar `numPoliza` de los nuevos seguros. No será accesible desde fuera de la clase.
  - `crearSeguro(datos: List<String>): SeguroHogar`: Retorna una instancia de `SeguroHogar`. El parámetro que recibe, `datos`, contiene el valor de cada propiedad en orden y deben ser convertidos según el tipo de la propiedad si es necesario. Muy atentos a controlar su llamada para evitar EXCEPCIONES por conversiones erróneas *(aunque si almacenamos bien la info no debería ocurir, pero un buen programador/a lo controla SIEMPRE)*
  - Yo crearía también dos constantes: PORCENTAJE_INCREMENTO_ANIOS = 0.02 y CICLO_ANIOS_INCREMENTO = 5.

- **Métodos que sobreescribe:**
  - `calcularImporteAnioSiguiente()`: Retornar el importe del año siguiente basándose en el interés que se pasa por parámetro, sumándole un interés residual de 0.02% por cada 5 años de antiguedad del hogar *(Ej: 4.77 años de antiguedad no incrementa, pero 23,07 sumará al interés el valor de 4 x 0.02 = 0.08)*.
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
  - `serializar(): String`: Modificar el comportamiento de este método heredado, para retornar una cadena de caracteres con los valores de los atributos de la clase separados por `;`.
  - `toString(): String`: Retornar la información del seguro de auto con un formato similar al del seguro de hogar.

##### **`SeguroVida`**

- **Atributos específicos:** `fechaNac`, `nivelRiesgo`, `indemnizacion`. Usad el tipo de datos `LocalDate` para `fechaNac`. No serán accesibles desde fuera de la clase.

- **Constructores:** Esta clase no implementa un constructor primaro. En su lugar, tiene dos constructores secundarios, los cuales llaman al constructor de la **clase padre `Seguro`** con `super(...)`.
  - Primer constructor secundario: Lo usaremos en la Contratacíon de un **NUEVO** seguro *(genera un número de póliza automáticamente, gracias a una propiedad privada numPolizasVida que comienza en el número 800000)*
  - Segundo constructor secundario: Lo usaremos para crear una póliza ya existente. Este segundo constructor no se podrá llamar desde fuera de la clase.

- **Propiedades y métodos estácticos:**
  - `numPolizasVida: Int`: Nos ayuda a generar `numPoliza` de los nuevos seguros. No será accesible desde fuera de la clase.
  - `crearSeguro(datos: List<String>): SeguroVida`: Retorna una instancia de `SeguroVida`. El parámetro que recibe, `datos`, contiene el valor de cada propiedad en orden y deben ser convertidos según el tipo de la propiedad si es necesario.

- **Métodos que sobreescribe:**
  - `calcularImporteAnioSiguiente()`: Retornar el importe del año siguiente basándose en el interés que se pasa por parámetro, sumándole un interés residual del 0.05% por cada año cumplido y el interés de su nivel de riesgo *(Ver clase enumerada `Riesgo`)*.
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

**¿POR QUÉ ES BUENA IDEA MANTENER `app` SEPARADO DE `service`?**

   - El paquete `service` **contiene clases reutilizables que gestionan el dominio**: contratar, eliminar, buscar... Ej: GestorUsuarios, GestorSeguros.
   
   - El paquete `app` contiene **clases que dirigen el flujo de la aplicación, pero que no encapsulan lógica de negocio pura**. Ej: GestorMenu, ControlAcceso.

Esto sigue la idea de que la capa de aplicación *(application layer)* es la que usa los servicios del dominio *(domain layer)* para orquestar procesos completos.

Esta estructura se parece mucho a una separación tipo **Clean Architecture**, donde se estructura un proyecto de forma clara, separando **responsabilidades** y facilitando el mantenimiento y evolución del sistema.

Aquí se muestra una tabla donde se explica cada paquete y su **equivalencia técnica en una arquitectura por capas** o limpia:

| **Paquete** | **Responsabilidad**                                                                 | **Equivalencia técnica / capa**         |
|-------------|--------------------------------------------------------------------------------------|------------------------------------------|
| `model`     | Define las **entidades del dominio** (como `Usuario`, `Seguro`, enums, etc.).       | **Capa de Dominio (Domain)**             |
| `data`      | Implementa el acceso a datos, ya sea en memoria o desde ficheros.                   | **Capa de Infraestructura (Infrastructure)** |
| `service`   | Contiene la lógica de negocio y casos de uso: gestionar seguros y usuarios.         | **Capa de Aplicación (Use Cases)**       |
| `ui`        | Se encarga de la interacción con el usuario (por consola en este caso).             | **Capa de Presentación (User Interface)** |
| `utils`     | Funcionalidades transversales: ficheros, seguridad, validaciones, etc.              | **Utilidades transversales (Cross-cutting concerns)** |
| `app`       | Orquesta el flujo general de la aplicación: menú principal, control de acceso, etc. | **Capa de Arranque / Control de flujo (Application Layer)** |

#### **Interfaces (`IServUsuarios`, `IServSeguros`)**

```kotlin
interface IServUsuarios {
    fun iniciarSesion(nombre: String, clave: String): Perfil?
    fun agregarUsuario(nombre: String, clave: String, perfil: Perfil): Boolean
    fun eliminarUsuario(nombre: String): Boolean
    fun cambiarClave(usuario: Usuario, nuevaClave: String): Boolean
    fun buscarUsuario(nombre: String): Usuario?
    fun consultarTodos(): List<Usuario>
    fun consultarPorPerfil(perfil: Perfil): List<Usuario>
}
```

```kotlin
interface IServSeguros {
    fun contratarSeguroHogar(
        dniTitular: String,
        importe: Double,
        metrosCuadrados: Int,
        valorContenido: Double,
        direccion: String,
        anioConstruccion: Int
    ): Boolean

    fun contratarSeguroAuto(
        dniTitular: String,
        importe: Double,
        descripcion: String,
        combustible: String,
        tipoAuto: Auto,
        cobertura: Cobertura,
        asistenciaCarretera: Boolean,
        numPartes: Int
    ): Boolean

    fun contratarSeguroVida(
        dniTitular: String,
        importe: Double,
        fechaNacimiento: LocalDate,
        nivelRiesgo: Riesgo,
        indemnizacion: Double
    ): Boolean

    fun eliminarSeguro(numPoliza: Int): Boolean

    fun consultarTodos(): List<Seguro>

    fun consultarPorTipo(tipoSeguro: String): List<Seguro>
}
```

#### **Servicios (`GestorUsuarios`, `GestorSeguros`)**
- Implementan las interfaces y usan los repositorios.
- `GestorUsuarios` maneja la autenticación, creación de nuevos usuarios y cambios de contraseña.
- `GestorSeguros` se encarga de contratar, listar y eliminar seguros.

##### `GestorUsuarios`  
Esta clase implementa las interfaces `IServUsuarios` y `IUtilSeguridad`. Representa el **servicio de gestión de usuarios**. Actúa como **puente entre la lógica de negocio y el repositorio**, y aplica reglas adicionales como encriptar la contraseña.

- **¿Qué hace?**  
  Gestiona la lógica de usuarios: alta, baja, cambio de contraseña, autenticación y consultas.

- **¿Cuándo se usa?**  
  Cada vez que se desea trabajar con usuarios desde el menú principal u otras partes del sistema.

- **¿Con qué se conecta?**  
  - Con `IRepoUsuarios` para acceder o modificar la lista o fichero de usuarios.  
  - Con `IUtilSeguridad` para encriptar y verificar contraseñas.

- **¿Qué debes implementar?**

   * `iniciarSesion(nombre: String, clave: String): Perfil?`  
     Busca el usuario y verifica que la contraseña introducida sea válida. Si todo está correcto, devuelve el perfil del usuario. Si no, devuelve `null`.

   * `agregarUsuario(nombre: String, clave: String, perfil: Perfil): Boolean`  
     Encripta la clave y crea un nuevo objeto `Usuario`. Luego lo intenta añadir al repositorio.  
     Se debe comprobar que el nombre de usuario no esté ya ocupado (esto lo hace el repositorio).

   * `eliminarUsuario(nombre: String): Boolean`  
     Busca el usuario por su nombre y, si existe, lo elimina usando el repositorio.

   * `cambiarClave(usuario: Usuario, nuevaClave: String): Boolean`  
     Encripta la nueva clave y actualiza la contraseña del usuario. Usa el método del repositorio para que se persista si es necesario.

   * `buscarUsuario(nombre: String): Usuario?`  
     Devuelve el usuario con ese nombre, si existe.

   * `consultarTodos(): List<Usuario>`  
     Devuelve todos los usuarios registrados (útil para administradores).

   * `consultarPorPerfil(perfil: Perfil): List<Usuario>`  
     Devuelve la lista de usuarios cuyo perfil coincida con el solicitado.

##### `GestorSeguros`  
Esta clase implementa la interfaz `IServSeguros` y representa el **servicio que permite gestionar los seguros**.

- **¿Qué hace?**  
  Se encarga de **crear (contratar)** los seguros, eliminarlos o consultarlos. Aísla al `Main` o `GestorMenu` de tener que preocuparse por cómo se guardan o gestionan.

- **¿Cuándo se usa?**  
  Cuando se contrata un seguro, se desea eliminarlo, o consultar los ya existentes.

- **¿Con qué se conecta?**  
  - Con `IRepoSeguros`, que almacena los seguros ya sea en memoria o en fichero.
  - Con las clases `SeguroHogar`, `SeguroAuto` y `SeguroVida`, que se instancian directamente dentro del servicio.

- **¿Qué debes implementar?**

   * `contratarSeguroHogar(...)`  
     Recibe todos los datos necesarios, instancia `SeguroHogar` mediante su constructor público (que generará automáticamente `numPoliza`), y lo guarda usando el repositorio.

   * `contratarSeguroAuto(...)`  
     Igual que el anterior, pero para crear un objeto `SeguroAuto`.

   * `contratarSeguroVida(...)`  
     Igual que el anterior, pero para crear un objeto `SeguroVida`.

   * `eliminarSeguro(numPoliza: Int): Boolean`  
     Elimina un seguro usando su número de póliza. No necesitas buscarlo previamente.

   * `consultarTodos(): List<Seguro>`  
     Devuelve todos los seguros registrados, sin importar el tipo.

   * `consultarPorTipo(tipoSeguro: String): List<Seguro>`  
     Filtra los seguros por tipo (`"SeguroAuto"`, `"SeguroVida"`, `"SeguroHogar"`) usando el método `tipoSeguro()` de cada clase.

---

### **5. `ui` (Interfaz de Usuario)**
Este paquete maneja **cómo interactúa el usuario** con el sistema.

#### **Interfaz `IEntradaSalida`**
- Define métodos como `mostrar(mensaje: String)`, etc.

```kotlin
interface IEntradaSalida {
    fun mostrar(msj: String, salto: Boolean = true, pausa: Boolean = false)
    fun mostrarError(msj: String, pausa: Boolean = true)
    fun pedirInfo(msj: String = ""): String
    fun pedirInfo(msj: String, error: String, debeCumplir: (String) -> Boolean): String
    fun pedirDouble(prompt: String, error: String, errorConv: String, debeCumplir: (Double) -> Boolean): Double
    fun pedirEntero(prompt: String, error: String, errorConv: String, debeCumplir: (Int) -> Boolean): Int
    fun pedirInfoOculta(prompt: String): String
    fun pausar(msj: String = "Pulse Enter para Continuar...")
    fun limpiarPantalla(numSaltos: Int = 20)
    fun preguntar(mensaje: String): Boolean
}
```

#### **`Consola`** (Implementación de `IUserInterface`)
Esta clase implementa la interfaz `IEntradaSalida` y se encarga de gestionar toda la **interacción con el usuario a través de la consola**. Permite mostrar mensajes, solicitar datos, validar entradas y simular la limpieza de la pantalla.

- **¿Qué hace?**  
  Centraliza todas las funciones de entrada y salida (E/S) del programa, de forma que se puedan reutilizar desde cualquier parte del código.

- **¿Cuándo se usa?**  
  Siempre que necesites mostrar información, pedir datos o limpiar la pantalla. Toda interacción con el usuario pasa por esta clase.

- **¿Con qué se conecta?**  
  - Se usa directamente desde `GestorMenu`, `ControlAcceso` y otras clases de la capa de aplicación.
  - Usa la librería `JLine` para ocultar las contraseñas si es posible. En las dependencias del fichero `build.gradle.kts` debéis incluir `implementation("org.jline:jline:3.29.0")`.
  - De todas formas, yo lo he probado y no funciona si ejecutamos dentro del IDE, pero probaremos a crear un JAR y ejecutarlo directamente en la terminal.

- **¿Qué debes implementar?**

#### `mostrar(msj: String, salto: Boolean = true, pausa: Boolean = false)`
   Muestra el mensaje por consola. Si `salto` es `true`, añade un salto de línea. Si `pausa` es `true`, espera a que el usuario pulse Enter.

#### `mostrarError(msj: String, pausa: Boolean = false)`
   Muestra el mensaje como un error anteponiendo `"ERROR - "`. Si ya empieza así, no lo repite.

#### `pedirInfo(msj: String): String`
   Muestra un mensaje (si no está vacío) y devuelve el texto introducido por el usuario. Elimina espacios en blanco con `.trim()`.

#### `pedirInfo(msj: String, error: String, debeCumplir: (String) -> Boolean): String`
   Sobrecarga de pedirInfo, que solicita una entrada al usuario y lanza un `require` si no cumple una condición personalizada (debeCumplir) con el mensaje `error`.

#### `pedirDouble(prompt: String, error: String, errorConv: String, debeCumplir: (Double) -> Boolean): Double`
   1. Pide un número decimal al usuario (toDoubleOrNull). Reemplaza `,` por `.` para mayor flexibilidad.
   2. Lanza un `require` si la conversión a doble no se puedo realizar correctamente con el mensaje `errorConv`.
   3. Lanza un `require` si no cumple una condición personalizada (debeCumplir) con el mensaje `error`.
   
#### `pedirEntero(prompt: String, error: String, errorConv: String, debeCumplir: (Int) -> Boolean): Int`
   Igual que el anterior, pero para un valor numérico entero.

#### `pedirInfoOculta(prompt)`
   Solicita un texto sin mostrarlo por pantalla, ideal para contraseñas.  
   - Usa `JLine` si se puede.  
   - Captura errores como Ctrl+C (`UserInterruptException`) y Ctrl+D (`EndOfFileException`).
   - Os lo doy yo, pero dentro del IDE no funciona... veremos si funciona en la terminal.

   ```kotlin
    override fun pedirInfoOculta(prompt: String): String {
        return try {
            val terminal = TerminalBuilder.builder()
                .dumb(true) // Para entornos no interactivos como IDEs
                .build()

            val reader = LineReaderBuilder.builder()
                .terminal(terminal)
                .build()

            reader.readLine(prompt, '*') // Oculta la contraseña con '*'
        } catch (e: UserInterruptException) {
            mostrarError("Entrada cancelada por el usuario (Ctrl + C).", pausa = false)
            ""
        } catch (e: EndOfFileException) {
            mostrarError("Se alcanzó el final del archivo (EOF ó Ctrl+D).", pausa = false)
            ""
        } catch (e: Exception) {
            mostrarError("Problema al leer la contraseña: ${e.message}", pausa = false)
            ""
        }
    }
   ```

#### `pausar(msj: String)`
   Pide al usuario que pulse Enter para continuar. Sirve como pausa entre operaciones. Utiliza `pedirInfo`.

#### `limpiarPantalla(numSaltos: Int = 20)`
   Limpia la consola.  
   - Si el programa se ejecuta desde una terminal real (`System.console()` no es `null`), usa códigos ANSI.  
   - Si está en un entorno como un IDE, imprime saltos de línea.
   - También os lo doy yo para que no busquéis.

   ```kotlin
    override fun limpiarPantalla(numSaltos: Int) {
        if (System.console() != null) {
            mostrar("\u001b[H\u001b[2J", false)
            System.out.flush()
        } else {
            repeat(numSaltos) {
                mostrar("")
            }
        }
    }
   ```

#### `preguntar(mensaje: String): Boolean`
   Pide al usuario una confirmación de tipo `sí / no`.  
   - Espera `s` o `n` (minúscula).  
   - Repite la pregunta si el usuario responde otra cosa.

---

### **6. `utils` (Utilidades)**
Contiene herramientas para operaciones repetitivas.

#### **Interfaz `IUtilFicheros`**
- Define métodos de lectura y escritura en archivos.

```kotlin
interface IUtilFicheros {
    fun leerArchivo(ruta: String): List<String>
    fun agregarLinea(ruta: String, linea: String): Boolean
    fun <T: IExportable> escribirArchivo(ruta: String, elementos: List<T>): Boolean
    fun existeFichero(ruta: String): Boolean
    fun existeDirectorio(ruta: String): Boolean
}
```

#### **Clase `Ficheros`**
Esta clase será la **encargada de leer y escribir información en ficheros de texto**, por tanto, **forma parte de la capa de utilidades** del sistema.

   - Implementa la interfaz `IUtilFicheros`, la cual define todas las operaciones necesarias para trabajar con archivos del sistema.
   - Permite que cualquier otra clase (por ejemplo, repositorios) pueda **leer, guardar o actualizar archivos** sin tener que preocuparse por los detalles de bajo nivel de E/S.
   - Se asegura de **mostrar mensajes de error claros** si algo falla al acceder a los ficheros.

##### ¿Qué relación tiene con otras clases?

- Se inyecta (según el principio **DIP**) en clases como `RepoUsuariosFich` o `RepoSegurosFich`, que son los repositorios encargados de almacenar datos en ficheros.
- También necesita una instancia de la interfaz `IEntradaSalida` para poder **mostrar mensajes de error** al usuario si ocurre algún problema.

**Ejemplo de uso** *(a nivel conceptual)*:
```kotlin
val ficheros = FicherosTexto(ui)
val repoUsuarios = RepoUsuariosFich("res/Usuarios.txt", ficheros)
```

##### **Constructor con Inyección DIP**

- El constructor debe recibir una instancia de `IEntradaSalida`, que se usará para mostrar errores.
- Es importante seguir este patrón para **no acoplar** la clase a una consola específica.

##### **Método `leerArchivo(ruta: String): List<String>`**

- Lee todas las líneas de un archivo de texto y las devuelve como una lista de cadenas.
- Si el archivo **no existe** o **hay errores de lectura**, debe devolver una lista vacía y mostrar un mensaje de error.

##### **Método `agregarLinea(ruta: String, linea: String): Boolean`**

- Añade una línea al final del archivo.
- Si no existe, **lo crea automáticamente**.
- Debe añadir un salto de línea `\n` al final de la cadena.
- Si ocurre un error, devuelve `false` y muestra el mensaje correspondiente.

##### **Método `escribirArchivo(ruta: String, elementos: List<T>): Boolean`**

- Recibe una lista de objetos que implementan la interfaz `IExportable` y **escribe sus representaciones serializadas** en un archivo, **sobrescribiendo su contenido anterior**.
- Utiliza un separador como `;` en cada línea (aunque esto lo hace cada objeto en su función `serializar()`).
- Si ocurre un error, también debe devolver `false`.

##### **Método `existeFichero(ruta: String): Boolean`**

- Comprueba si un archivo concreto **existe** en la ruta proporcionada.

##### **Método `existeDirectorio(ruta: String): Boolean`**

- Comprueba si una ruta corresponde a un **directorio existente**.


##### Consejos de implementación

- Usar la clase `File` de Kotlin (`java.io.File`) para gestionar todos los ficheros.
- Usar `try-catch` para capturar excepciones como `IOException`.

#### **Interfaz `IUtilSeguridad`**
- Define métodos para encriptar y verificar claves.

```kotlin
interface IUtilSeguridad {
    fun encriptarClave(clave: String, nivelSeguridad: Int = 12): String
    fun verificarClave(claveIngresada: String, hashAlmacenado: String): Boolean
}
```

#### **Clase `Seguridad`

- Incluir la implementación de la librería externa **BCrypt** en el fichero `build.gradle`:

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

### **7. `app` (Flujo de la aplicación)**

Esta capa y el Main lo vamos a trabajar la semana que viene en clase, pero os dejo algunos detalles de su posible implementación.

#### **ControlAcceso**

***Es la clase responsable del control de acceso al sistema:***
   - Verificación de la existencia de algún usuario en el fichero `Usuarios.txt`.
   - Solicitud de creación de un usuario con perfil `ADMIN` si no existen usuarios.
   - Inicio de sesión *(petición de nombre y clave para llamar al método ' iniciarSesion()` del gestor de usuarios y hacer login)*.

Los atributos de esta clase *(constructor primario)* serían los siguientes:
   - rutaArchivoUsuarios.
   - Interfaz de usuario *(IEntradaSalida)*
   - Servicio que gestiona los usuarios *(IServUsuarios)*
   - Clase que agrupa los métodos para gestionar ficheros *(IUtilFicheros)*

#### **CargadorInicial**
Clase encargada de centralizar el proceso de carga inicial de información desde los ficheros de usuarios y seguros al arrancar la aplicación.

Esta clase utiliza **inyección de dependencias (DIP)** al recibir instancias que implementan `ICargarUsuariosIniciales` y `ICargarSegurosIniciales`, por lo que **no está acoplada a ninguna implementación concreta de los repositorios**. Además, se apoya en la interfaz `IEntradaSalida` para mostrar errores si los datos no pueden cargarse.

Podría tener los siguientes métodos:

   - **`fun cargarInfo(repoUsuarios: ICargarUsuariosIniciales, repoSeguros: ICargarSegurosIniciales)`**
   Método principal que orquesta la carga inicial de usuarios y seguros desde sus respectivos ficheros.

   - **`private fun cargarUsuarios(repoUsuarios: ICargarUsuariosIniciales)`**
   Intenta cargar los usuarios desde fichero. Si ocurre un error durante el proceso *(por ejemplo, datos mal formateados o error al leer el fichero)*, se captura la excepción y se informa al usuario mediante la interfaz `ui`.

   - **`private fun cargarSeguros(repoSeguros: ICargarSegurosIniciales)`**
   Intenta cargar los seguros desde fichero. Si ocurre un error durante el proceso *(por ejemplo, datos mal formateados o error al leer el fichero)*, se captura la excepción y se informa al usuario mediante la interfaz `ui`.

#### **GestorMenu**

Los atributos de esta clase *(constructor primario)* serían los siguientes:
   - Nombre del usuario
   - Perfil del usuario
   - Interfaz de usuario *(IEntradaSalida)*
   - Servicio que gestiona los usuarios *(IServUsuarios)*
   - Servicio que gestiona los seguros *(IServSeguros)*

Entre sus métodos, ***debería tener uno para mostrar y gestionar los menús de la aplicación**.  

Además, un método por cada una de las opciones del menú, por ejemplo:  
   - `nuevoUsuario()`
   - `eliminarUsuario`
   - `cambiarClaveUsuario()`
   - `consultarUsuarios()`
   - `contratarSeguro()`
   - `eliminarSeguro()`
   ...

#### **Menús y Permisos**

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

---

### **8. `Main.kt` (Punto de Entrada)**
- Inicializa repositorios y servicios.
- Pregunta si desea iniciar en modo SIMULACIÓN o ALMACENAMIENTO.
- Pide credenciales o permite crear un `ADMIN` si no hay usuarios.
- Carga el **menú principal** para gestionar usuarios y seguros.

