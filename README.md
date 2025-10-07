🚀 Opty - Orquestração
Este repositório contém a configuração do Docker Compose para orquestrar todos os microsserviços que compõem a aplicação Opty.

📖 Sobre o Projeto
O Opty é uma aplicação web de busca de produtos que utiliza IA para entregar resultados personalizados e um poderoso comparador de preços. Esta é a camada de orquestração que une todos os serviços.

Microsserviços
opty-frontend: A interface do usuário, construída com React.
opty-backend: A API principal, construída com Python e FastAPI.
opty-socket-server: O servidor de mensageria, construído com Java e Spring Boot.

🐳 Executando o Projeto Completo
Pré-requisitos
Docker
Docker Compose
Git

Passos
Clone este repositório e os microsserviços:

git clone [https://github.com/seu-usuario/opty-orchestration.git](https://github.com/seu-usuario/opty-orchestration.git)
git clone [https://github.com/seu-usuario/opty-frontend.git](https://github.com/seu-usuario/opty-frontend.git)
git clone [https://github.com/seu-usuario/opty-backend.git](https://github.com/seu-usuario/opty-backend.git)
git clone [https://github.com/seu-usuario/opty-socket-server.git](https://github.com/seu-usuario/opty-socket-server.git)
cd opty-orchestration

Observação: O docker-compose.yml assume que todos os repositórios estarão no mesmo nível de diretório.

Configure a Chave da API (Backend):

Navegue até a pasta do backend: cd ../opty-backend

Crie um arquivo chamado .env e adicione sua chave da OpenAI:

OPENAI_API_KEY="sua_chave_aqui"

Volte para a pasta de orquestração: cd ../opty-orchestration

Inicie os containers:

docker-compose up --build

Após os containers serem construídos e iniciados, a aplicação estará acessível em http://localhost:3000.
