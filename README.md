# 🚀 RocketLog - API de Gerenciamento de Entregas

Uma **API RESTful robusta** para gerenciamento e rastreamento de encomendas, desenvolvida com TypeScript, Node.js e Express. O projeto implementa autenticação segura, controle de acesso baseado em papéis (RBAC) e um sistema completo de logs de entrega.

![Node.js](https://img.shields.io/badge/Node.js-v18+-green?style=flat-square&logo=node.js)
![TypeScript](https://img.shields.io/badge/TypeScript-5.5-blue?style=flat-square&logo=typescript)
![Express](https://img.shields.io/badge/Express-4.19-black?style=flat-square&logo=express)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Latest-336791?style=flat-square&logo=postgresql)
![Prisma](https://img.shields.io/badge/Prisma-5.19-2D3748?style=flat-square&logo=prisma)

---


## ✨ Features

✅ **Autenticação Segura**
- Autenticação com JWT (JSON Web Tokens)
- Senhas criptografadas com bcrypt
- Tokens com expiração configurável

✅ **Controle de Acesso (RBAC)**
- Dois papéis de usuário: **customer** e **sale**
- Validação de permissões em cada rota
- Middlewares de autenticação e autorização

✅ **Gerenciamento de Entregas**
- Criar entregas com descrição e usuário associado
- Consultar todas as entregas com informações do usuário
- Atualizar status de entrega (processing → shipped → delivered)

✅ **Sistema de Logs**
- Registrar mudanças de status com descrição
- Histórico completo de cada entrega
- Rastreamento automático de timestamps

✅ **Validação de Dados**
- Validação de schemas com Zod
- Tipagem forte com TypeScript
- Tratamento de erros centralizado

✅ **Testes Automatizados**
- Testes unitários e de integração com Jest
- Testes de endpoints com Supertest
- Configuração de ambiente de teste

✅ **Containerização**
- Docker Compose para ambientes de desenvolvimento
- Configuração pronta para banco de dados PostgreSQL

---

## 🛠️ Stack Tecnológico

| Categoria | Tecnologia | Versão |
|-----------|-----------|--------|
| **Runtime** | Node.js | v18+ |
| **Linguagem** | TypeScript | 5.5 |
| **Framework** | Express.js | 4.19 |
| **Banco de Dados** | PostgreSQL | Latest |
| **ORM** | Prisma | 5.19 |
| **Autenticação** | JWT | 9.0 |
| **Criptografia** | Bcrypt | 5.1 |
| **Validação** | Zod | 3.23 |
| **Testes** | Jest | 29.7 |
| **Testes HTTP** | Supertest | 7.0 |
| **Build** | tsup | 8.3 |
| **Dev Server** | tsx | 4.16 |

---

## 📦 Pré-requisitos

- **Node.js**: v18 ou superior
- **npm** ou **yarn**: gerenciador de pacotes
- **PostgreSQL**: 12 ou superior
- **Docker** (opcional): para ambiente containerizado

---

## 🚀 Instalação

### 1. Clone o repositório

```bash
git clone https://github.com/AllifBrun/rocketlog-deploy.git
cd rocketlog-deploy
```

### 2. Instale as dependências

```bash
npm install
```

### 3. Configure o banco de dados

Se estiver usando Docker:

```bash
docker-compose up -d
```

Ou configure manualmente seu PostgreSQL e obtenha a `DATABASE_URL`.

### 4. Configure as variáveis de ambiente

Crie um arquivo `.env` na raiz do projeto:

```bash
cp .env-example .env
```

Edite o `.env` com suas configurações:

```env
# Banco de Dados
DATABASE_URL="postgresql://user:password@localhost:5432/rocketlog"

# JWT
JWT_SECRET="sua-chave-secreta-super-segura"
JWT_EXPIRES_IN="7d"
```

### 5. Execute as migrations do banco de dados

```bash
npx prisma migrate dev
```

### 6. Inicie o servidor

**Modo desenvolvimento:**

```bash
npm run dev
```

O servidor estará disponível em `http://localhost:3000`

**Modo produção:**

```bash
npm run build
npm start
```

---

## ⚙️ Configuração

### Variáveis de Ambiente

Arquivo: `.env`

```env
# Banco de Dados
DATABASE_URL="postgresql://user:password@localhost:5432/rocketlog"

# Autenticação JWT
JWT_SECRET="sua-chave-secreta-aqui"
JWT_EXPIRES_IN="7d"
```

### Docker Compose

O arquivo `docker-compose.yml` está configurado para PostgreSQL:

```bash
# Iniciar serviços
docker-compose up -d

# Parar serviços
docker-compose down
```

---

## 💡 Como Usar

### 1. Criar um Usuário

**Endpoint:** `POST /users`

```bash
curl -X POST http://localhost:3000/users \
  -H "Content-Type: application/json" \
  -d '{
    "name": "João Silva",
    "email": "joao@example.com",
    "password": "senha123"
  }'
```

**Resposta:**

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "name": "João Silva",
  "email": "joao@example.com",
  "role": "customer",
  "createdAt": "2026-05-11T10:30:00Z",
  "updatedAt": "2026-05-11T10:30:00Z"
}
```

### 2. Fazer Login

**Endpoint:** `POST /sessions`

```bash
curl -X POST http://localhost:3000/sessions \
  -H "Content-Type: application/json" \
  -d '{
    "email": "joao@example.com",
    "password": "senha123"
  }'
```

**Resposta:**

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "name": "João Silva",
    "email": "joao@example.com",
    "role": "customer"
  }
}
```

### 3. Criar uma Entrega (apenas role `sale`)

**Endpoint:** `POST /deliveries`

```bash
curl -X POST http://localhost:3000/deliveries \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "description": "Encomenda com livros e eletrônicos"
  }'
```

### 4. Listar Entregas

**Endpoint:** `GET /deliveries`

```bash
curl -X GET http://localhost:3000/deliveries \
  -H "Authorization: Bearer {token}"
```

### 5. Atualizar Status de Entrega

**Endpoint:** `PATCH /deliveries/:id/status`

```bash
curl -X PATCH http://localhost:3000/deliveries/123/status \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "status": "shipped"
  }'
```

---

## 📁 Estrutura do Projeto

```
rocketlog-deploy/
├── src/
│   ├── controllers/          # Lógica de negócio
│   │   ├── users-controller.ts
│   │   ├── sessions-controller.ts
│   │   ├── deliveries-controller.ts
│   │   ├── deliveries-status-controller.ts
│   │   └── delivery-logs-controller.ts
│   ├── routes/               # Definição de endpoints
│   │   ├── users-routes.ts
│   │   ├── sessions-routes.ts
│   │   ├── deliveries-routes.ts
│   │   ├── delivery-logs-routes.ts
│   │   └── index.ts
│   ├── middlewares/          # Autenticação e tratamento de erros
│   │   ├── ensure-authenticated.ts
│   │   ├── verifyUserAuthorization.ts
│   │   └── error-handling.ts
│   ├── database/             # Conexão Prisma
│   │   └── prisma.ts
│   ├── configs/              # Configurações
│   │   └── auth.ts
│   ├── types/                # Tipos TypeScript
│   ├── utils/                # Funções utilitárias
│   ├── tests/                # Testes
│   ├── app.ts                # Configuração Express
│   ├── server.ts             # Inicialização do servidor
│   └── env.ts                # Validação de variáveis de ambiente
├── prisma/
│   ├── schema.prisma         # Schema do banco de dados
│   └── migrations/           # Histórico de migrations
├── build/                    # Saída de build
├── .env.example              # Exemplo de variáveis de ambiente
├── docker-compose.yml        # Configuração Docker
├── jest.config.ts            # Configuração Jest
├── tsconfig.json             # Configuração TypeScript
├── package.json              # Dependências e scripts
└── README.md                 # Este arquivo
```

---

## 🔌 Endpoints da API

### Usuários

| Método | Endpoint | Descrição | Auth |
|--------|----------|-----------|------|
| POST | `/users` | Criar novo usuário | ❌ |
| GET | `/users` | Listar usuários | ✅ (sale) |

### Autenticação

| Método | Endpoint | Descrição | Auth |
|--------|----------|-----------|------|
| POST | `/sessions` | Fazer login | ❌ |

### Entregas

| Método | Endpoint | Descrição | Auth |
|--------|----------|-----------|------|
| POST | `/deliveries` | Criar entrega | ✅ (sale) |
| GET | `/deliveries` | Listar entregas | ✅ (sale) |
| PATCH | `/deliveries/:id/status` | Atualizar status | ✅ (sale) |

### Logs de Entrega

| Método | Endpoint | Descrição | Auth |
|--------|----------|-----------|------|
| POST | `/delivery-logs` | Criar log | ✅ (sale) |
| GET | `/delivery-logs` | Listar logs | ✅ (sale) |
| GET | `/delivery-logs/:delivery_id` | Logs de uma entrega | ✅ (sale) |

---

## 🔐 Autenticação e Autorização

### Fluxo de Autenticação

1. **Registrar usuário** → `POST /users`
2. **Fazer login** → `POST /sessions` → Recebe JWT Token
3. **Usar token** → Incluir no header `Authorization: Bearer {token}`

### Papéis de Usuário (Roles)

#### 👤 `customer` (Cliente)
- Pode se registrar
- Pode fazer login
- Pode visualizar suas próprias entregas
- **Acesso restrito** a rotas administrativas

#### 👨‍💼 `sale` (Vendedor/Gerenciador)
- Pode se registrar (com role customer por padrão)
- Pode fazer login
- Pode criar entregas
- Pode listar todas as entregas
- Pode atualizar status de entregas
- Pode registrar logs de entrega
- Acesso **total** ao sistema

### Middleware de Autenticação

```typescript
// Valida JWT Token
ensureAuthenticated

// Valida se o usuário tem a role necessária
verifyUserAuthorization(["sale"])
```

---

## 🗄️ Banco de Dados

### Schema (Prisma)

#### Modelo User
```prisma
model User {
  id       String @id @default(uuid())
  name     String
  email    String @unique
  password String
  role     UserRole @default(customer)
  
  deliveries Delivery[]
  
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

#### Modelo Delivery
```prisma
model Delivery {
  id          String @id @default(uuid())
  userId      String
  description String
  status      DeliveryStatus @default(processing)
  
  user   User           @relation(fields: [userId], references: [id])
  logs   DeliveryLog[]
  
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

#### Modelo DeliveryLog
```prisma
model DeliveryLog {
  id          String @id @default(uuid())
  description String
  deliveryId  String
  
  delivery Delivery @relation(fields: [deliveryId], references: [id])
  
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

### Enums

**UserRole:**
- `customer` - Cliente
- `sale` - Vendedor/Gerenciador

**DeliveryStatus:**
- `processing` - Em processamento
- `shipped` - Enviada
- `delivered` - Entregue

---

## 🧪 Testes

### Executar todos os testes

```bash
npm test
```

### Modo watch (desenvolvimento)

```bash
npm run test:dev
```

### Estrutura de Testes

Os testes estão localizados em `src/tests/` e cobrem:
- Controllers
- Middlewares
- Validações
- Endpoints da API

### Exemplo de Teste

```typescript
describe("DeliveriesController", () => {
  it("should create a delivery", async () => {
    const response = await request(app)
      .post("/deliveries")
      .set("Authorization", `Bearer ${token}`)
      .send({
        user_id: "550e8400-e29b-41d4-a716-446655440000",
        description: "Encomenda de teste"
      });

    expect(response.status).toBe(201);
  });
});
```

---

## 🐳 Deploy

### Com Docker

1. **Build da imagem:**

```bash
docker build -t rocketlog:latest .
```

2. **Rodar container:**

```bash
docker run -p 3000:3000 \
  -e DATABASE_URL="postgresql://..." \
  -e JWT_SECRET="sua-chave-secreta" \
  rocketlog:latest
```

### Variáveis de Ambiente para Produção

```env
NODE_ENV=production
DATABASE_URL="postgresql://user:password@host:5432/rocketlog"
JWT_SECRET="chave-secreta-super-segura-e-longa"
JWT_EXPIRES_IN="7d"
```

---

## 🤝 Contribuindo

Contribuições são bem-vindas! Para contribuir:

1. **Fork** o projeto
2. **Crie uma branch** para sua feature (`git checkout -b feature/AmazingFeature`)
3. **Commit** suas mudanças (`git commit -m 'Add some AmazingFeature'`)
4. **Push** para a branch (`git push origin feature/AmazingFeature`)
5. **Abra um Pull Request**

---

## 📝 Licença

Este projeto está sob a licença ISC. Veja o arquivo `package.json` para mais detalhes.

---

## 👤 Autor

**Allif Brun**

- GitHub: [@AllifBrun](https://github.com/AllifBrun)
- LinkedIn: [Allif Brun](https://linkedin.com/in/allifbrun)


