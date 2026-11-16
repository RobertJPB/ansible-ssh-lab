# Práctica 8: Uso de Ansible para automatizar tareas en servidores

**Estudiante:** Robert Plaza (2024-2174) · ITLA

En esta práctica se usa Ansible para automatizar la configuración de 5 servidores Ubuntu. Como no se usan servidores en la nube, los servidores son contenedores Docker creados a partir de una imagen propia de Ubuntu 24.04 con servidor SSH y un usuario `ansible` (clave `ansible`) con permisos de sudo sin contraseña. Ansible se conecta a cada servidor por SSH, igual que lo haría con servidores reales.

## Estructura

```text
ansible-ssh-lab/
├── Dockerfile          # Imagen Ubuntu 24.04 con SSH, sudo, python3 y usuario ansible
├── docker-compose.yml  # 5 servidores (server1 a server5) con IP fija
├── inventario.ini      # Servidores, IPs, puertos, usuario y clave
├── ansible.cfg         # Usa inventario.ini e ignora la verificación de claves SSH
└── playbook.yml        # Tareas automatizadas
```

## Servidores

| Servidor | IP en la red Docker | Puerto SSH publicado |
|----------|--------------------|----------------------|
| server1  | 172.30.0.11        | 2221                 |
| server2  | 172.30.0.12        | 2222                 |
| server3  | 172.30.0.13        | 2223                 |
| server4  | 172.30.0.14        | 2224                 |
| server5  | 172.30.0.15        | 2225                 |

Con Docker Desktop en Windows (WSL) las IPs internas de los contenedores no son accesibles desde Ubuntu, por eso el inventario se conecta a `127.0.0.1` con el puerto publicado de cada servidor. En Linux nativo también se puede usar la IP interna como `ansible_host`.

## Tareas que automatiza el playbook

1. Actualizar la caché de APT.
2. Crear el usuario `itla` en cada servidor.
3. Crear la carpeta `/home/itla/app`.
4. Crear el archivo `hola.txt` dentro de `app`.
5. Instalar `cowsay` y `htop`.
6. Ejecutar `cowsay` para comprobar la instalación.

## Ejecución

Instalar Ansible y `sshpass` (necesario para conectarse por SSH con contraseña):

```bash
sudo apt update
sudo apt install -y ansible sshpass
```

Construir la imagen y levantar los 5 servidores:

```bash
docker compose up -d --build
docker ps
```

Probar la conexión con todos los servidores:

```bash
ansible servidores -m ping
```

Ejecutar el playbook:

```bash
ansible-playbook playbook.yml
```

Verificar el resultado en un servidor:

```bash
docker exec server1 cat /home/itla/app/hola.txt
docker exec server1 id itla
docker exec server1 /usr/games/cowsay "Hola ITLA"
```

Detener los servidores:

```bash
docker compose down
```
