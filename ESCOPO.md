Escopo Mestre do Projeto: Opty
Versão: 1.0
Data: 04/10/2025

1. Visão Geral e Objetivos
1.1. Descrição do Projeto
Desenvolvimento de um Web App que revoluciona a busca de produtos online através de personalização via IA (OpenAI GPT-4o), agregação de resultados de múltiplos varejistas e ferramentas
avançadas de comparação de preços para auxiliar o usuário na melhor decisão de compra. O projeto chama-se Opty.

1.2. Objetivos de Negócio
Aumentar a conversão de compra do usuário, oferecendo resultados de busca mais relevantes.
Fidelizar usuários através de uma experiência personalizada e eficiente.
Criar uma plataforma de análise de dados sobre tendências de preços e produtos.
Monetizar através de um futuro modelo de assinatura (PRO) e parcerias com varejistas.

1.3. Escopo
DENTRO DO ESCOPO: Todas as funcionalidades listadas neste documento, incluindo cadastro, onboarding com IA, busca agregada, comparação, filtros, mensageria e visualização de dados.
FORA DO ESCOPO (Versão Inicial): Finalização da compra (checkout) dentro da plataforma, sistema de pagamento para a assinatura PRO, painel administrativo complexo.

2. Arquitetura e Tecnologias
2.1. Diagrama da Arquitetura de Microsserviços
+-----------------+      +-----------------------+      +-------------------------+
|                 |      |                       |      |                         |
|  Cliente (Web)  |----->| Reverse Proxy (Nginx) |----->| Backend API (FastAPI)   |
|     (React)     |<-----|                       |<-----|        (Python)         |
|                 |      +-----------------------+      +-----------+-------------+
+--------+--------+                |                            |
         |                         |                            |
         | (WebSocket)             |                            | (CRUD)
         |                         |                            v
+--------v--------+                |                      +-------------+
|                 |                |                      |             |
|   Serviço de    |<---------------+                      |  Banco de   |
| Mensageria (Java|                                       | Dados (MongoDB) |
|   Spring Boot)  |                                       |             |
+-----------------+                                       +-------------+


2.2. Stack Tecnológica
Frontend: React.js, React Router (para navegação), Zustand (para gerenciamento de estado), Tailwind CSS (para estilização).
Backend: Python 3.9+, FastAPI (com Pydantic para validação de dados).
Serviço de Mensageria: Java 17+, Spring Boot (com Spring WebSocket).
Inteligência Artificial: OpenAI API (Modelo gpt-4o para análise de perfil).
Banco de Dados: MongoDB.
Containerização: Docker e Docker Compose.
Proxy Reverso: Nginx (para roteamento em ambiente de produção).

3. Modelagem de Dados Detalhada (MongoDB)
Coleção: users
{
  "_id": "ObjectId",
  "username": "string", // Indexado
  "email": "string (unique, indexado)",
  "password_hash": "string",
  "onboarding_completed": "boolean",
  "preferences": { // Objeto com as preferências extraídas pela IA
    "favorite_categories": ["eletrônicos", "livros"],
    "price_sensitivity": "medium", // "low", "medium", "high"
    "brand_preference": "quality_over_brand", // "indifferent", "specific_brands"
    "user_goals_summary": "Usuário busca o melhor custo-benefício em notebooks para trabalho."
  },
  "created_at": "ISODate",
  "updated_at": "ISODate",
  "last_login": "ISODate"
}


Coleção: search_history
{
  "_id": "ObjectId",
  "user_id": "ObjectId", // Referência à coleção 'users' (indexado)
  "query": "string",
  "filters_applied": {
    "sort_by": "price_asc",
    "price_range": "100-500"
  },
  "search_timestamp": "ISODate",
  "results_count": "integer"
}


4. API Detalhada (Backend - FastAPI)
URL Base: /api/v1

4.1. Autenticação (/auth)
POST /register
Descrição: Registra um novo usuário.
Body: { "username": "string", "email": "string", "password": "string" }
Respostas: 201 Created, 400 Bad Request (dados inválidos), 409 Conflict (email/usuário já existe).
POST /token
Descrição: Autentica e retorna um token JWT (duração de 24h).
Body (Form Data): username=string&password=string
Respostas: 200 OK, 401 Unauthorized.

4.2. Usuário (/users)
GET /me
Descrição: Retorna dados do usuário autenticado.
Autenticação: JWT Bearer Token.
Respostas: 200 OK, 401 Unauthorized.

