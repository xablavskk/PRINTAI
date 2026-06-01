# PRINTAI — Guia de Implantação

> **Leia tudo antes de começar.** Esse guia foi escrito para que qualquer pessoa consiga rodar o sistema do zero, mesmo sem experiência prévia com Java ou React.

---

## 1. O que é o PRINTAI?

O PRINTAI é uma plataforma web que conecta clientes a Makers (especialistas em impressão 3D). Ele é composto por três partes:

| Parte | O que faz | Porta |
|---|---|---|
| **Frontend** | Interface web (React + Vite) | 5173 |
| **Backend (API)** | Servidor Java com Spring Boot | 8080 |
| **Banco de dados** | MySQL 8.4 | 3306 |

---

## 2. Pré-requisitos

Antes de qualquer coisa, instale as ferramentas abaixo na sua máquina. Clique nos links para baixar.

### Opção A — Rodar com Docker (recomendado, mais fácil)

| Ferramenta | Para que serve | Link |
|---|---|---|
| **Docker Desktop** | Sobe tudo com um comando | https://www.docker.com/products/docker-desktop |
| **Git** | Baixar o código | https://git-scm.com/downloads |

### Opção B — Rodar manualmente (sem Docker)

| Ferramenta | Versão mínima | Link |
|---|---|---|
| **Java JDK** | 17 ou superior | https://adoptium.net |
| **Maven** | 3.8+ (ou use o `mvnw` incluso) | https://maven.apache.org/download.cgi |
| **Node.js** | 18 ou superior | https://nodejs.org |
| **MySQL** | 8.4 | https://dev.mysql.com/downloads/mysql |
| **Git** | qualquer versão recente | https://git-scm.com/downloads |

> **Como verificar se já tem instalado:** abra o terminal e digite:
> ```
> java -version
> node -v
> docker -v
> ```
> Se aparecer um número de versão, está instalado.

---

## 3. Baixar o código

Abra o terminal na pasta onde quer salvar o projeto e execute:

```bash
git clone --recurse-submodules https://github.com/xablavskk/PRINTAI.git
cd PRINTAI
```

> O `--recurse-submodules` é obrigatório. Sem ele o código do backend e frontend não vem junto.

Se já clonou sem esse parâmetro, execute dentro da pasta:

```bash
git submodule update --init --recursive
```

---

## 4. Rodar com Docker (Opção A — recomendado)

Essa é a forma mais simples. Um único comando sobe o banco, o backend e o frontend juntos.

### 4.1 Certifique-se que o Docker Desktop está aberto e rodando

O ícone do Docker deve aparecer na barra de tarefas. Se não aparecer, abra o Docker Desktop.

### 4.2 Entre na pasta do projeto

```bash
cd 3.Implementacao/PRINTAI-DEV
```

### 4.3 Suba tudo

```bash
docker-compose up --build
```

> Na primeira vez esse comando demora alguns minutos porque precisa baixar as imagens e compilar o projeto. Nas próximas vezes é mais rápido.

### 4.4 Aguarde as mensagens de inicialização

Você saberá que está pronto quando ver no terminal algo como:

```
printai-api  | Started PrintaiApplication in X seconds
printai-web  | ready in Xms
```

### 4.5 Acesse no navegador

| O que acessar | Endereço |
|---|---|
| **Aplicação web** | http://localhost:5173 |
| **API (backend)** | http://localhost:8080/api |

### 4.6 Para parar

```bash
docker-compose down
```

> Para parar E apagar os dados do banco (começar do zero):
> ```bash
> docker-compose down -v
> ```

---

## 5. Rodar manualmente (Opção B — sem Docker)

Use essa opção se não quiser instalar o Docker ou se precisar depurar o código.

### 5.1 Configurar o banco de dados MySQL

1. Abra o MySQL e execute os comandos abaixo para criar o banco e o usuário:

```sql
CREATE DATABASE printai;
CREATE USER 'printai'@'localhost' IDENTIFIED BY 'printai123';
GRANT ALL PRIVILEGES ON printai.* TO 'printai'@'localhost';
FLUSH PRIVILEGES;
```

### 5.2 Configurar o backend

Crie o arquivo `3.Implementacao/PRINTAI-DEV/api/src/main/resources/application-local.properties` com o conteúdo:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/printai?createDatabaseIfNotExist=true&useSSL=false&allowPublicKeyRetrieval=true
spring.datasource.username=printai
spring.datasource.password=printai123
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.database-platform=org.hibernate.dialect.MySQLDialect
spring.jpa.hibernate.ddl-auto=update
spring.jpa.defer-datasource-initialization=true
```

### 5.3 Iniciar o backend

Abra um terminal na pasta `3.Implementacao/PRINTAI-DEV/api` e execute:

**Windows:**
```bash
mvnw.cmd spring-boot:run
```

**Mac/Linux:**
```bash
./mvnw spring-boot:run
```

Aguarde a mensagem:
```
Started PrintaiApplication in X seconds
```

A API estará disponível em: `http://localhost:8080/api`

