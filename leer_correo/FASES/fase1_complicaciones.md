OBSERVACION : 

    No encryption key found - Auto-generating and saving to: /home/node/.n8n/config
    No encryption key found - Auto-generating and saving to: /home/node/.n8n/config
    Error: EACCES: permission denied, open '/home/node/.n8n/config'

SOLUCION : 

Error de permisos en el volumen montado. El contenedor corre como usuario node (UID 1000) pero la carpeta n8n_data fue creada por root. Se arregla así:

bash# Detener el contenedor primero
cd ~/n8n-docker
docker compose down

# Dar permisos correctos a la carpeta (UID 1000 = usuario node dentro del contenedor)
sudo chown -R 1000:1000 ./n8n_data

# Verificar que quedó bien
ls -la | grep n8n_data

# Volver a levantar
docker compose up -d

# Ver logs para confirmar que arrancó sin errores
docker logs n8n --follow
Si la carpeta aún no existe:
bashmkdir -p ~/n8n-docker/n8n_data
sudo chown -R 1000:1000 ~/n8n-docker/n8n_data
docker compose up -d
También puedes agregar esta línea al docker-compose.yml como alternativa permanente, dentro del servicio n8n:
yaml    user: "1000:1000"
Quedaría así:
yamlservices:
  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    user: "1000:1000"     # <-- agrega esta línea
    restart: always
    ...



ERROR AL RECORDAR CONTRASEÑA 

Resetear contraseña de WSL Ubuntu
Paso 1 — Abrir PowerShell o CMD en Windows (no WSL):
powershell# Entrar como root a tu WSL Ubuntu
wsl -u root
Paso 2 — Ya dentro de WSL como root, cambiar la contraseña:
bash# Ver tu usuario (si no lo recuerdas)
ls /home/

# Cambiar contraseña (reemplaza TU_USUARIO)
passwd TU_USUARIO