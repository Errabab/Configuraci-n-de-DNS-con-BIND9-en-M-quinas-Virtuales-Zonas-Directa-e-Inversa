# Configuracion de DNS con BIND9 en Maquinas Virtuales Zonas Directa e Inversa

## Configuración de DNS con BIND9 en Máquinas Virtuales: Zonas Directa e Inversa

Este documento explica los pasos necesarios para configurar un servidor DNS con BIND9, tanto la zona directa como la inversa, en un entorno de máquinas virtuales.

## Requisitos Previos

- Tener acceso a una máquina virtual (o servidor) con Debian o un sistema operativo basado en Linux.
- Permisos de superusuario (`sudo`) para realizar cambios en los archivos del sistema.
- Conexión a internet para descargar los paquetes de BIND9.

## 1. Instalación de BIND9

Para comenzar, necesitas instalar BIND9, que es el software utilizado para gestionar el servicio de DNS. Ejecuta los siguientes comandos en tu terminal:

```bash
sudo apt update
sudo apt install bind9 bind9utils 
```

![DNS](/img/DNS1.png)

## 2. Configuración de BIND (Zona Directa)

 * Edita el archivo de configuración local de BIND:

    El archivo **named.conf.local** es donde definimos las zonas DNS. Edita este archivo utilizando el siguiente comando:

    `sudo nano /etc/bind/named.conf.local`
    
* Agrega la configuración de la zona directa:

    Añade el siguiente bloque al archivo para definir la zona DNS directa. Reemplaza **tudominio.com** por el nombre de tu dominio.

    ```bash
    zone "tudominio.com" {
    type master;
    file "/etc/bind/db.tudominio.com";
    };
    ```
    ![DNS](/img/DNS2.png)

    **Esto le dice a BIND que el servidor que estás configurando es el maestro de esta zona.**
## 3. Creación del Archivo de Zona Directa

* Copia el archivo de plantilla:

    Copia el archivo de plantilla **db.local** a un nuevo archivo de zona específico para tu dominio:

    `sudo cp /etc/bind/db.local /etc/bind/db.tudominio.com`
* Edita el archivo de zona:

    Edita el archivo recién creado con la siguiente configuración para la zona directa:
    
    `sudo nano /etc/bind/db.tudominio.com`

* Reemplaza el contenido del archivo con lo siguiente, adaptándolo a tu dominio y dirección IP:

    ![DNS](/img/DNS3.png)

    **Asegúrate de ajustar el dominio y las direcciones IP según tu configuración.**
## 4. Reiniciar BIND

* Para aplicar los cambios, debes reiniciar el servicio de BIND:

    `sudo systemctl restart bind9`

    ![DNS](/img/DNS4.png)

## 5. Comprobar la Configuración
* Después de reiniciar el servicio, comprueba si la configuración es correcta ejecutando el siguiente comando para verificar la sintaxis de los archivos de zona:
    ```bash
    sudo named-checkconf
    sudo named-checkzone tudominio.com /etc/bind/db.tudominio.com
    ```
## 6. Configuración de BIND (Zona Inversa)
* Ahora procederemos a configurar la zona inversa, que permite resolver direcciones IP a nombres de dominio.

* Edita el archivo de configuración local de BIND:
    Vuelve a editar el archivo `named.conf.local`:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```
* Agrega la configuración de la zona inversa:

    Añade el siguiente bloque para la zona inversa. Ajusta la dirección IP según tu red (en este ejemplo, la red es 172.26.2):

    ```bash
    zone "2.26.172.in-addr.arpa." {
    type master;
    file "/etc/bind/db.172.26.2";
    };
    ```

    ![DNS](/img/DNS5.png)

## 7. Creación del Archivo de Zona Inversa
* Copia el archivo de plantilla:
    Copia el archivo db.127 para crear el archivo de zona inversa:

    `sudo cp /etc/bind/db.127 /etc/bind/db.172.26.2`
* Edita el archivo de zona inversa:
    Edita el archivo recién creado con la configuración de la zona inversa:

    `sudo nano /etc/bind/db.172.26.2`

    Reemplaza el contenido con lo siguiente:

    ![DNS](/img/DNS6.png)

    **Asegúrate de ajustar la IP y el dominio según tu configuración.**
## 8. Reiniciar BIND
* Para aplicar los cambios de la zona inversa, reinicia nuevamente el servicio de BIND:
    ```bash
    sudo systemctl restart bind9
    ```
     ![DNS](/img/DNS4.png)

## 9. Comprobación Final
* Verifica que no haya errores de sintaxis en el archivo de zona inversa y que todo funcione correctamente:

    ```bash
    dig -x 172.26.2.4
    ```
     ![DNS](/img/DNS7.png)

    ```bash
    sudo named-checkzone 2.26.172.in-addr.arpa /etc/bind/db.172.26.2
    ```

    ![DNS](/img/DNS8.png)

# Conclusión
Has configurado un *servidor DNS* utilizando **BIND9** para manejar tanto las consultas de zona directa como inversa. Esta configuración es fundamental para la resolución de nombres y direcciones IP en una red, proporcionando los cimientos para la administración de DNS.
