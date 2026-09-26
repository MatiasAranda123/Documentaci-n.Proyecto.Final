# SISTEMA DE LOTEO Y GESTIÓN DE ANIMALES

> **Institución:** ISFT N°190  
> **Carrera:** Análisis de Sistemas  
> **Materia:** Prácticas Profesionalizantes III  
> **Profesor:** Benvenuto Daniel  
> **Estudiante:** Aranda Matías  
> **Año:** 2026  

## 1. Introducción

En la gestión de establecimientos ganaderos, el registro de rodeos, rotación de potreros y controles sanitarios suele realizarse mediante anotaciones manuales, lo que incrementa el riesgo de pérdidas de datos y dificulta la trazabilidad individual.

El presente proyecto consiste en el desarrollo de una aplicación de escritorio orientada a centralizar y digitalizar la administración del ganado. El sistema permite controlar el inventario de animales mediante identificación por caravana única, gestionar el historial sanitario y supervisar la ocupación y permanencia de los ejemplares en los distintos lotes.

## 2. Objetivos
- **Digitalizar los registros de cada animal para resguardar su información completa.**
- **Administrar eficientemente las rotaciones y ocupaciones de pasturas.**
- **Permitir una gestión eficaz en entornos donde no hay conexión a internet.**
- **Planificar actividades ganaderas con seguimiento.**
- **Agilizar búsquedas sobre información específica de los animales.**

## 3. Requerimientos del sistema
### 3.1 Requerimientos Funcionales

### Módulo 1: Gestión de usuarios
- **RF01:** El sistema debe exigir el ingreso de credenciales para acceder al sistema.
- **RF02:** El sistema debe restringir las funciones disponibles según el rol del usuario.
- **RF03:** El sistema debe permitir al usuario "Admin" crear nuevos usuarios.
- **RF04:** El sistema debe permitir al usuario "Admin" restablecer contraseñas.
- **RF05:** El sistema debe permitir al usuario "Admin" desactivar cuentas de usuarios.

### Módulo 2: Gestión de lotes
- **RF06:** El sistema debe permitir registrar nuevos lotes con nombre y cantidad de ha.
- **RF07:** El sistema debe permitir modificar los datos de los lotes existentes.
- **RF08:** El sistema debe permitir dar de baja un lote que no esté ocupado por animales.
- **RF09:** El sistema debe permitir imprimir una lista con la información actual de los lotes.

### Módulo 3: Gestión de animales
- **RF10:** El sistema debe permitir registrar nuevos animales con caravana, sexo, tipo, raza, categoría, fecha de nacimiento, peso aproximado, observaciones y estado actual del animal.
- **RF11:** El sistema debe permitir modificar los datos de los animales existentes.
- **RF12:** El sistema debe validar las caravanas para que no existan animales duplicados.
- **RF13:** El sistema debe permitir actualizar el estado sanitario de los animales.
- **RF14:** El sistema debe permitir registrar la baja de los animales indicando el motivo.
- **RF15:** El sistema debe permitir imprimir una lista con la información de los animales.

### Módulo 4: Movimientos y asignaciones
- **RF16:** El sistema debe permitir asignar a los animales a los lotes determinados.
- **RF17:** El sistema debe permitir la rotación de los animales en los distintos lotes.
- **RF18:** El sistema debe registrar los traslados.
- **RF19:** El sistema debe almacenar un historial inmutable de cada traslado indicando fecha, lote de origen, lote de destino y usuario responsable.

### 3.2 Requerimientos No Funcionales
- **RNF01 - Seguridad de credenciales:** El sistema debe aplicar funciones de resumen criptográfico en la base de datos para las contraseñas.
- **RNF02 - Consistencia visual:** La interfaz debe presentar un diseño homogéneo.
- **RNF03 - Retroalimentación y usabilidad:** La interfaz debe ofrecer mensajes claros ante el ingreso de datos inválidos.
- **RNF04 - Disponibilidad e independencia de la red:** El sistema debe operar de forma autónoma en un entorno local, sin requerir acceso a internet.
- **RNF05 - Integridad de datos:** El sistema debe garantizar la consistencia en la base de datos evitando registros huérfanos.

## 4. Arquitectura
El sistema implementa una arquitectura monolítica de escritorio, estructurada internamente bajo un patrón de tres capas lógicas de diseño:

1. **Capa de presentación:** Formularios y pantallas donde el usuario interactúa, carga datos y ve reportes.

2. **Capa de procesamiento de la aplicación:** Módulos que procesan e implementan la lógica y las reglas operativas del sistema.

3. **Capa de gestión de datos:** Conexión y consultas SQL para guardar y leer la información en la base de datos local.

### 4.1 Herramientas utilizadas

- **Lenguaje de programación:** Python 3.14.7
    - **CustomTkinter:** Librería para diseño y desarrollo de la interfaz de usuario.
- **Base de datos:** MySQL 8.0.46
    - **MySQL Workbench 8.0 CE:** Modelado y administración de la base de datos.
- **Entorno de desarrollo:** Visual Studio Code.


## 5. Modelo de datos

### 5.1 Diagrama Entidad-Relación (DER)

El sistema implementa un modelo relacional normalizado compuesto por seis entidades principales:

* **animales (1) a sanidad (N):** Un animal puede registrar múltiples eventos sanitarios.
* **animales (1) a historial_pastoreo (N):** Un animal registra múltiples ingresos y egresos de lotes.
* **lotes (1) a historial_pastoreo (N):** Un lote recibe múltiples estadías de animales a lo largo del tiempo.
* **usuarios** y **recordatorios**: Operan como entidades de soporte para autenticación y avisos generales.

```text
┌──────────────────────────┐             ┌──────────────────────────┐
│          LOTES           │             │         USUARIOS         │
├──────────────────────────┤             ├──────────────────────────┤
│ PK  id_lote              │             │ PK  id_usuario           │
│     nombre               │             │     nombre_usuario (UQ)  │
│     superficie_ha        │             │     contrasena           │
│     recursos_pastura     │             │     rol                  │
└────────────┬─────────────┘             └──────────────────────────┘
             │ 1
             │                           ┌──────────────────────────┐
             │ N                         │      RECORDATORIOS       │
┌────────────┴─────────────┐             ├──────────────────────────┤
│    HISTORIAL_PASTOREO    │             │ PK  id_recordatorio      │
├──────────────────────────┤             │     texto                │
│ PK  id_movimiento        │             │     completado           │
│ FK  id_lote              │             │     fecha_creacion       │
│ FK  id_animal            │             └──────────────────────────┘
│     fecha_entrada        │
│     fecha_salida         │
└────────────┬─────────────┘
             │ N
             │
             │ 1
┌────────────┴─────────────┐             ┌──────────────────────────┐
│         ANIMALES         │ 1         N │         SANIDAD          │
├──────────────────────────┤─────────────┤──────────────────────────┤
│ PK  id_animal            │             │ PK  id_sanidad           │
│     caravana (UQ)        │             │ FK  id_animal            │
│     sexo                 │             │     tipo_evento          │
│     tipo                 │             │     descripcion          │
│     raza                 │             │     fecha                │
│     categoria            │             │     observaciones        │
│     fecha_nacimiento     │             └──────────────────────────┘
│     peso_aprox           │
│     observaciones        │
│     estado               │
└──────────────────────────┘