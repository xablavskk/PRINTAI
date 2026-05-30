# Guia de Implantação
**Versão 1.0**
**PRINTAI - Plataforma de Impressão 3D**

---

## HISTÓRICO DE REVISÕES

| Data | Versão | Descrição | Autor |
|---|---|---|---|
| 30/05/2026 | 1.0 | Criação do documento | Equipe PRINTAI |

---

## 1. INTRODUÇÃO

Este documento descreve o processo completo de implantação do sistema PRINTAI — uma plataforma web que conecta clientes a Makers (especialistas em impressão 3D). O guia cobre todos os passos necessários para instalar, configurar e colocar o sistema em funcionamento em um novo ambiente, desde a preparação da infraestrutura até o suporte pós-implantação.

O sistema é composto por três componentes principais:

- **Frontend**: Interface web desenvolvida em React com Vite, acessada pelo navegador.
- **Backend (API)**: Servidor REST desenvolvido em Java com Spring Boot, responsável pelas regras de negócio.
- **Banco de Dados**: MySQL 8.4, responsável pela persistência dos dados.

**Definições e acrônimos:**

| Termo | Significado |
|---|---|
| API | Interface de Programação de Aplicações (o servidor backend) |
| Docker | Ferramenta que empacota e executa aplicações em contêineres isolados |
| JDK | Java Development Kit — ambiente de execução do Java |
| Maven | Ferramenta de build do projeto Java |
| Node.js | Ambiente de execução do JavaScript (necessário para o frontend) |
| Vite | Ferramenta de build do frontend React |
| MySQL | Sistema Gerenciador de Banco de Dados relacional |
| Nominatim | API gratuita do OpenStreetMap usada para converter endereços em coordenadas geográficas |

### 1.1. Referências

- Repositório principal do projeto: https://github.com/xablavskk/PRINTAI
- Repositório do código-fonte (submodulo): https://github.com/xablavskk/PRINTAI-DEV
- Docker Desktop: https://www.docker.com/products/docker-desktop
- Java JDK 17 (Adoptium): https://adoptium.net
- Node.js LTS: https://nodejs.org
- MySQL 8.4: https://dev.mysql.com/downloads/mysql
- Nominatim (geocodificação): https://nominatim.openstreetmap.org

---

## 2. PLANEJAMENTO DE IMPLANTAÇÃO

A implantação do PRINTAI envolve as seguintes atividades, executadas em sequência:

1. Preparação do ambiente (instalação de pré-requisitos)
2. Obtenção do código-fonte via Git
3. Configuração do banco de dados
4. Configuração das variáveis de ambiente
5. Build e inicialização dos serviços
6. Validação do funcionamento (testes de fumaça)
7. Treinamento dos usuários

### 2.1. Responsabilidades

| Atividade | Responsável | Observação |
|---|---|---|
| Preparar o servidor/máquina | Equipe de TI / Implantador | Instalar Docker ou JDK + Node + MySQL |
| Clonar o repositório | Implantador | Requer acesso ao GitHub |
| Configurar variáveis de ambiente | Implantador | Senhas do banco, URL da API |
| Executar o build e subir os serviços | Implantador | Via Docker ou manualmente |
| Validar o funcionamento | Implantador + Testador | Executar os testes de fumaça descritos na seção 4 |
| Treinar os usuários finais | Equipe de desenvolvimento | Clientes e Makers da plataforma |
| Reportar discrepâncias | Testador | Registrar no roteiro de testes (planilha PRINTAI - Roteiro de Teste.xlsx) |

### 2.2. Cronograma

| # | Atividade | Duração estimada | Responsável |
|---|---|---|---|
| 1 | Preparação do ambiente | 1 hora | Implantador |
| 2 | Clonagem do repositório | 15 minutos | Implantador |
| 3 | Configuração do banco e variáveis | 30 minutos | Implantador |
| 4 | Build e inicialização (Docker) | 20 minutos (1ª vez) | Implantador |
| 5 | Validação e testes de fumaça | 1 hora | Implantador + Testador |
| 6 | Treinamento dos usuários | 2 horas | Equipe de desenvolvimento |
| **Total** | | **~5 horas** | |

---

## 3. RECURSOS NECESSÁRIOS PARA IMPLANTAR O PRODUTO

### 3.1. Ambiente

O sistema pode ser implantado em dois tipos de ambiente:

**Ambiente local (desenvolvimento/homologação):**
- Qualquer computador com acesso à internet
- Sistema operacional: Windows 10/11, macOS 12+ ou Ubuntu 20.04+
- Conexão com a internet (necessária para geocodificação via Nominatim e para clonar o repositório)

**Ambiente de servidor (produção):**
- Servidor Linux (Ubuntu 22.04 LTS recomendado)
- Acesso SSH ao servidor
- Portas 80, 443, 8080 e 3306 liberadas no firewall
- Conexão com a internet para geocodificação via Nominatim

