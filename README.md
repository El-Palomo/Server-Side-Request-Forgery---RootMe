# Server-Side-Request-Forgery - RootMe

Desarrollo del CTF Server Side Request Forgery (SSRF)

## 1. Configuración de la VM

- La máquina se encuentra en la web ROOT-ME: https://www.root-me.org/en/Challenges/Web-Server/Server-Side-Request-Forgery
- Se debe crear una cuenta para acceder al ejercicio.

## 2. Identificación de la vulnerabilidad

- Es posible llamar una página a través del portal. Candidato a vulnerabilidad SSRF

<img src="https://github.com/El-Palomo/Server-Side-Request-Forgery---RootMe/blob/main/ssrf1.jpg" width=80% />

- Podemos leer archivos a través de file:///etc/passwd

<img src="https://github.com/El-Palomo/Server-Side-Request-Forgery---RootMe/blob/main/ssrf2.jpg" width=80% />

## 3. Escaneo de Puertos internos no expuestos

- Se identifican puertos internos a través de dict://

```
Puerto TCP/80

HTTP/1.1 400 Bad Request
Date: Thu, 05 Jan 2023 23:59:52 GMT
Server: Apache/2.4.6 (CentOS) PHP/5.4.16
Content-Length: 226
Connection: close
Content-Type: text/html; charset=iso-8859-1

Puerto TCP/25

220 ssrf-box.localdomain ESMTP Postfix
502 5.5.2 Error: command not recognized
500 5.5.2 Error: bad syntax
221 2.0.0 Bye

Puerto TCP/6379

-ERR Syntax error, try CLIENT (LIST | KILL ip:port | GETNAME | SETNAME connection-name)
+OK

Puerto TCP/22

Erreur Curl : Recv failure: Connection reset by peer
```

<img src="https://github.com/El-Palomo/Server-Side-Request-Forgery---RootMe/blob/main/ssrf3.jpg" width=80% />


- A través de comandos REDIS (sin autenticacion) se pueden escribir archivos en el servidor. La idea es crear un archivo de conexión reversa en el CRON y esperar a que se ejecuten para obtener SHELL.


## 4. Conexión reversa a través de REDIS

- Se puede realizar la escritura de archivo de manera manual o se puede automatizar con la herramienta GOPHERUS: https://github.com/tarunkant/Gopherus
- Con la herramienta se puede utilizar el client GOPHER y diferentes servicios para establecer conexión reversa.
- Para establecer conexión reversa se puede utilizar NGROK o un servidor en la nube como AWS.


<img src="https://github.com/El-Palomo/Server-Side-Request-Forgery---RootMe/blob/main/ssrf4.jpg" width=80% />

- Configuramos NGROK y ejecutamos el puerto TCP/1234
- 
```
┌──(root㉿kali)-[/home/kali]
└─# ngrok tcp 1234
```

<img src="https://github.com/El-Palomo/Server-Side-Request-Forgery---RootMe/blob/main/ssrf5.jpg" width=80% />

- Ahora ejecutamos el comando generado por la herramienta GOPHERUS. Toca cambiar el puerto generado por NGROK, en nuestro ejemplo el puerto 18961

<img src="https://github.com/El-Palomo/Server-Side-Request-Forgery---RootMe/blob/main/ssrf6.jpg" width=80% />

- Esperamos la conexión NETCAT





