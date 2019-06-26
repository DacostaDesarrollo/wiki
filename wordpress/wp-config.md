# No puedo instalar temas ni plugin de Wordpress en localhost - Error de FTP

Recibo este error cuando intento instalar un tema desde Wordpress: "Error al conectar al servidor FTP".


Este error es muy comun cuando hacemos una instalación de wordpress de manera local el cual nos pude que ingresemos los datos de ftp para poder subir un plugin o un tema, para solucionar este problema es necessario seguir el siguiente paso.

Abra el archivo wp-config.php en la carpeta de instalación raíz de Wordpress y agregue

```php
define('FS_METHOD', 'direct');
```

Esta constante eliminara el error

y recuerde darle permiso a la carpeta del proyecto

```bash
sudo chmod 777 -R proyect/
```