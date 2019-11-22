# examen
Lo primero es poner dos adaptadores de red en modo puente(bridge) y entrar con el comando: sudo nano /etc/netplan/50-cloud-init.yaml  
![1](https://user-images.githubusercontent.com/55285319/69435473-e720ab00-0d3f-11ea-8206-c82d0fe8d74c.png)

Después entramos en local en sudo nano /etc/hosts y ponemos 192.168.1.61 viajes.com y 192.168.1.62 ropa.com
Pasamos los datos de recurso_examen con filezilla al servidor y entramos en sudo nano /etc/apache2/sites-available/viajes.conf: 
<VirtualHost *:80>                            
    DocumentRoot /var/www/viajes
    ServerName viajes.com
</VirtualHost>

![2](https://user-images.githubusercontent.com/55285319/69437245-34524c00-0d43-11ea-9d70-21f156fdeadd.png)
Entramos en sudo nano /etc/apache2/sites-available/ropa.conf: 
<VirtualHost *:80>                            
    DocumentRoot /var/www/ropa
    ServerName ropa.com
</VirtualHost>
![3](https://user-images.githubusercontent.com/55285319/69437285-4a600c80-0d43-11ea-9c2b-e91adbe9a60d.png)

-Creamos sudo mkdir /etc/apache2/password,una carpeta donde crear los usuarios y sus passwords en un archivo.
-sudo htpasswd -c /etc/apache2/password/passwords-admin admin, esto sirve para crear una contraseña.
-Habilitamos Digest:sudo a2enmod auth_digest,sudo systemctl restart apache2
-sudo htdigest -c /etc/apache2/password/digest "ropa" user, creamos usuario en el grupo digest
Para entrar y modificar ropa:sudo nano /etc/apache2/sites-available/ropa.conf
![6](https://user-images.githubusercontent.com/55285319/69438909-336ee980-0d46-11ea-94a1-9d6584490fcb.png) ponemos al final admin, no user.
-Entramos a ropa y nos pregunta user y password: 
![4](https://user-images.githubusercontent.com/55285319/69438796-015d8780-0d46-11ea-951c-eec48701bde8.png)
Modificamos la quee para que solo podamos entrar con nuestra ip en local:sudo nano /etc/apache2/sites-available/viajes.conf y ponemos 
<Directory>
    <RequireAll>
      Require ip 192.168.1.108
     </RequireAll>
  </Directory>
![7](https://user-images.githubusercontent.com/55285319/69439378-1b4b9a00-0d47-11ea-891e-6ac31dad053c.png)
Es muy importante ejecutar: sudo systemctl reload apache2 cada vez que configuremos algo.
-Entramos dentro de sudo nano /etc/apache2/sites-available/viajes.conf
-Ponemos sudo nano /etc/apache2/sites-available/viajes.conf.
![8](https://user-images.githubusercontent.com/55285319/69440593-69619d00-0d49-11ea-91cf-7e6838c588bf.png)

