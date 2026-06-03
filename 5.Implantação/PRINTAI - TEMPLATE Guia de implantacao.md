# PRINTAI — Guia de Implantação

## Histórico de Revisões

| Data | Versão | Descrição | Autor |
| --- | --- | --- | --- |
| 03/06/2026 | 1.0 | Versão inicial do Guia de Implantação da plataforma PRINTAI. | Equipe de Desenvolvimento |

---

## 1. Introdução

Este documento fornece uma visão geral e as instruções necessárias para a implantação completa do **PRINTAI**, uma plataforma web que conecta clientes finais a Makers (especialistas em impressão 3D). O guia foi estruturado para orientar o processo de configuração do ambiente, instalação de dependências, carga inicial de dados e execução do sistema a partir do código-fonte.

### 1.1. Referências

* Documentação de Requisitos do Sistema (`1.Requisitos/`)
* Diagramas de Arquitetura e Modelagem (`2.Analise e Design/`)
* Repositório Principal e Submódulos Git (`https://github.com/xablavskk/PRINTAI.git`)

---

## 2. Planejamento de Implantação

A implantação do PRINTAI pode ser executada de duas formas estratégicas: através de orquestração automatizada de containers (via Docker Compose, recomendado para homologação e desenvolvimento ágil) ou através de instalação e configuração modular nativa (passo a passo manual). O processo engloba a preparação da infraestrutura, validação de portas de rede e carga dos scripts de banco de dados.

### 2.1. Responsabilidades

* **Equipe de Desenvolvimento/Implantação:** Responsável por clonar o repositório garantindo a integridade dos submódulos, configurar as variáveis de ambiente (`.env` e `application.properties`) e executar os scripts de população inicial (seed).
* **Equipe de Infraestrutura/Cliente:** Responsável por garantir a liberação das portas lógicas locais e o provisionamento dos recursos mínimos de hardware/software detalhados na seção 3.

### 2.2. Cronograma

A execução das atividades segue o fluxo lógico abaixo:

1. **Provisionamento e Validação de Requisitos:** Instalação do Docker ou ferramentas nativas (Duração estimada: 20 min).
2. **Clonagem e Inicialização do Ambiente:** Download do código-fonte via Git e build inicial dos módulos (Duração estimada: 15 min).
3. **Carga e População do Banco de Dados:** Execução das migrations do JPA e rodada do script SQL de sementes (Duração estimada: 5 min).
4. **Testes de Aceitação Local:** Validação de conectividade entre as camadas Frontend, Backend e Banco de Dados (Duração estimada: 10 min).

---

## 3. Recursos Necessários para Implantar o Produto

### 3.1. Ambiente

O ambiente de execução precisa permitir o tráfego de rede local nas seguintes portas padrão:

* **Porta 5173:** Tráfego HTTP do servidor web do Frontend (Vite).
* **Porta 8080:** Requisições REST da API do Backend (Spring Boot).
* **Porta 3306:** Conexão nativa com o Sistema Gerenciador de Banco de Dados (MySQL).

### 3.2. Hardware

* **Processador:** Mínimo Dual-Core 2.0 GHz ou superior.
* **Memória RAM:** Mínimo de 8 GB (Recomendado 16 GB caso opte por rodar via Docker Desktop junto a IDEs de desenvolvimento).
* **Espaço em Disco:** Mínimo de 5 GB livres para armazenamento das imagens Docker, dependências do Maven (`.m2`), módulos do Node (`node_modules`) e volume do banco de dados.

### 3.3. Software

Dependendo da estratégia de implantação escolhida, os requisitos de software dividem-se em:

#### Opção A — Cenário com Docker (Recomendado)

* **Docker Desktop** (versão estável recente)
* **Git** (versão de linha de comando ou cliente visual)

#### Opção B — Cenário de Instalação Manual (Nativa)

* **Sistema Operacional:** Windows, Linux ou macOS.
* **Java JDK:** Versão **21 (LTS)** especificamente *(Atenção: Versões anteriores não são suportadas e 22+ gera incompatibilidade com as anotações Lombok).*
* **Maven:** Versão 3.9+ (ou utilização do empacotador `mvnw` embutido).
* **Node.js:** Versão **20 ou superior** (LTS).
* **MySQL Server:** Versão 8.4.

### 3.4. Pessoas

Para realizar a implantação do sistema, é necessário o envolvimento de:

* **1 Administrador de Ambiente / Implantador:** Com conhecimentos básicos em comandos de terminal (CLI), execução de scripts SQL e ferramentas Git.

---

## 4. Roteiro de Implantação

### Passo 1: Download do Código-Fonte (Comum a ambos os cenários)

Abra o terminal do sistema operacional na pasta destino de preferência e execute o comando abaixo para baixar o projeto principal e seu submódulo core estrutural (`PRINTAI-DEV`):

```bash
git clone --recurse-submodules https://github.com/xablavskk/PRINTAI.git
cd PRINTAI

```

> *Nota: Caso o repositório tenha sido clonado sem o parâmetro `--recurse-submodules`, use o comando abaixo dentro da pasta do projeto para corrigir:*

```bash
git submodule update --init --recursive

```

---

### Passo 2: Inicialização da Infraestrutura e Serviços

#### CENÁRIO A: Implantação via Docker (Automatizado)

1. Certifique-se de que o **Docker Desktop** está aberto e com o daemon ativo na máquina.
2. Navegue até a pasta correspondente ao submódulo de desenvolvimento:
```bash
cd 3.Implementacao/PRINTAI-DEV

```


