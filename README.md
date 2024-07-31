# Sistema de Gestión de Seguridad de la Información (SGSI)

Este proyecto implementa un SGSI básico enfocado en la autenticación de dos factores (2FA) para SSH y el monitoreo de integridad de archivos utilizando AIDE en un entorno Ubuntu Server.

## Requisitos previos

- Vagrant
- VirtualBox

## Configuración del entorno

1. Clona este repositorio:
   ```
   git clone https://github.com/Dylan010/Desafio-17
   cd Desafio-17
   ```

2. Inicia la máquina virtual:
   ```
   vagrant up
   ```

3. Accede a la máquina virtual:
   ```
   vagrant ssh
   ```

## Implementación del SGSI

### 1. Autenticación de dos factores (2FA) para SSH

1. Instala Google Authenticator:
   ```
   sudo apt-get update
   sudo apt-get install -y libpam-google-authenticator
   ```

2. Configura Google Authenticator:
   ```
   google-authenticator
   ```
   Sigue las instrucciones en pantalla y guarda la clave secreta y los códigos de recuperación.

3. Configura PAM para SSH:
   ```
   sudo nano /etc/pam.d/sshd
   ```
   Agrega al final del archivo:
   ```
   auth required pam_google_authenticator.so
   ```

4. Modifica la configuración de SSH:
   ```
   sudo nano /etc/ssh/sshd_config
   ```
   Asegúrate de que las siguientes líneas estén presentes y descomentadas:
   ```
   ChallengeResponseAuthentication yes
   UsePAM yes
   ```

5. Reinicia el servicio SSH:
   ```
   sudo systemctl restart sshd
   ```
### 2. Monitoreo de integridad con Tripwire

1. Instala Tripwire:
   ```
   sudo apt-get install -y tripwire
   ```

2. Durante la instalación, se te pedirá que configures dos contraseñas: una para el sitio y otra para la clave local. Asegúrate de recordarlas.

3. Inicializa la base de datos de Tripwire:
   ```
   sudo tripwire --init
   ```

4. Ejecuta un chequeo inicial:
   ```
   sudo tripwire --check
   ```

5. Para personalizar qué archivos y directorios monitorear, edita el archivo de política:
   ```
   sudo nano /etc/tripwire/twpol.txt
   ```
   Modifica las reglas según tus necesidades.

6. Después de modificar la política, actualiza la base de datos:
   ```
   sudo twadmin --create-polfile /etc/tripwire/twpol.txt
   sudo tripwire --init
   ```

7. Para ejecutar una comprobación manual:
   ```
   sudo tripwire --check
   ```

## Verificación

### Prueba de Tripwire

1. Modifica un archivo monitoreado:
   ```
   sudo nano /etc/passwd
   ```
   Realiza un cambio pequeño y guarda.

2. Ejecuta Tripwire:
   ```
   sudo tripwire --check
   ```
3. Verifica que Tripwire detecte y reporte el cambio.

### Prueba de 2FA

1. Desde tu máquina host, intenta conectarte por SSH:
   ```
   ssh vagrant@192.168.56.10
   ```
2. Deberías ser solicitado por tu contraseña y luego por un código de verificación.

## Mantenimiento y mejoras

- Revisa y actualiza las configuraciones de seguridad periódicamente.
- Ejecuta `sudo tripwire --check` regularmente para verificar la integridad del sistema.
- Considera automatizar las comprobaciones de Tripwire y el envío de informes.
- Actualiza la base de datos de Tripwire después de cambios autorizados con:
  ```
  sudo tripwire --update --twrfile /var/lib/tripwire/report/<nombre_del_reporte>.twr
  ```

