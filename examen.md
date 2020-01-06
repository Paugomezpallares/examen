# examen
Lo primero es poner dos adaptadores de red en modo puente(bridge) y entrar con el comando: sudo nano /etc/netplan/50-cloud-init.yaml  
![1](https://user-images.githubusercontent.com/55285319/69435473-e720ab00-0d3f-11ea-8206-c82d0fe8d74c.png)

Después entramos en local en sudo nano /etc/hosts y ponemos 192.168.1.61 y 192.168.1.62
Pasamos los datos de recurso_examen con filezilla al servidor y entramos en sudo nano /etc/apache2/sites-available/viajes.conf: 
<VirtualHost *:80>                            
    DocumentRoot /var/www/viajes
    ServerName 192.168.1.61
</VirtualHost>

![1](https://user-images.githubusercontent.com/55285319/71850440-950bcd00-30d4-11ea-8bbc-8758f8aa251e.png)
Entramos en sudo nano /etc/apache2/sites-available/ropa.conf: 
<VirtualHost *:80>                            
    DocumentRoot /var/www/ropa
    ServerName 192.168.1.61
</VirtualHost>
![2](https://user-images.githubusercontent.com/55285319/71850567-dd2aef80-30d4-11ea-9b0f-52ea53350660.png)

-Creamos sudo mkdir /etc/apache2/password,una carpeta donde crear los usuarios y sus passwords en un archivo.
-sudo htpasswd -c /etc/apache2/password/passwords-admin admin, esto sirve para crear una contraseña.
-Habilitamos Digest:sudo a2enmod auth_digest,sudo systemctl restart apache2
-sudo htdigest -c /etc/apache2/password/digest "ropa" user, creamos usuario en el grupo digest
Para entrar y modificar ropa:sudo nano /etc/apache2/sites-available/ropa.conf
![6](https://user-images.githubusercontent.com/55285319/69438909-336ee980-0d46-11ea-94a1-9d6584490fcb.png) ponemos al final admin, no user.
-Entramos a ropa y nos pregunta user y password: 
![3](https://user-images.githubusercontent.com/55285319/71851519-0ba9ca00-30d7-11ea-8082-6da3f674abb1.png)


Modificamos la quee para que solo podamos entrar con nuestra ip en local:sudo nano /etc/apache2/sites-available/viajes.conf y ponemos 
<Directory>
    <RequireAll>
      Require ip 192.168.1.108
     </RequireAll>
  </Directory>
![5](https://user-images.githubusercontent.com/55285319/71852232-8d4e2780-30d8-11ea-9124-9bb4e416b4c8.png)
Es muy importante ejecutar: sudo systemctl reload apache2 cada vez que configuremos algo.
-Entramos dentro de sudo nano /etc/apache2/sites-available/viajes.conf
-Ponemos sudo nano /etc/apache2/sites-available/viajes.conf.
![6](https://user-images.githubusercontent.com/55285319/71852234-90491800-30d8-11ea-87f0-36210e170c9b.png)
![9](https://user-images.githubusercontent.com/55285319/69441038-31a72500-0d4a-11ea-8992-0ef3190fb4e4.png)

-Hacemos cd /var/www/viajes,sudo mkdir settings, cd settings, sudo mkdir conf.txt
![10](https://user-images.githubusercontent.com/55285319/69441174-7cc13800-0d4a-11ea-92b7-7db21172ca48.png)
Entramos en sudo nano /etc/apache2/sites-available/viajes.conf y ponemos:
<Directory>
     AllowOverride All
     Options Indexes
</Directory>
Y hacemos sudo systemctl reload apache2.
Podríamos entrar con 192.168.1.61/settings.
Impedir acceso: Entraremos con sudo nano /var/www/viajes/settings/.htaccess y ponemos Require all denied.

Conexión segura:
-Activamos el servidor de SSL que ya está instalado por defecto: sudo a2enmod ssl y después sudo systemctl reload apache2
-Crear una clave privada: openssl genrsa -out certificado.key 2048
-Crear un archivo csr: openssl req -new -key certificado.key -out certificado.csr(ponemos la password)
-Crear un archivo crt: openssl x509 -req -days 90 -in certificado.csr -signkey certificado.key -out certificado.crt
###Agregar el certificado al servidor apache
Copiar los certificados a los directorios conrrespondientes:
-sudo cp certificado.crt /etc/ssl/certs
-sudo cp certificado.key /etc/ssl/private
###Configurar el archivo ssl para que detecte los certificados
-sudo nano /etc/apache2/sites-available/default-ssl.conf
###Cambiamos la linea SSLCertificateFile y SSLCertificateKeyFile a esto:
-SSLCertificateFile /etc/ssl/certs/certificado.crt
-SSLCertificateKeyFile /etc/ssl/private/certificado.key
Reiniciamos apache: sudo systemctl reload apache2
Configuramos el Firewall: sudo ufw enable
Activamos el Apache Full para activar el https: sudo ufw allow 'Apache Full'
Añadimos el VirtualHost 443 el archivo de configuración de la página a la que queremos acceder por https:
sudo nano /etc/apache2/sites-available/viajes.conf
<VirtualHost *:443>
    DocumentRoot /var/www/viajes
    ServerName 192.168.1.61
    SSLEngine On
    SSLCertificateFile /etc/ssl/certs/certificado.crt
    SSLCertificateKeyFile /etc/ssl/private/certificado.key
</VirtualHost>
Reiciamos apache: sudo systemctl reload apache2.

