# DESPLIEGUE — Evidencias y respuestas

Este documento recopila todas las evidencias y respuestas de la practica.

---

## Parte 1 — Evidencias minimas

### Fase 1: Instalacion y configuracion

1) Servicio Nginx activo
- Que demuestra: Que el contenedor `f41_nginx` está levantado, el proceso maestro de Nginx está en ejecución y el puerto 80 interno está publicado en el 8080 del host.
- Comando: `docker compose ps` y `docker exec -it nginx-web service nginx status`
- Evidencia:

![Evidencia 1: captura de docker compose ps (desde fuera) o service nginx status (desde dentro del contenedor) mostrando el servicio activo. Nota: systemctl no suele funcionar dentro de Docker](./evidencias/Evidencia%201%20Servicio%20Nginx%20activo.png)
![Evidencia 1.1](./evidencias/Evidencia%201-2%20Nginx%20Activo.png)

2) Configuracion cargada
- Que demuestra: Que el archivo `default.conf` se ha montado correctamente en el contenedor y Nginx lo está usando sin errores de sintaxis. 
- Comando: `docker compose exec nginx ls -la /etc/nginx/conf.d`
- Evidencia:

![Evidencia 2: captura listando el directorio de configuracion dentro del contenedor (ej: 'ls -l /etc/nginx/conf.d/' o 'sites-enabled' segun la imagen usada) donde se vea tu archivo .conf](./evidencias/Evidencia%202%20Configuración%20cargada.png)

3) Resolucion de nombres
- Que demuestra: Que el nombre configurado (fran.static en /etc/hosts) resuelve correctamente hacia el servicio web en lugar de usar IP directa.
- Evidencia:

![Evidencia 3: captura del navegador con la barra de direcciones mostrando http://nombre_web (no la IP) y la pagina cargada](./evidencias/Evidencia%203%20Resolución%20de%20nombres.png)

4) Contenido Web
- Que demuestra: Que la web principal (Cloud Academy) está desplegada en la raíz del documento (/usr/share/nginx/html) y se sirve correctamente por HTTP(S).
- Evidencia:

![Evidencia 4: la misma captura anterior, pero debe verse claramente el diseno de la web importada de Cloud Academy](./evidencias/Evidencia%204%20Contenido%20Web.png)

### Fase 2: Transferencia SFTP (Filezilla)

5) Conexion SFTP exitosa
- Que demuestra:  Que el servicio `f41_sftp` está accesible por SFTP en localhost:2222 y las credenciales configuradas funcionan.
- Evidencia:

![Evidencia 5: captura de Filezilla mostrando en el panel de registro "Status: Connected to..." y en el panel derecho el listado de carpetas remoto. Nota: en Docker la ruta suele ser /home/usuario/upload, no /var/www](./evidencias/Evidencia%205%20Conexión%20SFTP%20exitosa.png)

6) Permisos de escritura
- Que demuestra: Que el usuario SFTP tiene permisos de escritura sobre el directorio compartido (`/home/sftpuser/upload` ↔ `./site`) y puede subir archivos sin errores.
- Evidencia:

![Evidencia 6: captura de Filezilla mostrando la transferencia completada o los archivos ya presentes en el servidor remoto](./evidencias/Evidencia%206%20Permisos%20de%20escritura.png)

### Fase 3: Infraestructura Docker

7) Contenedores activos
- Que demuestra: Que los servicios `f41_nginx` y `f41_sftp` están ambos en estado Up y con los puertos publicados (8080:80, 8443:443, 2222:22).
- Comando: `docker compose ps`
- Evidencia:

![Evidencia 7: captura de docker compose ps donde se vean los dos servicios con estado Up y los puertos 0.0.0.0:8080->80/tcp y 0.0.0.0:2222->22/tcp](./evidencias/Evidencia%207%20Captura%20de%20docker%20compose%20ps%20mostrando%20f41_nginx%20y%20f41_sftp%20activos.png)

8) Persistencia (Volumen compartido)
- Que demuestra: Que el volumen/bind mount `./site` es común a ambos servicios y cualquier archivo subido por SFTP se ve inmediatamente desde Nginx.
- Evidencia:

![Evidencia 8: evidencia cruzada (Filezilla + navegador) demostrando que lo subido al SFTP se ve en la web (por ejemplo localhost:8080)](./evidencias/Evidencia%208%20Evidencia%20cruzada%20Captura%20FileZilla%20y%20sitio%20web.png)

9) Despliegue multi-sitio
- Que demuestra: Que la segunda web está desplegada en `./site/reloj` y el bloque `location /reloj/ { alias ... }` funciona sirviendo http://localhost:8080/reloj/.
- Evidencia: 

