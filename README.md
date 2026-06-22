
# 🚀 Desafio Prático: Guardião do Almoxarifado

### Sistema de Controle de Estoque com Autenticação e Perfis de Acesso

## 📋 Contexto do Projeto
Você foi encarregado de desenvolver o backend de uma solução de gerenciamento de estoque chamada Guardião do Almoxarifado. Sua missão é construir uma API que não apenas controle o catálogo de itens, mas que registre meticulosamente cada alteração de saldo (entrada e saída dos produtos).

### Por que isso é importante?
Um almoxarifado sem controle é sinônimo de prejuízo financeiro e desorganização. O controle rigoroso de estoque é fundamental para garantir que materiais essenciais nunca faltem para a operação da empresa e que os recursos não sejam desperdiçados com perdas, desvios ou compras em duplicidade.

Neste cenário, a implementação de perfis de acesso é uma camada de segurança indispensável. Ela garante a rastreabilidade e a integridade dos dados: apenas funcionários autorizados devem ter permissão para registrar entradas e saídas de mercadorias (evitando fraudes ou erros operacionais catastróficos), enquanto outros colaboradores precisam apenas de permissão de leitura para consultar o que está disponível. É a união perfeita entre regras de negócio sólidas e segurança digital.

## 🏗️ Diretrizes Arquiteturais (Obrigatórias)
Você deve seguir rigorosamente o boilerplate e os padrões de modularização trabalhados em aula:

- **Estrutura de Pastas:** Divisão clara das responsabilidades do sistema (ex: `models`, `repository`, `controllers`, `routes`, `middlewares` e `config`).
- **Segurança de Senhas:** Nenhuma senha pode ser salva em texto aberto. Utilize a biblioteca `bcrypt` para a criptografia (hash) antes de salvar no banco.
- **Proteção de Rotas:** Utilização de Middlewares customizados para validar o Token JWT e checar o perfil do usuário antes de liberar o acesso aos endpoints.
- **Validações Coerentes:** Tratamento de erros estruturado, retornando os Status Codes HTTP corretos (200, 201, 400, 401, 403, 404, 500).

## 📦 Módulos do Sistema
Sua API deverá conter quatro módulos principais:

1. Módulo de Usuários e Autenticação

Responsável pelo gerenciamento de quem acessa o sistema.

- **Perfil de Usuário:** O sistema deve aceitar dois perfis:
    - `ADMIN`: Tem controle total. Pode cadastrar produtos e realizar movimentações de estoque.
    - `LEITOR`: Perfil restrito. Pode apenas visualizar os produtos e os relatórios.

- **Endpoints Necessários:**
    - `POST /auth/register`: Cadastro de usuários (Nome, Email, Senha e Perfil).
    - `POST /auth/login`: Autenticação. Valida as credenciais e devolve um Token JWT.

2. Módulo de Produtos (CRUD)

Gerenciamento dos itens físicos cadastrados no almoxarifado.

- **Campos Básicos:** Nome, ID do Produto (código único), Descrição, Preço e Categoria.
- **Regras de Acesso:** Criação, edição e exclusão restritas ao perfil `ADMIN`.
- **Endpoints Necessários:**
    - `POST /produtos` (Acesso restrito): Cadastrar um novo produto.
    - `GET /produtos` (Acesso livre): Listar todos os produtos.
    - `GET /produtos/:id`  (Acesso livre): Buscar os detalhes de um produto específico.
    - `PUT /produtos/:id` (Acesso restrito): Editar informações do produto.
    - `DELETE /produtos/:id` (Acesso restrito): Excluir produto.

3. Módulo de Estoque e Movimentação (O Coração do Sistema)

Responsável por garantir a integridade das quantidades físicas. **O saldo atual de um produto não pode ser alterado diretamente na rota de edição de produtos**, mas exclusivamente através de movimentações.

- **Regra de Acesso:** Apenas usuários `ADMIN` podem operar este módulo.
- **Regras de Negócio Críticas:**
    - Cada alteração deve gerar um registro com: ID do Produto, Tipo (`ENTRADA` ou `SAIDA`), Quantidade, Motivo, Data e Usuário responsável pela ação.
    - **Trava de Estoque:** Uma movimentação de SAIDA deve ser bloqueada (Erro 400) caso a quantidade solicitada seja maior que o saldo atual do produto. Nunca permita estoque negativo.
    - Após o registro da movimentação, o saldo do produto correspondente deve ser atualizado automaticamente no banco.

- **Endpoints Necessários:**
    - `POST /movimentacoes` (Apenas ADMIN)

4. Módulo de Relatórios

Para a tomada de decisão da gestão. Acessível a todos os usuários logados.

- **Endpoints Necessários:**
    - `GET /relatorios/saldo`: Retorna uma lista de todos os produtos com suas respectivas quantidades atuais.
    - `GET /relatorios/historico`: Retorna o histórico de todas as movimentações de entrada e saída, detalhando quem fez o que e quando.

## 💻 Stack Tecnológica e Requisitos Técnicos

O projeto deve ser desenvolvido obrigatoriamente utilizando a seguinte stack de tecnologias. O uso correto e integrado de cada uma delas será critério fundamental de avaliação:

- **Criação do Repositório:** Crie um repositório no GitHub para o projeto. De preferência público. Ex.: **desafio-api-estoque-lions-dev**
- **Node.js & Express:** O backend deve ser construído no ambiente de execução Node.js, utilizando o framework Express para a construção da API RESTful, gerenciamento de rotas e manipulação das requisições e respostas HTTP (verbos GET, POST, PUT, DELETE).
- **MongoDB Atlas (Cloud):** O banco de dados da aplicação deve ser hospedado na nuvem. Vocês deverão criar um cluster gratuito no **MongoDB Atlas** e configurar a Connection String da aplicação para se conectar remotamente.
- **Mongoose (ODM):** Toda a interação da sua API com o MongoDB Atlas deve ser feita obrigatoriamente através do Mongoose. É exigida a criação de Schemas e Models bem definidos para as entidades (Usuários, Produtos e Movimentações), aplicando regras de validação direto no schema.
- **JSON Web Token (JWT):** A autenticação do sistema deve seguir o padrão stateless. Após a validação das credenciais no login, a API deve gerar e retornar um token JWT assinado. Este token deverá ser exigido via `Middleware` no cabeçalho (`Authorization: Bearer <token>`) para liberar o acesso às rotas privadas.
- **Bcrypt:** A segurança das credenciais é inegociável. Nenhuma senha pode ser salva em texto puro no banco de dados. É obrigatório o uso da biblioteca `bcrypt` (ou `bcryptjs`) para gerar o hash (criptografia) das senhas no momento do cadastro e para comparar a integridade da senha informada no momento do login.
- **Variáveis de Ambiente (`dotenv`):** Informações sensíveis (como a Connection String do Atlas, a chave secreta do JWT e a porta do servidor) não devem estar fixas ("chumbadas") no código-fonte. Utilizem a biblioteca `dotenv` para carregar essas configurações a partir de um arquivo `.env` (lembrem-se de incluir o `.env` no seu `.gitignore`).
