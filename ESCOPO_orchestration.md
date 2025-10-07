Escopo Técnico: Opty - Orquestração com Docker
Versão: 1.0

1. Visão Geral
Este escopo define a configuração de containerização e orquestração de todos os microsserviços do projeto Opty. O objetivo é criar um ambiente de desenvolvimento e produção consistente e facilmente replicável.

2. Tecnologias
Containerização: Docker
Orquestração: Docker Compose

3. Estrutura de Serviços (docker-compose.yml)
frontend
   Build: A partir do Dockerfile no diretório ../opty-frontend.
   Portas: Mapeia 3000:3000.
   Depende de: backend.
   
backend
   Build: A partir do Dockerfile no diretório ../opty-backend.
   Portas: Mapeia 8000:8000.
   Variáveis de Ambiente: Carrega a OPENAI_API_KEY do arquivo .env.
   Depende de: database.
   
socket-server
   Build: A partir do Dockerfile no diretório ../opty-socket-server.
   Portas: Mapeia 8080:8080.

database
   Imagem: mongo:latest.
   Volumes: Persiste os dados do MongoDB.
   Portas: Mapeia 27017:27017.

4. Networking
Todos os serviços estarão em uma rede bridge customizada (opty-network) para permitir a comunicação entre eles usando os nomes dos serviços como hostnames (ex: http://backend:8000).