### 5.4 Configurar o frontend

Crie o arquivo `3.Implementacao/PRINTAI-DEV/frontend/.env` com o conteúdo:

```
VITE_API_URL=http://localhost:8080/api
```

### 5.5 Iniciar o frontend

Abra **outro terminal** na pasta `3.Implementacao/PRINTAI-DEV/frontend` e execute:

```bash
npm install
npm run dev
```

Aguarde a mensagem:
```
Local: http://localhost:5173/
```

Acesse no navegador: **http://localhost:5173**

---

## 6. Dados iniciais (seed)

O sistema já vem com dados de exemplo carregados automaticamente ao iniciar. Isso inclui:

- 2 Makers cadastrados (Adriano e Lucas)
- 2 impressoras 3D (Ender 3 e Anycubic Photon Mono)
- 2 serviços de impressão (FDM e SLA)
- 1 cliente (João)
- Tipos de impressão: Filamento, Resina e Pó
- Materiais: PLA, ABS, PETG, Resina, TPU, Nylon, ASA, PEEK, Fibra de Carbono

> Se quiser resetar os dados, pare o sistema e execute `docker-compose down -v` (Docker) ou apague e recrie o banco (manual).

---

## 7. Principais endpoints da API

| Método | Endpoint | O que faz |
|---|---|---|
| GET | `/api/busca/impressoras` | Lista impressoras com filtros |
| GET | `/api/busca/servicos` | Lista serviços de impressão |
| GET | `/api/busca/detalhe/{id}` | Detalhes de um serviço |
| POST | `/api/cliente/cadastro` | Cadastra um cliente |
| POST | `/api/maker/cadastro` | Solicita cadastro como Maker |
| POST | `/api/pedidos` | Solicita pedido de impressão |
| GET | `/api/pedidos` | Lista pedidos do cliente |
| GET | `/api/materiais` | Lista materiais disponíveis |
| GET | `/api/tipos` | Lista tipos de impressão |

> Para testar os endpoints diretamente, use o [Postman](https://www.postman.com) ou o [Insomnia](https://insomnia.rest).

---

## 8. Problemas comuns

### "Porta 8080 já está em uso"
Algum outro programa está usando a porta. Encerre-o ou mude a porta no `application.properties`:
```properties
server.port=8081
```
E atualize o `.env` do frontend para apontar para a nova porta.

### "Cannot connect to the Docker daemon"
O Docker Desktop não está aberto. Abra-o e aguarde o ícone ficar estável na barra de tarefas.

### "npm: command not found"
Node.js não está instalado. Baixe em https://nodejs.org e instale a versão LTS.

### Frontend abre mas não carrega dados
Verifique se o backend está rodando em `http://localhost:8080`. Abra essa URL no navegador — deve aparecer uma resposta JSON ou erro 404 (não uma página em branco).

### Banco não conecta (manual)
Confirme que o MySQL está rodando e que o usuário `printai` foi criado corretamente. Teste a conexão com:
```bash
mysql -u printai -pprintai123 -h localhost printai
```

---

## 9. Estrutura de pastas

```
PRINTAI/
├── 1.Requisitos/          → Documentos de requisitos e casos de uso
├── 2.Analise e Design/    → Diagramas UML e arquitetura
├── 3.Implementacao/
│   └── PRINTAI-DEV/
│       ├── api/           → Backend Java (Spring Boot)
│       │   └── src/main/java/com/printai/
│       │       ├── controller/   → Endpoints REST
│       │       ├── service/      → Regras de negócio
│       │       ├── repository/   → Acesso ao banco
│       │       ├── model/        → Entidades JPA
│       │       └── dto/          → Objetos de transferência
│       ├── frontend/      → Interface web (React + Vite)
│       │   └── src/
│       │       ├── pages/        → Páginas da aplicação
│       │       ├── components/   → Componentes reutilizáveis
│       │       ├── hooks/        → Lógica de estado
│       │       └── services/     → Chamadas à API
│       └── docker-compose.yml
└── 4.Teste/               → Roteiros e planilhas de teste
```

---

## 10. Contato e suporte

Em caso de dúvidas sobre o projeto, consulte os documentos em `1.Requisitos/` e `2.Analise e Design/` ou entre em contato com a equipe de desenvolvimento.
