<h1>Navegación por la UI de Red Hat Satellite y conocimiento de sus componentes</h1>
<p align="left"><img src="../images/sat.png?raw=true"></p>
<p>
<strong>Meta:</strong>
<br>- Entender las opciones disponibles en la UI de Red Hat Satellite.
</p>
<p>
<strong>Objetivos:</strong>
<br>- Visualizar las opciones disponibles en la UI de Red Hat Satellite
</p>
<p>
<strong>Secciones:</strong>
<br>- Visualización de la GUI de Red Hat Satellite.(Demostración)
<br>- Visualización de la CLI de Red Hat Satellite.(Demostración)
</p>
<p>
<strong>Laboratorios:</strong>
<br>- Explorar la GUI y CLI de Red Hat Satellite
</p>

# Visualización de la GUI de Red Hat Satellite.(Demostración)
El Instructor realizara un Tour por la herramienta Red Hat Satellite, primero loguearse en el servidor satellite instalado en los capítulos anteriores
<p align="left"><img src="../images/sat09.png?raw=true"></p>

# Visualización de la CLI de Red Hat Satellite.(Demostración)
La herramienta hammer es el CLI de Red Hat Satellite, esta ya viene preconfigurada en el servidor junto con la instalación de Satellite, se valida las credenciales ingresadas en el proceso de instalación
```
[root@satellite ~]# cat /root/.hammer/cli.modules.d/foreman.yml
:foreman:
  # Credentials. You'll be asked for them interactively if you leave them blank here
  :username: 'admin'
  :password: 'redhat'
```

Modificamos el archivo para incluir la directiva :use_sessions:
```
[root@satellite ~]# vi /root/.hammer/cli.modules.d/foreman.yml ^C
:foreman:
  # Credentials. You'll be asked for them interactively if you leave them blank here
  :username: 'admin'
  :password: 'redhat'
  :use_sessions: true
```

Probemos un logueo básico de hammer
```
[root@satellite ~]# hammer auth login basic
[Foreman] Username: admin
[Foreman] Password for admin:
Successfully logged in as 'admin'.
[root@satellite ~]# hammer auth status
```

Revisemos el parámetro de timeout de sesión, para evaluar cuantos segundos va a durar antes de cerrarse por inactividad
```
[root@satellite ~]# hammer settings list | grep ^idle_timeout
idle_timeout                                           | Idle timeout                                                 | 60                                                                               | Log out idle users after a certain number of minutes
```

El tiempo de sesión esta configurado a 60 segundos, podemos modificarlo a 40 segundos
```
[root@satellite ~]# hammer settings set --name idle_timeout --value 30
Setting [idle_timeout] updated to [30].
[root@satellite ~]# hammer settings list | grep ^idle_timeout
idle_timeout                                           | Idle timeout                                                 | 30                                                                               | Log out idle users after a certain number of minutes
```

Revisar el estado de los servicios principales o core de Satellite
```
[root@satellite ~]# hammer ping
database:
    Status:          ok
    Server Response: Duration: 0ms
cache:
    servers:
     1) Status:          ok
        Server Response: Duration: 0ms
candlepin:
    Status:          ok
    Server Response: Duration: 44ms
candlepin_auth:
    Status:          ok
    Server Response: Duration: 25ms
candlepin_events:
    Status:          ok
    message:         0 Processed, 0 Failed
    Server Response: Duration: 0ms
katello_events:
    Status:          ok
    message:         0 Processed, 0 Failed
    Server Response: Duration: 2ms
pulp3:
    Status:          ok
    Server Response: Duration: 667ms
pulp3_content:
    Status:          ok
    Server Response: Duration: 513ms
foreman_tasks:
    Status:          ok
    Server Response: Duration: 6ms
```

Modificando el mensaje en la ventana de login, primero exploramos el mensaje actual
```
[root@satellite ~]# hammer settings info --id login_text
Id:            login_text
Name:          login_text
Description:   Text to be shown in the login-page footer. Keyword $VERSION is replaced by current version.
Category:      General
Settings type: string
Value:         Version 6.18.0

```


Ahora modificamos el texto para incluir la palabra MIGRACIONES
```
[root@satellite ~]# hammer settings set --name login_text --value "Version 6.18.0 MIGRACIONES"
Setting [login_text] updated to [Version 6.18.0 MIGRACIONES].
[root@satellite ~]# hammer settings info --id login_text
Id:            login_text
Name:          login_text
Description:   Text to be shown in the login-page footer. Keyword $VERSION is replaced by current version.
Category:      General
Settings type: string
Value:         Version 6.18.0 MIGRACIONES
```
También se valida por la web
<p align="left"><img src="../images/sat10.png?raw=true"></p>

**Gestionar los servicios de red hat satellite**

Listar servicios Red Hat Satellite
```
[root@satellite ~]# satellite-maintain service list
```
Listar estado Red Hat Satellite
```
[root@satellite ~]# satellite-maintain service status
```
Parar servicios Red Hat Satellite
```
[root@satellite ~]# satellite-maintain service stop
```
Reiniciar servicios Red Hat Satellite
```
[root@satellite ~]# satellite-maintain service restart
```
Hacer una revision de salud de Red Hat Satellite
```
[root@satellite ~]# satellite-maintain health check
```

**Asignar una contraseña nueva**
```
[root@satellite ~]# foreman-rake permissions:reset password=redhat123
```

También se puede cambiar la contraseña con la consola web en la sección de usuarios
<p align="left"><img src="../main/images/sat11.png?raw=true"></p>

Se ingresa la contraseña actual y la nueva
<p align="left"><img src="../images/sat12.png?raw=true"></p>

# Laboratorio: Cliente Hammer.
<br>**Configurar el cliente hammer**
<br>- Configurar hammer para que utilice sesiones **:use_sessions:**
<br>- Logearse al satellite por la herramienta hammer utilizando **hammer auth login basic**
<br>- Setear el timeout de sesión a 30 minutos
<br>- Validar por hammer los servicios principales
<br>- Utilizar satellite-maintain service para listar, ver el estado y reiniciar los servicios de satellite.
<br>- Configurar un mensaje de inicio de sesión que diga la frase: "Red Hat Satellite MIGRACIONES v6.18.0"
<br>- Cambiar la contraseña del usuario admin por los 2 métodos: ´rimero setearla a redhat123 por CLI luego cambiarlo por la GUI a redhat.

<p><br><a href="sat">volver</a></p>
