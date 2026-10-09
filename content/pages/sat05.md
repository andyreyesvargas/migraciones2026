<h1>Gestión de organizaciones y ubicaciones</h1>
<p align="left"><img src="../images/sat.png?raw=true"></p>
<p>
<strong>Meta:</strong>
<br>- Entendimiento de la gestión de organizaciones y ubicaciones en Red Hat Satellite.
</p>
<p>
<strong>Objetivos:</strong>
<br>- Identificación y planificación de Organizaciones y Ubicaciones en Red Hat Satellite (Demostración)
</p>
<p>
<strong>Secciones:</strong>
<br>- Crear Organizaciones en Satellite por GUI. (Demostrativo)
<br>- Crear Organizaciones en Satellite por CLI hammer. (Demostrativo)
<br>- Crear Ubicaciones en Satellite por GUI. (Demostrativo)
<br>- Crear Ubicaciones en Satellite por CLI hammer. (Demostrativo)
<br>- Laboratorio: Creando Organizaciones y Ubicaciones en Red Hat Satellite por GUI y Hammer
</p>
<p>
<strong>Laboratorios:</strong>
</p>

# Crear Organizaciones en Satellite por GUI. (Demostrativo)
La siguiente demostración tiene como finalidad aprender a crear organizaciones por la herramienta gráfica.

**Administrar > Organizaciones**

<p align="left"><img src="../images/sat14.png?raw=true"width="800" height="400"></p>

En la pagina de Organziaciones darle click al boton **Nueva organización**

<p align="left"><img src="../images/sat15.png?raw=true"width="800" height="400"></p>

Ingresar los datos como se muestra en la imagen y darle click al boton **Enviar**

<p align="left"><img src="../images/sat16.png?raw=true"width="800" height="400"></p>

Verificar que la nueva organización se haya creado de manera satisfactoria en la pagina **Organizaciones**

<p align="left"><img src="../images/sat17.png?raw=true"width="800" height="400"></p>

# Crear Organizaciones en Satellite por CLI hammer. (Demostrativo)
Autenticarse utilizando la herramienta hammer
```
[root@satellite ~]# hammer auth login basic
[Foreman] Username: admin
[Foreman] Password for admin:
Successfully logged in as 'admin'.
```
Crear la organización **Aplicaciones** con etiqueta **apps** y descripción **'Servidores del área de aplicaciones'**
```
[root@satellite ~]# hammer organization create  --name Aplicaciones --label apps --description 'Servidores del área de aplicaciones'
```
Listar las organizaciones para validar
```
[root@satellite ~]# hammer organization list
---|--------------|--------------|-------------------------------------|------------
ID | TITLE        | NAME         | DESCRIPTION                         | LABEL
---|--------------|--------------|-------------------------------------|------------
4  | Aplicaciones | Aplicaciones | Servidores del área de aplicaciones | apps
3  | Global Bank  | Global Bank  | Organizacion Principal              | principal
1  | Migraciones  | Migraciones  |                                     | Migraciones
---|--------------|--------------|-------------------------------------|------------
```

Borrar la organización **Aplicaciones**
```
[root@satellite ~]# hammer organization delete --name Aplicaciones
[....................................................................................................................................................] [100%]
```
Listar las organizaciones para validar
```
[root@satellite ~]# hammer organization list
---|-------------|-------------|------------------------|------------
ID | TITLE       | NAME        | DESCRIPTION            | LABEL
---|-------------|-------------|------------------------|------------
3  | Global Bank | Global Bank | Organizacion Principal | principal
1  | Migraciones | Migraciones |                        | Migraciones
---|-------------|-------------|------------------------|------------
```

# Crear Ubicaciones en Satellite por GUI. (Demostrativo)
**Administrar > Ubicaciones**

<p align="left"><img src="../images/sat18.png?raw=true"width="800" height="400"></p>

En la pagina de Ubicaciones darle click al boton **Nueva ubicacion**

<p align="left"><img src="../images/sat19.png?raw=true"width="800" height="400"></p>

Ingresar los datos como se muestra en la imagen y darle click al boton **Enviar**

<p align="left"><img src="../images/sat20.png?raw=true"width="800" height="400"></p>

Verificar que la nueva ubicacion se haya creado de manera satisfactoria en la pagina **Ubicaciones**

<p align="left"><img src="../images/sat21.png?raw=true"width="800" height="400"></p>

Asociar la nueva ubicación Panamá a la organización Global Bank

<p align="left"><img src="../images/sat22.png?raw=true"width="800" height="400"></p>

En la pagina de Organizaciones darle click al boton **Editar**

<p align="left"><img src="../images/sat23.png?raw=true"width="800" height="400"></p>

**Ubicaciones > Panamá y darle click al símbolo de las flechas**

<p align="left"><img src="../images/sat24.png?raw=true"width="800" height="400"></p>

Luego darle click al botón **Enviar**

<p align="left"><img src="../images/sat25.png?raw=true"width="800" height="400"></p>

En la parte superior izquierda en la parte oscura validar que exista la **organización Global Bank** y **la ubicación Panamá**

<p align="left"><img src="../images/sat26.png?raw=true"width="800" height="400"></p>