![Evidencia 9: captura del navegador en http://localhost:8080/reloj mostrando el reloj funcionando](./evidencias/Evidencia%209%20Captura%20del%20navegador%20mostrando%20el%20reloj%20funcionando.png)

### Fase 4: Seguridad HTTPS

10) Cifrado SSL
- Que demuestra: Que Nginx está escuchando en 443 con los certificados autofirmados configurados y el sitio es accesible por HTTPS (mapeado a 8443 en el host).
- Evidencia:

![Evidencia 10: captura del navegador accediendo por https://... mostrando el candado (o alerta de certificado autofirmado) y el puerto configurado (ej. 8443)](./evidencias/Evidencia%2010%20HTTPS%20candado.png)

11) Redireccion forzada
- Que demuestra: Que el bloque `server` en puerto 80 devuelve un 301 hacia la versión HTTPS, forzando el uso de conexión cifrada.
- Evidencia:

![Evidencia 11: captura de la pestaña Network (DevTools) mostrando 301 Moved Permanently al intentar entrar por HTTP](./evidencias/Evidencia%2011%20Redireccion%20301.png)

---

## Parte 2 — Evaluacion RA2 (a–j)

### a) Parametros de administracion
- Respuesta:
- Evidencias:
  - evidencias/a-01-grep-nginxconf.png
  - evidencias/a-02-nginx-t.png
  - evidencias/a-03-reload.png

### b) Ampliacion de funcionalidad + modulo investigado
- Opcion elegida (B1 o B2):
- Respuesta:
- Evidencias (B1 o B2):
  - evidencias/b1-01-gzipconf.png
  - evidencias/b1-02-compose-volume-gzip.png
  - evidencias/b1-03-nginx-t.png
  - evidencias/b1-04-curl-gzip.png
  - evidencias/b2-01-defaultconf-headers.png
  - evidencias/b2-02-nginx-t.png
  - evidencias/b2-03-curl-https-headers.png

#### Modulo investigado: <NOMBRE>
- Para que sirve:
- Como se instala/carga:
- Fuente(s):

### c) Sitios virtuales / multi-sitio
- Respuesta:
- Evidencias:
  - evidencias/c-01-root.png
  - evidencias/c-02-reloj.png
  - evidencias/c-03-defaultconf-inside.png

### d) Autenticacion y control de acceso
- Respuesta:
- Evidencias:
  - evidencias/d-01-admin-html.png
  - evidencias/d-02-defaultconf-auth.png
  - evidencias/d-03-curl-401.png
  - evidencias/d-04-curl-200.png

### e) Certificados digitales
- Respuesta:
- Evidencias:
  - evidencias/e-01-ls-certs.png
  - evidencias/e-02-compose-certs.png
  - evidencias/e-03-defaultconf-ssl.png

### f) Comunicaciones seguras
- Respuesta:
- Evidencias:
  - evidencias/f-01-https.png
  - evidencias/f-02-301-network.png

### g) Documentacion
- Respuesta:
- Evidencias: enlaces a todas las capturas

### h) Ajustes para implantacion de apps
- Respuesta:
- Evidencias:
  - evidencias/h-01-root.png
  - evidencias/h-02-reloj.png

### i) Virtualizacion en despliegue
- Respuesta:
- Evidencias:
  - evidencias/i-01-compose-ps.png

### j) Logs: monitorizacion y analisis
- Respuesta:
- Evidencias:
  - evidencias/j-01-logs-follow.png
  - evidencias/j-02-metricas.png

---

## Checklist final

### Parte 1
- [ ] 1) Servicio Nginx activo
- [ ] 2) Configuracion cargada
- [ ] 3) Resolucion de nombres
- [ ] 4) Contenido Web (Cloud Academy)
- [ ] 5) Conexion SFTP exitosa
- [ ] 6) Permisos de escritura
- [ ] 7) Contenedores activos
- [ ] 8) Persistencia (Volumen compartido)
- [ ] 9) Despliegue multi-sitio (/reloj)
- [ ] 10) Cifrado SSL
- [ ] 11) Redireccion forzada (301)

### Parte 2 (RA2)
- [ ] a) Parametros de administracion
- [ ] b) Ampliacion de funcionalidad + modulo investigado
- [ ] c) Sitios virtuales / multi-sitio
- [ ] d) Autenticacion y control de acceso
- [ ] e) Certificados digitales
- [ ] f) Comunicaciones seguras
- [ ] g) Documentacion
- [ ] h) Ajustes para implantacion de apps
- [ ] i) Virtualizacion en despliegue
- [ ] j) Logs: monitorizacion y analisis
