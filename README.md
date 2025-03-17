# PRO2425_SegurosAlquileres

**Práctica: Gestión de Seguros y Alquileres con Ficheros TXT**

1. Introducción

Esta aplicación permite gestionar seguros y alquileres aplicando:

- POO y principios SOLID.
- Almacenamiento en ficheros TXT con datos separados por ;.
- Gestión de usuarios con claves encriptadas (BCrypt).
- Carga y validación de clientes al registrar seguros y alquileres.
- Modo de ejecución: SIMULACIÓN (memoria) o ALMACENAMIENTO (persistencia en ficheros).

2. Ficheros de Datos TXT

Se utilizarán cuatro archivos TXT, cada uno con datos separados por ;:

* Usuarios.txt (contiene usuarios con contraseña encriptada)

dni;nombre;apellidos;clave_encriptada;perfil

- Clave encriptada con BCrypt.
- Si el fichero está vacío, preguntará si se desea crear un usuario admin.

* Clientes.txt (almacena clientes registrados)

```
dni;nombre;apellidos;telefono
```

- Al registrar un seguro o alquiler, se pedirá el DNI del cliente.
- Si no existe en el fichero, se creará pidiendo sus datos.

* Seguros.txt (almacena seguros contratados)

```
id;dniTitular;fechaInicio;fechaFin;importeMensual;[atributos específicos]
```

Ejemplo para un seguro de hogar:

```
100001;12345678A;2024-03-18;2025-03-18;50.00;80;120000
```

* Viviendas.txt (almacena alquileres)

```
id;dniInquilino;ubicacion;superficie;precioMensual;fechaInicio;fechaFin;[atributos específicos]
```

Ejemplo para un alquiler de piso:

```
200001;98765432B;Madrid;90;800.00;2024-03-18;2025-03-18;5 años;Sí
```

3. Inicio de Sesión y Creación del Usuario Admin

Al ejecutar la aplicación:
	1.	Verifica si Usuarios.txt está vacío.
	2.	Si está vacío, pregunta:

No se encontraron usuarios en el sistema. ¿Desea crear un usuario admin? (S/N)

- Si responde S, pedirá los datos y lo guardará en el fichero con clave encriptada.
- Si responde N, la aplicación terminará.

	3.	Si Usuarios.txt tiene datos, pedirá:

Ingrese su DNI:
Ingrese su contraseña:

- Verificará la clave encriptada usando BCrypt.
- Cargará su perfil (admin, gestion, consulta) y mostrará el menú correspondiente.

4. Gestión de Clientes

Cada vez que se contrate un seguro o se alquile una vivienda, se pedirá el DNI del cliente.
	1.	Si el DNI ya existe en Clientes.txt, se usa el cliente existente.
	2.	Si el DNI no existe, pedirá los datos y lo guardará en Clientes.txt:

Cliente no encontrado. Se procederá a registrarlo.
Ingrese nombre: Juan
Ingrese apellidos: Pérez García
Ingrese teléfono: 600123456

5. Menús y Permisos

- Los usuarios verán opciones según su perfil.

Menú para admin

```
1. Usuarios
    1. Nuevo usuario
    2. Eliminar usuario
    3. Cambiar contraseña
2. Seguros
    1. Contratar Seguro
        1. Hogar
        2. Coche
        3. Moto
    2. Editar Seguro (ingresar ID)
    3. Eliminar Seguro (ingresar ID)
    4. Listar Seguros
3. Viviendas
    1. Alquilar Vivienda
        1. Chalet
        2. Piso
        3. Garaje
        4. Parcela
    2. Listar Alquileres
4. Salir
```

Menú para gestión
	•	Igual que el de admin, pero sin acceso a la gestión de usuarios.

Menú para consulta

```
1. Seguros
    1. Listar Seguros
2. Viviendas
    1. Listar Alquileres
3. Salir
```

6. Validación de Datos

- Cada campo será validado antes de continuar.
- Métodos estáticos en Seguro y Alquiler manejarán las validaciones.

Ejemplo:

```
class Seguro {
    companion object {
        fun validarDni(dni: String): Boolean {
            return dni.matches(Regex("[0-9]{8}[A-Z]"))
        }
    }
}
```

Si el usuario ingresa un dato incorrecto:

DNI inválido. Inténtelo nuevamente o escriba "CANCELAR" para salir.

7. Generación del id de los seguros

Los ids de los seguros se generarán automáticamente:

- Seguros de Hogar → desde 100000.
- Seguros de Coche → desde 400000.
- Seguros de Moto → desde 800000.

8. Modo de Ejecución

Al iniciar, el usuario selecciona:

Seleccione el modo de ejecución:
1. SIMULACIÓN (solo en memoria)
2. ALMACENAMIENTO (usar ficheros)

	•	SIMULACIÓN: Todos los datos se manejan en memoria.
	•	ALMACENAMIENTO: Se guardan y cargan desde ficheros.