### 3.2. Hardware

**Configuração mínima (desenvolvimento/homologação):**

| Componente | Mínimo | Recomendado |
|---|---|---|
| Processador | 2 núcleos, 2 GHz | 4 núcleos, 2.5 GHz |
| Memória RAM | 4 GB | 8 GB |
| Armazenamento | 10 GB livres | 20 GB livres |
| Rede | 10 Mbps | 50 Mbps |

**Configuração mínima (produção):**

| Componente | Mínimo | Recomendado |
|---|---|---|
| Processador | 2 núcleos, 2.5 GHz | 4 núcleos, 3 GHz |
| Memória RAM | 8 GB | 16 GB |
| Armazenamento | 50 GB SSD | 100 GB SSD |
| Rede | 100 Mbps | 1 Gbps |

### 3.3. Software

**Opção A — Com Docker (recomendado):**

| Software | Versão | Fabricante | Licença | Link |
|---|---|---|---|---|
| Docker Desktop | 4.x ou superior | Docker Inc. | Gratuito (uso pessoal/educacional) | https://www.docker.com/products/docker-desktop |
| Git | 2.x ou superior | Software Freedom Conservancy | Gratuito (GPL) | https://git-scm.com |

> Com Docker, todos os demais softwares (Java, Node, MySQL) são baixados automaticamente como imagens de contêiner. Não é necessário instalá-los separadamente.

**Opção B — Sem Docker (manual):**

| Software | Versão | Fabricante | Licença | Link |
|---|---|---|---|---|
| Java JDK | 17 ou superior | Eclipse Adoptium | Gratuito (GPL) | https://adoptium.net |
| Apache Maven | 3.8+ | Apache Foundation | Gratuito (Apache 2.0) | https://maven.apache.org |
| Node.js | 18 LTS ou superior | OpenJS Foundation | Gratuito (MIT) | https://nodejs.org |
| MySQL | 8.4 | Oracle | Gratuito (GPL) / Comercial | https://dev.mysql.com/downloads/mysql |
| Git | 2.x ou superior | Software Freedom Conservancy | Gratuito (GPL) | https://git-scm.com |

**Serviços externos utilizados:**

| Serviço | Finalidade | Custo | Limite de uso |
|---|---|---|---|
| Nominatim (OpenStreetMap) | Converter endereço em coordenadas geográficas | Gratuito | 1 requisição/segundo |
| GitHub | Hospedagem do código-fonte | Gratuito | — |

### 3.4. Pessoas

Para executar a implantação são necessários os seguintes perfis:

| Perfil | Quantidade | Responsabilidade |
|---|---|---|
| **Implantador** | 1 | Responsável por preparar o ambiente, clonar o repositório, configurar variáveis e subir os serviços. Deve ter conhecimento básico de terminal/linha de comando. |
| **Testador** | 1 | Responsável por validar o funcionamento após a implantação, executando os testes de fumaça e registrando discrepâncias. |
| **Instrutor** | 1 | Responsável por treinar os usuários finais (Clientes e Makers) no uso da plataforma. Pode ser o próprio implantador. |

---

## 4. ROTEIRO DE IMPLANTAÇÃO

### Passo 1 — Verificar pré-requisitos

Abra o terminal (Prompt de Comando no Windows, Terminal no Mac/Linux) e verifique se as ferramentas estão instaladas:

```
docker -v
git --version
```

Se algum comando retornar erro, instale a ferramenta conforme indicado na seção 3.3 antes de continuar.

---

### Passo 2 — Baixar o código-fonte

Execute no terminal:

```bash
git clone --recurse-submodules https://github.com/xablavskk/PRINTAI.git
cd PRINTAI
```

> **ATENÇÃO:** O parâmetro `--recurse-submodules` é obrigatório. Sem ele o código do backend e frontend não será baixado.

Se já clonou sem esse parâmetro, execute dentro da pasta PRINTAI:

```bash
git submodule update --init --recursive
```

---

### Passo 3 — Entrar na pasta do projeto

```bash
cd 3.Implementacao/PRINTAI-DEV
```

---

### Passo 4 — Subir o sistema com Docker

```bash
docker-compose up --build
```

> Na primeira execução esse comando pode demorar entre 5 e 15 minutos, pois precisa baixar as imagens do Java, Node e MySQL e compilar o projeto. Nas execuções seguintes é muito mais rápido.

**Como saber que está pronto:**

Aguarde as seguintes mensagens no terminal:

```
printai-api  | Started PrintaiApplication in X.XXX seconds
printai-web  | ready in XXXms
```

---

### Passo 5 — Acessar o sistema

Abra o navegador e acesse:

| O que acessar | Endereço |
|---|---|
| **Aplicação web (frontend)** | http://localhost:5173 |
| **API REST (backend)** | http://localhost:8080/api |