4.3. Perfil e IA (/profile)
POST /onboarding
Descrição: Processa o questionário do usuário com a IA.
Autenticação: JWT Bearer Token.
Body: { "answers": [ { "question_id": 1, "answer": "Prefiro pagar mais por qualidade." } ] }
Processo: Constrói um prompt detalhado, solicita à OpenAI uma resposta em formato JSON, valida e salva no preferences do usuário.
Respostas: 200 OK, 400 Bad Request, 401 Unauthorized, 500 Internal Server Error (falha na API da OpenAI).

4.4. Busca (/search)
GET /
Descrição: Realiza a busca de produtos.
Autenticação: JWT Bearer Token.
Query Params: q=string, sort_by=string, filter=string.
Processo: Recupera perfil do usuário -> Aprimora a query (q) com o user_goals_summary -> Dispara scrapers/APIs externas em paralelo -> Agrega e normaliza os resultados -> Agrupa ofertas por produto -> Aplica filtros e ordenação.
Response (200 OK): [ { "product_group": "Notebook Dell XPS 13", "offers": [product_result] } ]
Respostas: 200 OK, 400 Bad Request (query q faltando), 401 Unauthorized.

5. Serviço de Mensageria Detalhado (Java - WebSocket)
Endpoint: ws://<seu-dominio>:8080/chat
Autenticação: O cliente deve passar o token JWT como um query parameter na URL de conexão: ws://.../chat?token=SEU_TOKEN_JWT. O servidor valida o token na abertura da conexão (OnOpen).
Ciclo de Vida e Eventos
OnOpen: Valida o JWT. Se válido, associa a sessão ao userId. Envia evento connectionSuccess. Se inválido, fecha a conexão.
OnMessage: Recebe um payload JSON, processa com base no type e responde adequadamente.
OnClose: Limpa os dados da sessão do usuário.
OnError: Loga o erro.
Payloads dos Eventos
Cliente → Servidor: { "type": "user_message", "content": "Olá!" }
Servidor → Cliente: { "type": "server_response", "content": "Recebemos sua mensagem.", "timestamp": "ISODate" }
Servidor → Cliente: { "type": "connection_ack", "message": "Conectado com sucesso ao servidor de chat." }

6. Fluxos de Usuário
6.1. Fluxo de Novo Usuário
Usuário acessa a LoginPage.
Clica em "Registrar" e preenche o RegisterForm.
API (POST /auth/register) cria o usuário.
Usuário é redirecionado para a OnboardingPage.
Preenche o OnboardingQuiz.
API (POST /profile/onboarding) processa as respostas com a IA e salva o perfil.
Usuário é redirecionado para a HomePage, pronto para fazer sua primeira busca.

6.2. Fluxo de Busca Padrão
Usuário logado está na HomePage.
Digita o produto desejado na SearchBar e pressiona Enter.
Frontend chama a API (GET /search?q=...) com o token de autenticação.
Backend executa o processo de busca personalizada.
Resultados são exibidos no ResultsGrid.
Usuário utiliza a FilterBar para refinar os resultados. A cada mudança, uma nova chamada à API é feita.
Usuário clica em um grupo de produtos para ver o PriceComparisonChart.

7. Requisitos Não Funcionais
Segurança:
Senhas devem ser hasheadas usando bcrypt.
Todas as chaves de API e segredos devem ser gerenciados por variáveis de ambiente (nunca commitados).
Implementar proteção contra ataques comuns (XSS, CSRF).
Desempenho:
Tempo de resposta da API de busca < 5 segundos.
Carregamento inicial da página (First Contentful Paint) < 2 segundos.
Escalabilidade:
A arquitetura de microsserviços permite o escalonamento horizontal independente de cada serviço.
O MongoDB suporta escalabilidade através de sharding.
Manutenibilidade:
Código deve seguir os padrões de estilo (PEP 8 para Python, Prettier para React).
Cobertura de testes unitários de no mínimo 70% para o backend.

8. Riscos e Mitigações
Risco 1: Varejistas bloquearem os IPs dos scrapers.
Mitigação: Utilizar um pool de proxies rotativos e user-agents variados. Priorizar o uso de APIs oficiais sempre que possível.
Risco 2: Alta latência ou custo da API da OpenAI.
Mitigação: Implementar caching para as análises de perfil. Otimizar os prompts para reduzir o número de tokens.
Risco 3: Inconsistência na estrutura dos dados retornados pelos varejistas.
Mitigação: Desenvolver uma camada de normalização de dados robusta no backend para padronizar os resultados antes de enviá-los ao frontend.
