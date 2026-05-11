Instalar n8n con Docker en Linux


    Fase 1 : Instalar Docker y Docker Compose

        1* Ejecuta en tu servidor Linux (Ubuntu/Debian):

            Instalar Docker

                curl -fsSL https://get.docker.com | sh
                sudo usermod -aG docker $USER
                newgrp docker

            Instalar Docker Compose

                sudo apt-get install docker-compose-plugin -y
                docker compose version

            #Verifica que retorne algo como: Docker Compose version v2.x.x
        
        2* Crear estrcutura de carpetas 

            mkdir -p ~/n8n-docker
            cd ~/n8n-docker
            mkdir n8n_data

            #Toda la configuración vivirá en ~/n8n-docker/
        
        3* Crear archivo docker-compose.yml

            Crea el archivo

                nano ~/n8n-docker/docker-compose.yml

            Pega este contenido (reemplaza TU_IP y TU_DOMINIO)
                version: '3.8'

                services:
                n8n:
                    image: n8nio/n8n:latest
                    container_name: n8n
                    restart: always
                    ports:
                    - "5678:5678"
                    environment:
                    - N8N_HOST=TU_IP_O_DOMINIO
                    - N8N_PORT=5678
                    - N8N_PROTOCOL=http
                    - WEBHOOK_URL=http://TU_IP_O_DOMINIO:5678/
                    - N8N_BASIC_AUTH_ACTIVE=true
                    - N8N_BASIC_AUTH_USER=admin
                    - N8N_BASIC_AUTH_PASSWORD=TuPasswordSeguro123
                    - GENERIC_TIMEZONE=America/Lima
                    - TZ=America/Lima
                    volumes:
                    - ./n8n_data:/home/node/.n8n

            #Cambia TU_IP_O_DOMINIO por la IP o dominio real de tu servidor.

        4*Iniciar n8n

            cd ~/n8n-docker
            docker compose up -d

            Verificar que esté corriendo

                docker ps
                docker logs n8n

            Accede desde el navegador a: http://TU_IP:5678

            Usa admin y el password que definiste para entrar.

            *Abre el puerto 5678 en tu firewall: sudo ufw allow 5678
        
        



        
    
    
