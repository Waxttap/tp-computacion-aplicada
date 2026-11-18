# Trabajo Práctico Integrador — Computación Aplicada

Universidad de Palermo · Servidor Debian 12 `TPServer`

## Integrantes

- Agustín Anzuinelli
- Alexis Figueroa
- 
- 

## Objetivo

Documentar la configuración y administración de la máquina virtual provista por la cátedra.


## Contenido del repositorio

| Archivo | Contenido |
|---|---|
| `root.tar.gz` | Directorio `/root` |
| `etc.tar.gz` | Directorio `/etc` |
| `opt.tar.gz` | Directorio `/opt` (incluye `/opt/scripts/backup_full.sh` y `/opt/particion`) |
| `www_dir.tar.gz` | Directorio `/www_dir` (sitio web: `index.php` y `logo.png`) |
| `backup_dir.tar.gz` | Directorio `/backup_dir` (backups generados por el script) |
| `var.tar.gz.part_*` | Directorio `/var`, comprimido y dividido en partes de 40 MB |

### Reconstruir `/var`

```bash
cat var.tar.gz.part_* > var.tar.gz
tar -xzf var.tar.gz
```

## Resumen de la configuración

1. **Entorno:** contraseña de root actualizada; hostname `TPServer` en `/etc/hostname` y `/etc/hosts`.
2. **Servicios:**
   - Sistema actualizado a Debian 12.
   - SSH: acceso de root por clave pública/privada (clave provista agregada a `/root/.ssh/authorized_keys`).
   - Apache 2 con PHP 8.2 y la extensión MySQLi.
   - MariaDB con el script `db.sql` cargado (base `ingenieria`).
3. **Red:** IP estática `192.168.0.200/24`, gateway `192.168.0.1`, configurada en `/etc/network/interfaces`.
4. **Almacenamiento:**
   - Disco adicional de 10 GB (`/dev/sdc`) con dos particiones tipo 83 en ext4.
   - `/dev/sdc1` (3 GB) en `/www_dir` y `/dev/sdc2` (6 GB) en `/backup_dir`, montadas al inicio vía `/etc/fstab` (por UUID).
   - Apache sirve desde `/www_dir` (`000-default.conf` y `apache2.conf`).
   - Copia de `/proc/partitions` en `/opt/particion`.
5. **Backup:** script `/opt/scripts/backup_full.sh`.
   - Uso: `backup_full.sh <origen> <destino>`; ayuda con `-help`.
   - Nombre de salida: `<directorio>_bkp_AAAAMMDD.tar.gz`.
   - Valida que el origen exista y que el destino tenga un sistema de archivos montado y permisos de escritura.
   - Programado en cron: `/var/log` todos los días a las 00:00; `/www_dir` lunes, miércoles y viernes a las 23:00.

## Notas

- La consigna indica `/var/logs`, pero ese directorio no existe en Debian; se utilizó `/var/log`.
- En el crontab de root se encontró la entrada heredada `@reboot /usr/sbin/iptables-restore /root/myfw.txt`, que referencia un archivo inexistente. Se dejó comentada.
