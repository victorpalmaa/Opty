🚀 Opty

📖 Sobre o Projeto
O Opty é uma aplicação web projetada para transformar a experiência de compra online. Através de um questionário inicial, a nossa IA (OpenAI GPT-4o) traça um perfil detalhado do usuário para entender seus gostos e objetivos. Com isso, cada busca por produtos retorna resultados mais assertivos e personalizados, além de oferecer um poderoso comparador de preços e gráficos dinâmicos para auxiliar na melhor decisão de compra.

O projeto é construído em uma arquitetura de microsserviços containerizada com Docker, garantindo escalabilidade e facilidade de manutenção.
✨ Funcionalidades Principais
🤖 Perfil de Usuário com IA: Um sistema de onboarding que analisa as respostas do usuário para criar um perfil de compra personalizado.
🔍 Busca Agregada: Pesquisa produtos em múltiplos varejistas online para encontrar as melhores ofertas.
📊 Comparador de Preços e Gráficos: Ferramentas visuais para comparar preços do mesmo produto em diferentes lojas e analisar variações.
🔧 Filtros e Ordenação: Refine seus resultados por preço (maior para menor e vice-versa), ordem alfabética e mais.
💬 Mensageria em Tempo Real: Um canal de comunicação simples e direto entre o usuário e o servidor, construído com WebSockets em Java.
💎 Interface Moderna: Uma interface limpa e responsiva desenvolvida em React.
🛠️ Tecnologias Utilizadas

Este projeto é construído com uma arquitetura de microsserviços, utilizando tecnologias de ponta para cada finalidade:

Frontend:
React.js (UI Library)
Tailwind CSS (Estilização)
Zustand (Gerenciamento de Estado

Backend (API Principal):
Python com FastAPI (para a API RESTful)

Inteligência Artificial:
Integração com a API da OpenAI (Modelo gpt-4o)

Serviço de Mensageria (WebSocket):
Java com Spring Boot

Containerização:
Docker & Docker Compose

Banco de Dados:
MongoDB (Banco de Dados NoSQL)
🐳 Executando com Docker (Recomendado)

A maneira mais simples de executar toda a stack de serviços é utilizando o Docker.

Pré-requisitos
Docker
Docker Compose
Passos
Clone o repositório:
git clone [https://github.com/seu-usuario/opty.git](https://github.com/seu-usuario/opty.git)
cd opty


Configure a Chave da API:
Navegue até a pasta do backend: cd backend
Crie um arquivo chamado .env e adicione sua chave da OpenAI:
OPENAI_API_KEY="sua_chave_aqui"

Volte para a raiz do projeto: cd ..
Inicie os containers:
Na pasta raiz do projeto, execute o seguinte comando:
docker-compose up --build

Após os containers serem construídos e iniciados, a aplicação estará acessível em http://localhost:3000.
⚙️ Configuração Manual
Se preferir não usar Docker, você pode configurar cada serviço individualmente.
Pré-requisitos
Node.js (v16 ou superior)
Python (v3.9 ou superior)
Java (JDK 17 ou superior)
Git
Passos
Clone o repositório:
git clone [https://github.com/seu-usuario/opty.git](https://github.com/seu-usuario/opty.git)
cd opty

Configuração do Backend (Python/FastAPI):
Navegue até a pasta do backend: cd backend
Crie e ative um ambiente virtual:
python -m venv venv
source venv/bin/activate  # No Windows: venv\Scripts\activate

Instale as dependências:
pip install -r requirements.txt

Configure suas variáveis de ambiente. Crie um arquivo .env e adicione sua chave da OpenAI:
OPENAI_API_KEY="sua_chave_aqui"

Execute o servidor:
uvicorn main:app --reload

Configuração do Frontend (React):
Em um novo terminal, navegue até a pasta do frontend: cd frontend
Instale as dependências:
npm install

Execute a aplicação:
npm start

Configuração do Serviço de Mensageria (Java):
Navegue até a pasta do serviço Java: cd socket-server
Compile e execute o projeto com Maven:
mvn spring-boot:run

📄 Licença
Distribuído sob a Licença MIT. Veja LICENSE para mais informações.
