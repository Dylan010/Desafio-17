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

### 2. Monitoreo de integridad con AIDE

1. Instala AIDE:
   ```
   sudo apt-get install -y aide
   ```

2. Inicializa la base de datos de AIDE:
   ```
   sudo aideinit
   sudo mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db
   ```

3. Configura AIDE:
   ```
   sudo nano /etc/aide/aide.conf
   ```
   Agrega o modifica reglas según tus necesidades.

4. Ejecuta una comprobación manual:
   ```
   sudo aide --check
   ```

## Verificación

### Prueba de 2FA

1. Desde tu máquina host, intenta conectarte por SSH:
   ```
   ssh vagrant@192.168.33.10
   ```
2. Deberías ser solicitado por tu contraseña y luego por un código de verificación.

### Prueba de AIDE

1. Modifica un archivo monitoreado:
   ```
   sudo nano /etc/passwd
   ```
   Realiza un cambio pequeño y guarda.

2. Ejecuta AIDE:
   ```
   sudo aide --check
   ```
3. Verifica que AIDE detecte y reporte el cambio.

## Mantenimiento y mejoras

- Ejecuta `sudo aide --update` regularmente para actualizar la base de datos de AIDE.
- Considera automatizar las comprobaciones de AIDE y el envío de informes.
- Revisa y actualiza las configuraciones de seguridad periódicamente.

## Solución de problemas

Si encuentras problemas durante la configuración o uso:

1. Verifica los logs del sistema:
   ```
   sudo journalctl -xe
   ```
2. Para problemas con SSH, revisa `/var/log/auth.log`:
   ```
   sudo tail -f /var/log/auth.log
   ```
3. Para problemas con AIDE, revisa la salida de los comandos para errores específicos.

