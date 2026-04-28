# Guia de Sincronização: PRINTAI ↔ PRINTAI-DEV

Este guia explica como manter o repositório principal (Gerenciamento) e o submódulo (Código) em harmonia.

---

## 1. Fluxo de Trabalho Diário (Desenvolvendo)

Quando você altera o código (Java ou React), você está trabalhando dentro do repositório `PRINTAI-DEV`.

1. **Entre na pasta do código:**
   ```powershell
   cd 3.Implementacao/PRINTAI-DEV
   ```
2. **Faça suas alterações e commite no submódulo:**
   ```powershell
   git add .
   git commit -m "feat: sua mensagem de alteração"
   git push origin develop
   ```
3. **Volte para o repositório principal e salve a nova versão:**
   ```powershell
   cd ../..
   git add 3.Implementacao/PRINTAI-DEV
   git commit -m "chore: atualiza ponteiro do submódulo"
   git push
   ```

---

## 2. Sincronizando Alterações (Puxando Novidades)

Se você ou outra pessoa subiu código no `PRINTAI-DEV` e você quer trazer para o projeto principal:

1. **Na raiz do projeto principal:**
   ```powershell
   # Busca a última versão da branch 'develop' do submódulo (conforme configuramos)
   git submodule update --remote --merge
   ```
2. **Salve essa atualização no repositório principal:**
   ```powershell
   git add 3.Implementacao/PRINTAI-DEV
   git commit -m "chore: sincroniza com as últimas do develop"
   git push
   ```

---

##  3. Clonando o Projeto do Zero

Se você clonar o repositório `PRINTAI` em uma máquina nova:

```powershell
# Clona o principal e já baixa o código do submódulo automaticamente
git clone --recursive https://github.com/xablavskk/PRINTAI.git
```

*Se esqueceu do `--recursive`, rode isso dentro da pasta:*
```powershell
git submodule update --init --recursive
```

---

## 4. Comandos de "Emergência"

| Situação | Comando |
| :--- | :--- |
| **Pasta do código sumiu ou está vazia** | `git submodule update --init --recursive` |
| **Erro ao trocar de branch** | `Rename-Item` na pasta do submódulo (como fizemos hoje) |
| **Saber em qual commit o submódulo está** | `git submodule status` |

---

> **Dica:** O repositório principal guarda apenas **O LINK** (commit hash) para o submódulo. Sempre que você dar um `push` dentro de `PRINTAI-DEV`, lembre-se de avisar ao repositório `PRINTAI` que o link mudou!
