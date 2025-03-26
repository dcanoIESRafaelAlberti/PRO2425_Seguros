# PRO2425_Seguros

**Práctica: Gestión de Seguros con Ficheros TXT**

## [Guía de Implementación Paso a Paso](Guia.md) ***(Última actualización 26/03/2025 22:30)***

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
100000;12345678A;50.0;80;150000;Calle Mayor, 12;1998;SeguroHogar
```

Ejemplo de Seguro de Auto:

```
400002;77777777T;11.0;Hyndai i10;Galosina;COCHE;TODO_RIESGO;true;0;SeguroAuto
```

Ejemplo de Seguro de Vida:

```
800001;44000998F;54.0;01/09/2000;BAJO;200000.0;SeguroVida
```

## 3. Generación del id de los seguros

Los ids de los seguros se generarán automáticamente:

- Seguros de Hogar → desde 100000.
- Seguros de Auto → desde 400000.
- Seguros de Vida → desde 800000.

## 4. Menús y Permisos

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

📌 Menú de gestión *(Accede a todos los seguros, pero no puede gestionar usuarios)*
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

📌 Menú de consulta *(Accede solo a la consulta de seguros)*
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

## 5. Modo de Ejecución

- Al iniciar el programa, se realiza la pregunta:

   - **¿Deseas iniciar en modo SIMULACIÓN (sin guardar datos)?**

   - Dependiendo de la respuesta (s/n):
      * SIMULACIÓN: Todos los datos se manejan en memoria.
      * ALMACENAMIENTO: Se guardan y cargan desde Seguros.txt.

- A continuación, el programa deberá comprobar si existen usuarios registrados en la app *(fichero Usuarios.txt)*.

- En caso de que no existan *(no existe el fichero o el fichero está vacío)* preguntará si desea crear un usuario administrador:
   - Si responde negativamente, la aplicación finalizará.
   - Si responde positivamente, se pedirá los datos para crear el usuario con perfil `ADMIN`.

- El paso siguiente será solicitar el login o inicio de sesión *(nombre de usuario y contraseña)*.

- Por último, si ha seleccionado el modo de ejecucuón de `ALMACENAMIENTO` deberá cargar los datos que existen de los ficheros `Usuarios.txt` y `Seguros.txt`.
