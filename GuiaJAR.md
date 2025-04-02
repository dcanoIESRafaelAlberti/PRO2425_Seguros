## **Guía para compilar, empaquetar y ejecutar una app Kotlin con dependencias**

### 1. ¿Por qué necesitamos el plugin Shadow?

Cuando desarrollamos una aplicación con **librerías externas** (por ejemplo, `BCrypt`, `JLine`, etc.), no basta con compilar nuestro código fuente: también debemos **empaquetar esas dependencias** para que el programa pueda ejecutarse de forma independiente en cualquier máquina.

Ahí es donde entra en juego el plugin:

#### ¿Qué es el plugin Shadow?

El plugin [`shadow`](https://github.com/johnrengelman/shadow) permite crear un **fat JAR**: un único archivo `.jar` que **incluye todo tu código más todas las dependencias** necesarias para que se ejecute sin errores.

Este plugin registra automáticamente la tarea `shadowJar`, que puedes personalizar a tu gusto (nombre, versión, etc.).

### 2. Configuración en `build.gradle.kts`

1. Agrega al fichero `build.gradle.kts` el plugin:

```kotlin
plugins {
    ...
    application
    id("com.github.johnrengelman.shadow") version "8.1.1"
}
```

2. Indica en el fichero `build.gradle.kts` cuál es el punto de entrada de la aplicación:

```kotlin
application {
    mainClass.set("es.prog2425.segurosalquiler.MainKt")
}
```

3. Por último, agrega la tarea para empaquetar la aplicación:

```kotlin
tasks.test {
    useJUnitPlatform()
}

tasks.named<com.github.jengelman.gradle.plugins.shadow.tasks.ShadowJar>("shadowJar") {
    archiveBaseName.set("GestionSeguros")    // Nombre personalizado
    archiveVersion.set("1.0")                // Versión
    archiveClassifier.set("")                // Sin sufijo -all
    mergeServiceFiles()
    exclude("META-INF/*.SF", "META-INF/*.DSA", "META-INF/*.RSA") // Evita errores de firma
}
```

Os muestro como ejemplo mi fichero `build.gradle.kts` de este mismo proyecto:

```kotlin
plugins {
    kotlin("jvm") version "2.0.20"
    application
    id("com.github.johnrengelman.shadow") version "8.1.1"
}

application {
    mainClass.set("es.prog2425.segurosalquiler.MainKt")
}

group = "es.prog2425.taskmanager"
version = "1.0-SNAPSHOT"

repositories {
    mavenCentral()
}

dependencies {
    testImplementation(kotlin("test"))
    implementation("at.favre.lib:bcrypt:0.9.0")
    implementation("org.jline:jline:3.29.0")
}

tasks.test {
    useJUnitPlatform()
}
kotlin {
    jvmToolchain(21)
}

tasks.named<com.github.jengelman.gradle.plugins.shadow.tasks.ShadowJar>("shadowJar") {
    archiveBaseName.set("GestionSeguros") // Nombre del jar final
    archiveVersion.set("1.0")
    archiveClassifier.set("")

    mergeServiceFiles()
    exclude("META-INF/*.SF", "META-INF/*.DSA", "META-INF/*.RSA") // Evita errores de firma
}
```

### 3. Compilar y generar el JAR ejecutable

Ejecuta desde el terminal del proyecto:

```bash
./gradlew shadowJar
```

Esto creará un archivo `.jar` con todas las dependencias integradas en:

```
build/libs/GestionSeguros-1.0.jar
```

### 4. Ejecutar la app

Puedes lanzar la aplicación desde cualquier terminal compatible:

```bash
java -jar build/libs/GestionSeguros-1.0.jar
```

O desde cualquier ruta si usas la **ruta absoluta** del archivo `.jar`.

### 5. Crear un archivo `.bat` para lanzar la app fácilmente

Esto permite a usuarios menos técnicos **ejecutar la app haciendo doble clic** o escribiendo un comando corto como `gs`.

#### **PASOS**

1. Abre el **Notepad++** o el **Bloc de Notas**.
2. Escribe:

```bat
@echo off
java -jar "C:\Ruta\Completa\build\libs\GestionSeguros-1.0.jar"
```

3. Guarda el archivo como `gs.bat`.
   - Tipo: Todos los archivos
   - Asegúrate de que **no se llame `gs.bat.txt`**

#### Opcional: Añadir al `PATH` del sistema

1. Copia `gs.bat` a una carpeta de tu preferencia ***(por ejemplo en `C:\scripts\kotlin`)***.
2. Añade esa carpeta al `PATH` del sistema:
   - Inicio → "Editar las variables de entorno del sistema"
   - Variables de entorno → Editar `Path` → Añadir ruta
3. Reinicia el IDE para que detecte los cambios.

### 6. Ejecutar desde cualquier lugar con el alias `.bat`

Una vez creado el archivo por lotes y configurado el `PATH`, simplemente escribe:

```bash
gs
```

Y tu app se ejecutará inmediatamente.

