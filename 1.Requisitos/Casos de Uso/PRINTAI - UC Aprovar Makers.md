# PRINTAI — História de Usuário
**Versão 1.0**

---

## 1. CASO DE USO: UC-07 – APROVAR MAKERS

---

## 2. NÚMERO DA HISTÓRIA: US-07

---

## 3. ESTIMATIVA

| Data Início | Data Fim | Horas | Valor | Risco | Complexidade Técnica |
|---|---|---|---|---|---|
| 27/05/2026 | 03/06/2026 | 20 | 6 | 4 | 5 |

---

## 4. DESCRIÇÃO DA HISTÓRIA

Como um Administrador da plataforma, eu quero visualizar as solicitações de cadastro de Makers pendentes e aprovar ou rejeitar cada uma, de modo que somente Makers verificados possam oferecer serviços de impressão 3D na plataforma.

---

## 5. TESTES DE ACEITAÇÃO

**Pré-condição:** Usuário autenticado com perfil ADMINISTRADOR (header `X-Admin-Id` válido). Existir ao menos um Maker com `statusAprovacao` nulo (pendente) cadastrado no sistema.

| Nr. | Funcionalidade/Comportamento | Entradas | Resultado Esperado |
|-----|------------------------------|----------|--------------------|
| 01 | Listar Makers pendentes de aprovação | `X-Admin-Id` de Administrador válido | HTTP 200 — lista de Makers com `statusAprovacao` nulo |
| 02 | Aprovar um Maker | `X-Admin-Id` válido, `makerId` válido, `aprovado: true` | HTTP 200 — Maker com `statusAprovacao: true` |
| 03 | Rejeitar um Maker com motivo | `X-Admin-Id` válido, `makerId` válido, `aprovado: false`, `motivoRejeicao: "Documentação inválida"` | HTTP 200 — Maker com `statusAprovacao: false` |
| 04 | Rejeitar um Maker sem informar motivo | `aprovado: false`, `motivoRejeicao` vazio | HTTP 422 — erro: "Informe o motivo da rejeição" |
| 05 | Tentar aprovar Maker já processado | `makerId` de Maker já aprovado ou rejeitado | HTTP 422 — erro: "Esta solicitação já foi processada" |
| 06 | Tentar aprovar com `X-Admin-Id` de usuário não administrador | `X-Admin-Id` de um Cliente ou Maker | HTTP 422 — erro: "Acesso restrito para administradores" |
| 07 | Tentar aprovar com `X-Admin-Id` ausente | Header `X-Admin-Id` não enviado | HTTP 400 — erro de header obrigatório ausente |
| 08 | Buscar Maker inexistente | `makerId` que não existe no banco | HTTP 422 — erro: "Maker não encontrado" |
| 09 | Listar todos os Makers (aprovados e pendentes) | `X-Admin-Id` válido | HTTP 200 — lista completa de Makers independente do status |
| 10 | Campo `aprovado` nulo na requisição | `aprovado: null` | HTTP 400 — erro de validação: "O campo 'aprovado' é obrigatório" |

---

## 6. ENDPOINTS IMPLEMENTADOS

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| POST | `/api/admin/login` | Login do Administrador |
| GET | `/api/admin/makers/pendentes` | Lista Makers com aprovação pendente |
| GET | `/api/admin/makers` | Lista todos os Makers |
| GET | `/api/admin/makers/{makerId}` | Busca detalhes de um Maker específico |
| PATCH | `/api/admin/makers/{makerId}/processar` | Aprova ou rejeita um Maker |

---

## 7. TEM PROTÓTIPO?

Painel administrativo implementado na rota `/admin` do frontend (`AdminPanel.jsx`), acessível após login com perfil ADMINISTRADOR. Exibe listagem de Makers pendentes com botões de aprovar e rejeitar, incluindo campo de motivo de rejeição.