# Crear Ubicaciones en Satellite por CLI hammer. (Demostrativo)
Autenticarse utilizando la herramienta hammer
```
[root@satellite ~]# hammer auth login basic
[Foreman] Username: admin
[Foreman] Password for admin:
Successfully logged in as 'admin'.
```
Crear la ubicación **'San Isidro'** con la descripción 'Data Center Level 3'
```
[root@satellite ~]# hammer location create --name 'San Isidro' --description 'Data Center Level 3'
Location created.
```
Listar las ubicaciones para validar
```
[root@satellite ~]# hammer location list
---|------------|------------|--------------------
ID | TITLE      | NAME       | DESCRIPTION
---|------------|------------|--------------------
2  | Lima       | Lima       |
5  | Panama     | Panama     | Sede Principal
6  | San Isidro | San Isidro | Data Center Level 3
---|------------|------------|--------------------
```
Borrar la ubicación **'San Isidro'**
```
[root@satellite ~]# hammer location delete --name 'San Isidro'
Location deleted.
```
Listar las ubicaciones para validar
```
[root@satellite ~]# hammer location list
---|--------|--------|---------------
ID | TITLE  | NAME   | DESCRIPTION
---|--------|--------|---------------
2  | Lima   | Lima   |
5  | Panama | Panama | Sede Principal
---|--------|--------|---------------
```

Crear la organización y ubicación eliminadas en los pasos anteriores
```
[root@satellite ~]# hammer organization create  --name Aplicaciones --label apps --description 'Servidores del área de aplicaciones'
[root@satellite ~]# hammer location create --name 'San Isidro' --description 'Data Center Level 3'
```
Asignar la ubicación **'San Isidro'** a la organización **Aplicaciones**
```
[root@satellite ~]# hammer organization add-location --help
Usage:
    hammer organization add-location [OPTIONS]

Options:
 --id VALUE                          Organization ID
 --location[-id|-title] VALUE/NUMBER Name/Title/Id of associated location
 --name VALUE                        Set the current organization context for the request
 --title VALUE                       Set the current organization context for the request
 -h, --help                          Print help

Option details:
  Here you can find option types and the value an option can accept:

  BOOLEAN             One of true/false, yes/no, 1/0
  DATETIME            Date and time in YYYY-MM-DD HH:MM:SS or ISO 8601 format
  ENUM                Possible values are described in the option's description
  FILE                Path to a file
  KEY_VALUE_LIST      Comma-separated list of key=value.
                      JSON is acceptable and preferred way for such parameters
  LIST                Comma separated list of values. Values containing comma should be quoted or escaped with backslash.
                      JSON is acceptable and preferred way for such parameters
  MULTIENUM           Any combination of possible values described in the option's description
  NUMBER              Numeric value. Integer
  SCHEMA              Comma separated list of values defined by a schema.
                      JSON is acceptable and preferred way for such parameters
  VALUE               Value described in the option's description. Mostly simple string

```
```
[root@satellite ~]# hammer organization list
---|--------------|--------------|-------------------------------------|------------
ID | TITLE        | NAME         | DESCRIPTION                         | LABEL
---|--------------|--------------|-------------------------------------|------------
7  | Aplicaciones | Aplicaciones | Servidores del área de aplicaciones | apps
3  | Global Bank  | Global Bank  | Organizacion Principal              | principal
1  | Migraciones  | Migraciones  |                                     | Migraciones
---|--------------|--------------|-------------------------------------|------------
```
```
[root@satellite ~]# hammer location list
---|------------|------------|--------------------
ID | TITLE      | NAME       | DESCRIPTION
---|------------|------------|--------------------
2  | Lima       | Lima       |
5  | Panama     | Panama     | Sede Principal
8  | San Isidro | San Isidro | Data Center Level 3
---|------------|------------|--------------------
```
```
[root@satellite ~]# hammer organization add-location --name Aplicaciones --location 'San Isidro'
The location has been associated.
```
```
[root@satellite ~]# hammer organization info --name Aplicaciones | tail -n 15

Hostgroups:

Parameters:

Locations:
    San Isidro
Created at:         2026/10/07 20:07:04
Updated at:         2026/10/07 20:07:07
Label:              apps
Description:        Servidores del área de aplicaciones
Service Levels:
CDN configuration:
    Type: Red Hat CDN
    URL:  https://cdn.redhat.com


```
# Laboratorio de la Unidad

<br>El siguiente laboratorio se realizara en el servidor Satellite instalado en el capitulo anterior
<br>
### <br>**1. Crear una Organización y Ubicaciones utilizando la GUI**
<br>**Crear una Organización con los siguiente datos:**
<br>Nombre: **Aplicaciones**
<br>Label: **apps**
<br>Descripción: **'Servidores del área de aplicaciones'** 
<br>
<br>**Crear una Ubicación con los siguiente datos:**
<br>Nombre: **Panama**
<br>Descripción: **'Sede Principal'**
<br>**Asociar la Ubicación Panamá a la Organización Aplicaciones**
<br>
### **2. Crear una Organización y Ubicación utilizando la CLI hammer**
<br>**Crear una Organización con los siguiente datos:**
<br>Nombre: **Cajeros**
<br>Label: **caj**
<br>Descripción: **'Servidores de la Organización Cajeros'** 
<br>

<br>**Crear una Ubicación con los siguiente datos:**
<br>Nombre: **Piura** 
<br>Descripción: **'Sede Sucursal'**
<br>**Asociar la Ubicación Piura a la Organización Cajeros**


<p><br><a href="sat.md">volver</a></p>
