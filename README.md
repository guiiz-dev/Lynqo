# Lynqo

Projeto de feira de ciências: um site para aprender idiomas por **troca** entre pessoas do mundo todo. Você ensina o seu idioma e aprende o de outra pessoa, com **tradução automática** para facilitar a conversa.

> Repositório público, para visualização do código. Os parceiros do protótipo são perfis fictícios e as respostas do chat são simuladas por IA.

## O que tem aqui
- `public/index.html`: landing page
- `public/app.html`: protótipo (busca de parceiros por idioma e interesses, chat com tradução)
- `public/pesquisa.html`: pesquisa rápida para os visitantes
- `src/worker.js`: servidor em Cloudflare Workers (chat com a API da Anthropic, pesquisa, área da equipe com senha)
- `src/resultados.html`: página de resultados, só para a equipe
- `schema.sql`: tabelas do banco D1

## Segredos
Chaves e senhas **não** estão no código. Elas ficam em variáveis do Cloudflare (`ANTHROPIC_API_KEY`, `ADMIN_PASSWORD`) ou, localmente, no arquivo `.dev.vars`, que está no `.gitignore`. Use `.dev.vars.example` como modelo.

| `/pesquisa` | Pesquisa para os visitantes |
| `/resultados` | Resultados (pede senha: usuário qualquer, senha = `ADMIN_PASSWORD`) |

## Publicar (uma vez só)
Precisa do Node.js 20+ e de uma conta gratuita no Cloudflare. Na pasta do projeto:

1. `npm install`
2. `npx wrangler login` (abre o navegador para você entrar no Cloudflare)
3. `npx wrangler d1 create lynqo-db`
   Copie o `database_id` que aparecer e cole em `wrangler.jsonc`, no lugar de `COLE-AQUI-O-ID-DO-BANCO`.
4. `npm run db:remote` (cria as tabelas)
5. `npx wrangler secret put ANTHROPIC_API_KEY` (cole a chave de console.anthropic.com)
6. `npx wrangler secret put ADMIN_PASSWORD` (escolha uma senha forte)
7. `npm run deploy`

O site abre em `https://lynqo.SEU-USUARIO.workers.dev`, já com HTTPS. Para atualizar depois, é só rodar `npm run deploy` de novo.

## Colocar o domínio
- **Domínio novo:** em Cloudflare, menu *Domain Registration → Register Domains*. Eles vendem pelo preço de custo (`.com`, `.net`, etc.; não vendem `.com.br`).
- **Domínio que já existe (ex.: registro.br):** em Cloudflare, *Add a domain*, e troque os servidores de nome (nameservers) no registrador pelos que o Cloudflare mostrar.
- Depois: *Workers & Pages → lynqo → Settings → Domains & Routes → Add → Custom domain* e escolha o domínio. O HTTPS é automático.

## Testar no seu computador
1. Copie `.dev.vars.example` para `.dev.vars` e preencha.
2. `npm run db:local` e depois `npm run dev`. Abra http://localhost:8787

## Custos e segurança
- Cloudflare Workers e D1 têm plano gratuito suficiente para uma feira. Confira os limites atuais no painel.
- Cada mensagem do chat usa a IA e custa centavos na sua conta da Anthropic. `DAILY_LIMIT` (padrão 500/dia, em `wrangler.jsonc`) trava o gasto, e cada pessoa pode enviar no máximo 20 mensagens por minuto. Defina também um limite de gasto no console da Anthropic.
- As respostas da pesquisa ficam no D1 e não se perdem quando o site reinicia. Mesmo assim, baixe o CSV em `/resultados` ao fim da feira.
- A pesquisa não pede nome nem e-mail.
- Os parceiros são fictícios e isso aparece no protótipo. Não retire esse aviso.
