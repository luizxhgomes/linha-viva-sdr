# 🔐 Política de Segurança — Linha Viva

Este documento descreve a **infraestrutura de armazenamento seguro de chaves de API** e as práticas
de cybersegurança do projeto. É repositório **privado**; ainda assim, tratamos segredos com o mesmo
rigor de um repositório público — o segredo nunca vive no código.

---

## 1. Onde as chaves vivem (camadas do cofre)

O princípio é **um único lugar por ambiente, nunca no git**:

| Camada | Onde | Para quê |
|---|---|---|
| **Desenvolvimento local** | arquivo `.env` (gitignored) | rodar/testar localmente. Template em `.env.example`. |
| **Runtime do agente (CLI)** | cofre do runner — variáveis de ambiente injetadas (`.claude/settings.local.json` → `env`, gitignored) | operar Retell/n8n por API sem expor a chave |
| **Frontend / deploy (Vercel)** | Vercel → Environment Variables (encrypted at rest) | build e runtime do painel |
| **Automação (n8n)** | n8n → Credentials (referenciadas por ID, nunca por valor) | os workflows usam o **ID** da credencial, o valor fica no cofre do n8n |
| **CI/CD (GitHub)** | GitHub → Repository Secrets | pipelines, se houver |

> **Regra de ouro:** o código referencia `${VARIAVEL}` / `$env.VARIAVEL` / o **ID** da credencial —
> **jamais o valor**. O valor só existe no cofre da camada correspondente.

---

## 2. O que NUNCA é commitado

- `.env` e qualquer `.env.*` com valores (só `.env.example`, sem valores, é versionado).
- Chaves de API (Retell, ElevenLabs, OpenAI, Twilio), tokens, senhas SIP, `*.key`, `*.pem`.
- Planilhas/arquivos de cofre local (`*Chaves*.xlsx`, `*Acessos*.xlsx`).
- Endpoints que carregam token embutido na URL (ex.: MCP com token) — vão para `${N8N_MCP_URL}`.

O `.gitignore` na raiz cobre esses padrões.

---

## 3. Secret scanning (defesa em profundidade)

1. **Pré-commit / pré-push:** rodar um scanner de segredos antes de publicar. Recomendado
   [`gitleaks`](https://github.com/gitleaks/gitleaks):
   ```bash
   gitleaks detect --no-banner --redact
   ```
2. **GitHub Secret Scanning + Push Protection:** habilitados no repositório — o GitHub bloqueia o push
   se detectar um padrão de segredo conhecido.
3. **Varredura manual** (padrões deste projeto), deve retornar **zero**:
   ```bash
   grep -rnE 'sk-|Bearer [A-Za-z0-9]|AC[a-f0-9]{32}|api[_-]?key\s*[:=]\s*["'\'']?[A-Za-z0-9]{20}' .
   ```

---

## 4. Rotação de segredos

- Se um segredo for exposto (mesmo em repo privado): **rotacionar imediatamente** no provedor
  (Retell/ElevenLabs/Twilio/n8n) e atualizar o cofre da camada. Um segredo commitado fica no
  **histórico git para sempre** — rotacionar é obrigatório, não opcional.
- Endpoints com token na URL (ex.: MCP do n8n) devem ser regenerados periodicamente.

---

## 5. Reportar uma vulnerabilidade

Repositório privado de um único mantenedor. Para reportar um problema de segurança, abra uma
*issue* privada ou contate o mantenedor diretamente. Não divulgue publicamente antes da correção.
