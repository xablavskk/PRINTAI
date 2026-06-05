# Protótipo UC-07 — Aprovar Makers

**PrintAI** | Painel Administrativo

---

## TELA 1 — Login Administrativo

**Rota:** `/admin`

```
┌─────────────────────────────────────────────────┐
│  🛡  Painel Administrativo                       │
│  Acesso exclusivo para administradores PrintAI. │
│                                                 │
│  ┌─────────────────────────────────────────┐   │
│  │ E-mail                                  │   │
│  │ [ admin@printai.com                   ] │   │
│  └─────────────────────────────────────────┘   │
│                                                 │
│  ┌─────────────────────────────────────────┐   │
│  │ Senha                                   │   │
│  │ [ ••••••••                            ] │   │
│  └─────────────────────────────────────────┘   │
│                                                 │
│  [ 🛡  Entrar como Administrador            ]   │
│                                                 │
└─────────────────────────────────────────────────┘
```

**Comportamento:**
- Campos obrigatórios: E-mail e Senha
- Em caso de credenciais inválidas: exibe mensagem de erro em vermelho
- Em caso de sucesso: redireciona para o Painel de Aprovação (Tela 2)

---

## TELA 2 — Painel de Aprovação de Makers

**Rota:** `/admin` (após login)

```
┌────────────────────────────────────────────────────────────────────┐
│  Painel Administrativo                          [ Sair →  ]        │
│  Olá, Admin PrintAI · 2 solicitação(ões) pendente(s)               │
├────────────────────────────────────────────────────────────────────┤
│  [ ⏱ Pendentes ]   [ 👥 Todos os Makers ]                         │
├────────────────────────────────────────────────────────────────────┤
│                                                                    │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  Carlos Maker                          [ ⏱ Pendente ]        │  │
│  │  carlos@maker.com · +55 11 99999-0000                        │  │
│  │  CPF/CNPJ: 123.456.789-00                                    │  │
│  │  São Paulo — SP · -23.5505, -46.6333                         │  │
│  │                                                              │  │
│  │  2 serviço(s) cadastrado(s):                                 │  │
│  │  [ Impressão FDM · Filamento · PLA ]  [ Impressão SLA ]      │  │
│  │                                                              │  │
│  │  [ ✓ Aprovar ]   [ ✗ Rejeitar ]                             │  │
│  └──────────────────────────────────────────────────────────────┘  │
│                                                                    │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  Lucas Maker                           [ ✓ Aprovado ]        │  │
│  │  lucas@maker.com · +55 11 98888-2222                         │  │
│  │  CPF/CNPJ: 00.111.222/0001-33                                │  │
│  │  São Paulo — SP · -23.5616, -46.6559                         │  │
│  │                                                              │  │
│  │  1 serviço(s) cadastrado(s):                                 │  │
│  │  [ Impressão em Resina · Resina · RESINA ]                   │  │
│  └──────────────────────────────────────────────────────────────┘  │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

---

## TELA 3 — Confirmação de Rejeição (expansão do card)

Ao clicar em **Rejeitar**, o card expande com o formulário de motivo:

```
│  [ ✓ Aprovar ]   [ ✗ Rejeitar ]                                    │
│                                                                    │
│  ┌────────────────────────────────────────────────────────────┐    │
│  │ Informe o motivo da rejeição (obrigatório)...              │    │
│  │                                                            │    │
│  │                                                            │    │
│  └────────────────────────────────────────────────────────────┘    │
│                                                                    │
│  [ ✗ Confirmar Rejeição ]   [ Cancelar ]                           │
```

**Comportamento:**
- Botão "Confirmar Rejeição" fica desabilitado enquanto o campo motivo estiver vazio
- Após confirmar: card atualiza o badge para `[ ✗ Rejeitado ]` e exibe mensagem de feedback
- Após aprovar: card atualiza o badge para `[ ✓ Aprovado ]` e exibe "Maker aprovado com sucesso."

---

## ESTADOS DOS BADGES DE STATUS

| Status | Badge |
|--------|-------|
| Pendente | ⏱ Pendente (amarelo) |
| Aprovado | ✓ Aprovado (verde) |
| Rejeitado | ✗ Rejeitado (vermelho) |

---

## FILTROS DO PAINEL

| Filtro | Comportamento |
|--------|---------------|
| Pendentes | Exibe apenas Makers com `statusAprovacao` nulo |
| Todos os Makers | Exibe todos os Makers independente do status |
