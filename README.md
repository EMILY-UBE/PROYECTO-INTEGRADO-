# PROYECTO-INTEGRADO-
INFORME DE LA IMPLEMENTACIÓN DE UNA INFRAESTRUCTURA HÍBRIDA SEGURA PARA EL SOPORTE DE SERVICIOS DE IA

ESTUDIANTE:


EMILY MAITTE ZAMBRANO  REYES 



















1.INTRODUCCION
El avance acelerado de la Inteligencia Artificial ha generado
la necesidad de contar con infraestructuras tecnológicas capaces de
soportar cargas de trabajo crecientes, garantizar la seguridad de los
datos y mantener la disponibilidad de los servicios de forma continua.
En entornos corporativos, la combinación de sistemas Windows
(para gestión centralizada de identidades) y Linux
(para servicios web, almacenamiento distribuido y automatización)
constituye la solución más robusta y extendida.
El presente proyecto tiene como propósito diseñar, implementar y
validar una infraestructura híbrida que integre ambos sistemas operativos
bajo un mismo esquema de seguridad:
Windows Server como Controlador de Dominio con Active Directory, centralizando usuarios, permisos y políticas.
Linux (Ubuntu) como nodo de servicios: servidor web Apache, almacenamiento compartido
mediante Samba y respaldo automatizado de bases de datos de IA.


2.Se busca demostrar que una arquitectura bien diseñada permite interoperabilidad segura, gestión unificada de accesos y protección de la información, fundamentales cuando se manejan datos relacionados con modelos de Inteligencia ArtificiaL.ANALISIS DEL PROYECTO

Requerimientos de Software: Windows Server (Active Directory, DNS, DHCP, GPO, IIS), Linux Ubuntu/Debian (Apache2, Samba, Bash scripting) y VirtualBox/VMware como hipervisor.
Requerimientos de Hardware Virtual: Mínimo 2 GB de RAM para el nodo Linux, 4 GB de RAM para Windows Server y interfaces de red adaptadas en puente (Bridged) o red interna.
Necesidades del Usuario y Negocio: Gestión centralizada de credenciales, compartición eficiente de archivos entre plataformas, alojamiento web de servicios y copias de seguridad automáticas con retención de datos.
Justificación de Arquitectura: El esquema híbrido maximiza el rendimiento y la seguridad al delegar la gestión de identidades a Active Directory y el procesamiento ligero de archivos y servicios web a servidores Linux.



2.1_ DISEÑO DE LA SOLUCION

Diagrama de Arquitectura de Red: La topología implementada se distribuye desde un cortafuegos/router conectado a Internet hacia un switch principal, el cual conecta directamente el servidor Windows Server (servicios Active Directory, DNS, DHCP, GPO, IIS) y el servidor Linux Server (servicios HTTP/Apache, almacenamiento Samba y MySQL). (Insertar imagen 1000103855.jpg).
Decisiones Técnicas: Se utiliza Ext4 en Linux por su estabilidad e integridad para archivos del sistema y Samba, y NTFS en Windows Server por el control granular mediante ACLs.