---

### Passo 6 — Testes de fumaça (validação básica)

Execute os seguintes testes para confirmar que o sistema está funcionando:

| # | O que testar | Como testar | Resultado esperado |
|---|---|---|---|
| 1 | Frontend carrega | Abrir http://localhost:5173 no navegador | Página "Encontre seu Maker" aparece |
| 2 | API responde | Abrir http://localhost:8080/api/busca/servicos no navegador | Retorna lista JSON com serviços |
| 3 | Busca de impressoras | Abrir http://localhost:8080/api/busca/impressoras | Retorna lista JSON com impressoras |
| 4 | Dados iniciais carregados | Verificar se aparecem Makers na tela inicial | Adriano Maker e Lucas Maker aparecem no mapa |
| 5 | Cadastro de cliente | Preencher o formulário de cadastro com dados válidos | Mensagem "Cadastro realizado com sucesso!" |

---

### Passo 7 — Parar o sistema

Para parar os serviços:

```bash
docker-compose down
```

Para parar e apagar todos os dados do banco (reiniciar do zero):

```bash
docker-compose down -v
```

---

### Implantação manual (sem Docker)

Se não for possível usar Docker, siga os passos abaixo:

**Banco de dados:**

1. Instale o MySQL 8.4
2. Abra o cliente MySQL e execute:

```sql
CREATE DATABASE printai;
CREATE USER 'printai'@'localhost' IDENTIFIED BY 'printai123';
GRANT ALL PRIVILEGES ON printai.* TO 'printai'@'localhost';
FLUSH PRIVILEGES;
```

**Backend:**

1. Crie o arquivo `api/src/main/resources/application-local.properties` com:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/printai?createDatabaseIfNotExist=true&useSSL=false&allowPublicKeyRetrieval=true
spring.datasource.username=printai
spring.datasource.password=printai123
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.database-platform=org.hibernate.dialect.MySQLDialect
spring.jpa.hibernate.ddl-auto=update
```

2. Na pasta `api`, execute:

```bash
# Windows
mvnw.cmd spring-boot:run

# Mac/Linux
./mvnw spring-boot:run
```

**Frontend:**

1. Crie o arquivo `frontend/.env` com:

```
VITE_API_URL=http://localhost:8080/api
```

2. Na pasta `frontend`, execute:

```bash
npm install
npm run dev
```

---

### Problemas comuns

| Problema | Causa provável | Solução |
|---|---|---|
| "Cannot connect to the Docker daemon" | Docker Desktop não está aberto | Abrir o Docker Desktop e aguardar o ícone estabilizar |
| "Porta 8080 já está em uso" | Outro programa usa a porta | Encerrar o programa ou alterar a porta no `application.properties` |
| Frontend abre mas não carrega dados | Backend não está rodando | Verificar se http://localhost:8080/api responde |
| "npm: command not found" | Node.js não instalado | Instalar Node.js LTS em https://nodejs.org |
| Banco não conecta | MySQL não está rodando ou credenciais erradas | Verificar se o serviço MySQL está ativo e as credenciais estão corretas |
| Geocodificação não funciona | Sem acesso à internet | Verificar conexão; o cadastro é salvo sem coordenadas se o Nominatim não responder |

---

## 5. TREINAMENTO E SUPORTE

### Treinamento

O treinamento dos usuários deve cobrir os seguintes fluxos, de acordo com o perfil:

**Perfil Cliente:**
- Como se cadastrar na plataforma
- Como buscar impressoras 3D por tecnologia, material e volume
- Como visualizar o perfil completo de um Maker
- Como solicitar um pedido de impressão (com e sem modelo 3D pronto)
- Como acompanhar o status dos pedidos

**Perfil Maker:**
- Como solicitar cadastro como Maker
- Como cadastrar serviços de impressão
- Como receber e analisar pedidos de clientes

**Formato sugerido:** demonstração prática de 2 horas com acesso ao ambiente de homologação, seguida de período de uso supervisionado de 1 semana.

### Suporte

**Hardware:**
- Manter o servidor com os recursos mínimos descritos na seção 3.2
- Monitorar uso de disco, especialmente o volume do banco de dados (`printai_db_data`)

**Software:**
- Manter o Docker Desktop atualizado
- Monitorar logs do backend com: `docker logs printai-api`
- Monitorar logs do banco com: `docker logs printai-db`
- Para reiniciar apenas o backend sem perder dados: `docker-compose restart backend`

**Peopleware:**
- Canal de suporte: contato direto com a equipe de desenvolvimento via repositório GitHub (Issues)
- Para reportar bugs: abrir uma Issue em https://github.com/xablavskk/PRINTAI/issues com descrição do problema, passos para reproduzir e captura de tela
- Documentação de referência: pasta `1.Requisitos/` e `2.Analise e Design/` do repositório
