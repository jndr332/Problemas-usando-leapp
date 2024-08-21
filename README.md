# Problemas usando Leapp

Este repositorio proporciona soluciones a posibles errores al migrar de CentOS 7 a Rocky 8 utilizando la herramienta **Leapp**.

## Índice

1. **Error con PostgreSQL 11**
2. **Error con Java**
3. **Soporte de Btrfs removido en RHEL 8**
4. **Configuración de Firewalld no soportada**
5. **Drivers de kernel removidos en RHEL 8**

---

## 1. **Error con PostgreSQL 11 (pgdg11**

**Error:**
Actor: dnf_transaction_check
Message: DNF execution failed with non zero exit code.
STDOUT: PostgreSQL 11 for RHEL / Rocky 8 - x86_64 537 B/s | 146 B 00:00
STDERR: Errors during downloading metadata for repository ‘el8-pgdg11’
**Solución:**

1. Edita los siguientes archivos:

    ```bash
    sudo nano /etc/leapp/files/vendors.d/postgresql_map.json
    sudo nano /etc/leapp/files/vendors.d/postgresql.repo
    sudo nano /etc/yum.repos.d/CentOS-Base.repo
    ```

2. Elimina todas las referencias a `postgres-11` o `pgdg-11`. En el archivo `.json`, elimina todo el elemento `{pgdg-11}`.

---

## 2. **Error con Java**

**Error:**
Actor: dnf_transaction_check
Message: DNF execution failed with non zero exit code.
STDOUT: Problem: package javapackages-tools-5.3.0-2.module+el8.3.0+74+855e3f5d.noarch from rocky8-powertools requires javapackages-filesystem = 5.3.0-2

**Solución:**

1. Elimina los paquetes problemáticos:

    ```bash
    sudo yum remove javapackages-tools javapackages-filesystem
    ```

2. **Recomendación:** En caso de problemas similares con otros paquetes o repositorios, considera deshabilitarlos o eliminarlos.

---

## 3. **Btrfs has been removed from RHEL8**

**Para verificar si Btrfs está cargado y elminar:**
```bash
lsmod | grep btrfs
sudo modprobe -r btrfs
```

## 4. **Firewalld Configuration AllowZoneDrifting Is Unsupported**

**Solución:**
```bash
sudo setenforce 0
sudo sed -i "s/^AllowZoneDrifting=.*/AllowZoneDrifting=no/" /etc/firewalld/firewalld.conf
```

## 5. **Leapp detected loaded kernel drivers which have been removed in RHEL 8. Upgrade cannot proceed.**
**Solución:**
Verifica si el módulo pata_acpi está cargado, si no es esencial, desactívalo:
```bash
lsmod | grep pata_acpi
sudo rmmod pata_acpi
```