3. Execute o comando para compilar e subir todos os containers de forma orquestrada:
```bash
docker-compose up --build

```


4. Aguarde até que o terminal exiba a mensagem de sucesso da API Java: `Started PrintaiApplication in X seconds`. Os serviços estarão acessíveis em `http://localhost:5173` (Frontend) e `http://localhost:8080/api` (Backend).

#### CENÁRIO B: Implantação Manual e Modular (Sem Docker)

**1. Instanciação do Banco de Dados:**
Acesse o terminal ou ferramenta cliente do seu MySQL e execute o bloco SQL abaixo para criar o esquema e a conta de serviço do sistema:

```sql
CREATE DATABASE printai CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'printai'@'localhost' IDENTIFIED BY 'printai123';
GRANT ALL PRIVILEGES ON printai.* TO 'printai'@'localhost';
FLUSH PRIVILEGES;

```

**2. Configuração do Perfil de Ambiente do Backend:**
Valide se as propriedades do arquivo `3.Implementacao/PRINTAI-DEV/api/src/main/resources/application-local.properties` estão apontando corretamente para a credencial criada:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/printai?useSSL=false&createDatabaseIfNotExist=true&allowPublicKeyRetrieval=true&serverTimezone=America/Sao_Paulo&characterEncoding=UTF-8
spring.datasource.username=printai
spring.datasource.password=printai123
spring.jpa.hibernate.ddl-auto=update

```

**3. Inicialização da API Java (Spring Boot):**
Abra um terminal exclusivo na pasta `3.Implementacao/PRINTAI-DEV/api` e execute conforme o sistema operacional:

* *Windows:* `mvnw.cmd spring-boot:run -Dspring-boot.run.profiles=local`
* *Mac/Linux:* `./mvnw spring-boot:run -Dspring-boot.run.profiles=local`

**4. Carga dos Dados de Referência (Seed):**
Após o Spring Boot criar a estrutura de tabelas automaticamente na primeira inicialização, abra um novo terminal e execute o script de sementes para popular os domínios fixos:

```bash
mysql -uprintai -pprintai123 printai < 3.Implementacao/PRINTAI-DEV/api/src/main/resources/db/seed.sql

```

**5. Configuração e Inicialização do Frontend (React):**
Crie um arquivo chamado `.env` dentro do diretório `3.Implementacao/PRINTAI-DEV/frontend/` com a seguinte variável:

```env
VITE_API_URL=http://localhost:8080/api

```

Em seguida, abra um terminal específico na pasta do frontend e execute os comandos de instalação e inicialização do ecossistema Node:

```bash
npm install
npm run dev

```

---

### Passo 3: Verificação e Validação pós-implantação

Abra o navegador web e valide os seguintes acessos pontuais:

* **Interface do Usuário:** `http://localhost:5173`
* **Health Check / Teste de API:** `http://localhost:8080/api/tipos` (Deve retornar uma estrutura JSON com os tipos de impressão cadastrados pelo seed).

---

## 5. Treinamento e Suporte

### 5.1. Carga de Dados Inicial e Operação básica

O sistema sob o perfil `dev` (Docker) carrega em sua inicialização dados pré-definidos essenciais para a operação da plataforma, divididos da seguinte forma:

* **Tipos de Impressão:** Filamento, Resina, Pó.
* **Tecnologias Relacionadas:** FDM, MJF, SLA, DLP, SLS, Binder Jetting.
* **Materiais Suportados:** PLA, ABS, PETG, Resina, TPU, Nylon, ASA, PEEK, Fibra de Carbono.

> *Nota operacional:* Dados dinâmicos como cadastro de Perfis de Makers, Impressoras e Serviços não utilizam mais dados estáticos (mocks). Estes devem ser validados de forma operacional diretamente pela interface do usuário através do fluxo contido na rota `/maker/cadastro` ("Seja um Maker").

### 5.2. Resolução de Problemas Conhecidos (Suporte Técnico)

* **Porta 8080 ou 5173 já alocada:** Finalize o processo concorrente na sua máquina ou mude a propriedade `server.port` no `application.properties` correspondente, lembrando de atualizar a nova porta no `.env` do Frontend.
* **Falha de Conectividade com o Docker Daemon:** O Docker Desktop não foi iniciado ou travou na barra de tarefas. Reinicie o serviço do Docker.
* **Dados de Referência em Branco/Vazios:** No cenário manual, certifique-se de disparar a query de importação do arquivo `seed.sql` detalhada no Roteiro de Implantação.
* **Erros de Inicialização de Banco no Docker:** Caso precise limpar o estado do banco de dados nos containers para uma nova implantação limpa, utilize:
```bash
docker-compose down -v
docker-compose up --build

```



---

## 6. Estrutura de Pastas de Referência

A distribuição dos artefatos no diretório raiz do projeto segue o mapeamento abaixo para fins de auditoria e manutenção do ambiente implantado:

```
PRINTAI/
├── 1.Requisitos/          → Documentos de especificações e casos de uso
├── 2.Analise e Design/    → Modelagens e arquitetura de software
├── 3.Implementacao/
│   └── PRINTAI-DEV/       → Submódulo core do código-fonte
│       ├── api/           → API Backend (Spring Boot 3.4.1 / Java 21)
│       │   └── src/main/resources/db/seed.sql  → Script de carga de dados
│       ├── frontend/      → Interface Web (React 19 + Vite 8)
│       └── docker-compose.yml → Arquivo de orquestração do ambiente conteinerizado
├── 4.Teste/               → Planilhas e roteiros de validação de qualidade
└── 5.Implantação/         → Documentação de implantação do sistema

```