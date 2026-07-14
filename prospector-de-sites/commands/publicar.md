---
description: Publica as páginas redesenhadas na Cloudflare (Pages) e retorna as URLs públicas
argument-hint: "[nome do cliente ou todos]"
---

Publique páginas na Cloudflare seguindo a skill `deploy-cloudflare`.

## Passos

1. Leia `prospector-config.json`. Se os dados da Cloudflare (`apiToken`, `accountId`) não estiverem preenchidos, oriente o usuário a preenchê-los pelo dashboard (aba Configurações → Conexão Cloudflare) — nunca colete o token pelo chat. Não prossiga sem eles.
2. Determine o que publicar: `$ARGUMENTS` (um cliente ou "todos"), ou liste as páginas com status `redesenhado` em `leads.md` e pergunte.
3. **Gere a página-capa de cada cliente**: preencha `references/capa-proposta-template.html` (skill `proposta-email`) com os dados do lead + assinatura do config e salve como `sites/[slug]/public/proposta.html` — dentro de `public/` do próprio projeto Astro, o build copia o arquivo sem alterar e ele fica acessível em `[slug].pages.dev/proposta.html` no mesmo deploy, sem publicação separada.
4. **Publique seguindo a skill `deploy-cloudflare`**, para cada cliente: `npm run build` dentro de `sites/[slug]/` (confirme que terminou sem erro), depois `npx wrangler pages deploy sites/[slug]/dist --project-name=[slug] --account-id="[accountId]"`. Token lido do config, nunca exibido.
5. **Verificação (bloqueante)**: abra `https://[slug].pages.dev` e `https://[slug].pages.dev/proposta.html` e confirme que ambas carregam com conteúdo certo e HTTPS válido (automático no Pages). Se o build falhou, corrija o erro antes de tentar publicar de novo — nunca suba um `dist/` de um build quebrado.
6. Atualize `leads.md` e o banco do dashboard: status `publicado` + URL pública nova (`urlNova`).

## Saída

Liste, por cliente: URL da página nova (`[slug].pages.dev`) e URL da capa (`[slug].pages.dev/proposta.html`), ambas testadas em https. Sugira o próximo passo: `/proposta` para enviar os e-mails.