3.IMPLEMENTACION TECNICA Y EVIDENCIAS
Configuración de Servicios Web y Almacenamiento en Linux: Habilitación y estado activo del servicio apache2 en el servidor Ubuntu (juan@bunto-server)
Instalación de paquetes del sistema, procesamiento de disparadores para ufw y samba
Edición del archivo de configuración global /etc/samba/smb.conf con nano (Insertar
Gestión de credenciales de usuario SMB mediante smbpasswd y verificación de parámetros de sistema

3.IMPLEMENTACION TECNICA Y EVIDENCIAS
Configuración de Servicios Web y Almacenamiento en Linux: Habilitación y estado activo del servicio apache2 en el servidor Ubuntu (juan@bunto-server)
Instalación de paquetes del sistema, procesamiento de disparadores para ufw y samba
Edición del archivo de configuración global /etc/samba/smb.conf con nano (Insertar
Gestión de credenciales de usuario SMB mediante smbpasswd y verificación de parámetros de sistema

<img width="376" height="211" alt="image" src="https://github.com/user-attachments/assets/f189ca2a-8152-463a-a589-631f63e88b1d" />
<img width="423" height="249" alt="image" src="https://github.com/user-attachments/assets/3cc685e4-0791-4ea8-b19c-34af3fcb12be" />
<img width="475" height="265" alt="image" src="https://github.com/user-attachments/assets/a7f95640-ee7f-4630-ac75-a065d3d015b2" />
<img width="468" height="249" alt="image" src="https://github.com/user-attachments/assets/95e75664-b83d-426c-9fcc-3f7cce443af4" />




3.1Código del Script de Respaldo #!/bin/bash ORIGEN="/opt/ia/config" DESTINO="/opt/ia/backups" LOG="$DESTINO/backup.log"
FECHA=$(date +%Y-%m-%d_%H-%M-%S)
ARCHIVO="$DESTINO/backup_$FECHA.tar.gz"
DIAS=7

mkdir -p "$DESTINO"
echo "Inicio del respaldo: $(date)" >> "$LOG"

if [ -d "$ORIGEN" ]; then
tar -czf "$ARCHIVO" -C "$ORIGEN" .
if [ $? -eq 0 ]; then
echo "Respaldo creado: $ARCHIVO" >> "$LOG" sha256sum "$ARCHIVO" >> "$LOG"
else
echo "Error al crear respaldo." >> "$LOG" exit 1
fi else
echo "No existe el origen." >> "$LOG" exit 1
fi

find "$DESTINO" -name "backup_*.tar.gz" -type f -mtime +$DIAS -delete echo "Respaldo finalizado: $(date)" >> "$LOG"

4.PRUEBAS Y OPTIMIZACIÓN

Validación del Servicio Apache: Verificación mediante systemctl status apache2 confirmando el estado active (running).
Verificación del Respaldo: Comprobación de integridad generando sumas hash SHA-256 y lectura directa del archivo de registro backup.log.
Verificación	de	Contenido:	Ejecución	del	comando	sudo	tar	-tzf
/opt/ia/backups/backup_*.tar.gz	para	validar	la	estructura	interna	del	paquete comprimido.

<img width="266" height="124" alt="image" src="https://github.com/user-attachments/assets/17db133c-3012-4451-95e2-ca4ed0d820d1" />
<img width="451" height="467" alt="image" src="https://github.com/user-attachments/assets/c8559866-9561-4cfa-8932-47ce22ea60fb" />
<img width="415" height="113" alt="image" src="https://github.com/user-attachments/assets/89355d0f-e54b-42bf-ad98-a02d491669c3" />


 Implementación Técnica
4.1 Configuración de Windows Server — Active Directory
Paso 1: Instalar el rol de Servicios de Dominio de Active Directory
Administrador del Servidor → Agregar roles y características → Servicios de Dominio de Active Directory
Promover el servidor a Controlador de Dominio → Crear nuevo bosque: uboe.local
Paso 2: Crear Estructura de Unidades Organizativas
plaintext
uboe.local
├── OU=Usuarios
│   ├── OU=Direccion
│   ├── OU=Ingenieria_IA
│   ├── OU=Administracion
│   └── OU=Soporte
├── OU=Equipos
└── OU=Recursos
Paso 3: Crear usuarios de prueba
usuario.ia en Ingenieria_IA
usuario.admin en Administracion
usuario.soporte en Soporte
📸 Captura esperada: Panel de Usuarios y Equipos de Active Directory mostrando la estructura de OU creada.
4.2 Configuración de Ubuntu Linux — Apache, Samba y Respaldos
Instalación de Apache (Servidor Web)
bash
# Actualizar e instalar
sudo apt update && sudo apt upgrade -y
sudo apt install apache2 -y

# Habilitar y verificar estado
sudo systemctl enable --now apache2
sudo systemctl status apache2

# Abrir puerto en firewall
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw reload
Colocar página de prueba en: /var/www/html/index.html
Acceder desde navegador del cliente Windows: http://192.168.56.20
📸 Captura esperada: Página de Apache visible desde el navegador del equipo cliente.
Instalación y Configuración de Samba
bash
# Instalar
sudo apt install samba -y

# Habilitar en firewall
sudo ufw allow 139/tcp
sudo ufw allow 445/tcp
sudo ufw reload

# Crear carpeta compartida
sudo mkdir -p /srv/compartido_ia
sudo chmod 770 /srv/compartido_ia

# Editar configuración
sudo nano /etc/samba/smb.conf
Agregar al final del archivo:
ini
[Compartido_IA]
   path = /srv/compartido_ia
   browseable = yes
   read only = no
   writable = yes
   guest ok = no
   valid users = @users
   create mask = 0660
   directory mask = 0770
bash
# Reiniciar servicio
sudo systemctl restart smbd nmbd
sudo systemctl enable --now smbd nmbd
Acceder desde cliente Windows: \\192.168.56.20\Compartido_IA
Ingresar credencial de usuario autorizado
📸 Captura esperada: Carpeta abierta en el Explorador de Archivos de Windows, mostrando archivos de prueba creados.
Script de Respaldo Automatizado
bash
#!/bin/bash
# =====================================================
# Script de Respaldo - Datos y Configuración de IA
# Proyecto Integrador — Sistemas Operativos
# =====================================================

# ------------------- CONFIGURACIÓN -------------------
ORIGEN="/srv/compartido_ia /etc/apache2 /etc/samba"
DESTINO="/respaldos"
FECHA=$(date +"%Y%m%d_%H%M%S")
NOMBRE_ARCHIVO="respaldo_ia_$FECHA.tar.gz"
REGISTRO="/var/log/respaldo_ia.log"

# Crear carpeta de destino si no existe
mkdir -p "$DESTINO"

# ------------------- EJECUTAR RESPALDO -------------------
echo "[$FECHA] Iniciando respaldo..." >> "$REGISTRO"

tar -czf "$DESTINO/$NOMBRE_ARCHIVO" $ORIGEN 2>>"$REGISTRO"

if [ $? -eq 0 ]; then
    echo "[$FECHA] ✅ Respaldo exitoso: $NOMBRE_ARCHIVO" >> "$REGISTRO"
    echo "Tamaño: $(du -h "$DESTINO/$NOMBRE_ARCHIVO" | cut -f1)" >> "$REGISTRO"
else
    echo "[$FECHA] ❌ ERROR: El respaldo falló" >> "$REGISTRO"
fi

# Eliminar respaldos de más de 7 días
find "$DESTINO" -name "respaldo_ia_*.tar.gz" -mtime +7 -delete
echo "[$FECHA] Limpieza de respaldos antiguos completada" >> "$REGISTRO"
Guardar como: /usr/local/bin/respaldo_ia.sh
bash
# Dar permisos de ejecución
sudo chmod +x /usr/local/bin/respaldo_ia.sh

# Programar ejecución diaria a las 02:00
sudo crontab -e
# Agregar la línea:
0 2 * * * /usr/local/bin/respaldo_ia.sh

<img width="331" height="409" alt="image" src="https://github.com/user-attachments/assets/80477ff0-5261-4988-8f3b-d810ae1d5c6a" />




5.CONCLUSIONES
La integración híbrida entre Windows Server y Linux optimiza el uso de recursos, garantizando un control de acceso unificado a través de Active Directory e interoperabilidad en el almacenamiento mediante Samba.
La automatización de respaldos en Linux con scripts en Shell y tareas programadas en CRON asegura la disponibilidad de la información y la integridad de los datos de configuración mediante resúmenes SHA-256.
La separación modular de servicios (servidor web, base de datos y controlador de dominio) reduce la superficie de ataque y facilita las tareas de mantenimiento administrativo.

6.RECOMENDACIONES
Implementar replicación off-site de los archivos.tar.gz generados hacia un almacenamiento secundario o en la nube para mitigar riesgos ante fallos de hardware local.
Ajustar reglas de cortafuegos en ambos entornos (UFW en Linux y Firewall de Windows) limitando el tráfico exclusivamente a los puertos de los servicios habilitados (80/443 para HTTP/HTTPS, 445 para SMB y 53 para DNS).
Monitorear periódicamente el log del script (/opt/ia/backups/backup.log) mediante alertas automatizadas en caso de detectar errores de ejecución.




EN ESTE ÚLTIMO PUNTO  ESTÁN MIS DIASPOSITIVAS:
