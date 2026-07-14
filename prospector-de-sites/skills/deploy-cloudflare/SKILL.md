---
name: deploy-cloudflare
description: Esta skill deve ser usada ao publicar páginas na Cloudflare — deploy via Wrangler CLI em Cloudflare Pages, um projeto por cliente, criação da URL pública (*.pages.dev) e domínio customizado quando o cliente fecha. Acione quando o usuário disser "publicar", "subir o site", "colocar no ar", "deploy", "cloudflare" ou rodar /publicar ou o teste de conexão do /setup.
---

# Deploy na Cloudflare (Pages, um projeto por cliente)

Publicar cada site como um projeto Cloudflare Pages isolado — `https://[slug].pages.dev` — sem servidor local, sem fila, sem agendador. A API da Cloudflare é HTTPS normal e o sandbox do Cowork alcança ela direto, então o deploy inteiro roda no bash, sem ação manual do usuário.

## Credenciais

Tudo vem de `prospector-config.json` (bloco `cloudflare`): `apiToken`, `accountId`. **O token vive SÓ nesse arquivo, no computador do usuário — nunca é digitado no chat, nunca é exibido em nenhuma saída, log ou comando mostrado ao usuário.** Se estiverem vazios, oriente o usuário: dashboard → aba Configurações → seção Conexão Cloudflare → colar o token e o account ID e salvar (ou editar o arquivo na mão). Nunca pelo chat.

O token precisa da permissão **"Cloudflare Pages — Edit"** (criar via dash.cloudflare.com → My Profile → API Tokens → Create Token → template "Edit Cloudflare Workers" cobre Pages também, ou custom com escopo Pages:Edit).

## Pré-requisito de build (Astro)

O site de cada cliente é um projeto Astro estático (skill `redesign-premium`). Antes de publicar:

```bash
cd sites/[slug] && npm install --silent && npm run build
```

Isso gera `sites/[slug]/dist/` — é essa pasta que sobe para o Pages, não os fontes `.astro`.

## Publicação (via Wrangler, direto do sandbox)

```bash
export CLOUDFLARE_API_TOKEN="$(ler apiToken do config, nunca imprimir)"
npx wrangler pages deploy sites/[slug]/dist \
  --project-name=[slug] \
  --account-id="[accountId do config]" \
  --branch=main \
  --commit-dirty=true
```

1. **Primeiro deploy de um cliente**: se o projeto `[slug]` ainda não existir na conta, o comando acima já cria automaticamente (Wrangler faz `pages project create` implícito ao publicar num nome novo). Não precisa de passo separado.
2. 2. **Nome do projeto = slug do cliente**, o mesmo usado em `sites/[slug]/`. Se o slug tiver caracteres inválidos para subdomínio (acentos, underscore), normalize para minúsculas + hífen antes de usar como `--project-name`.
   3. 3. **Redeploy** (cliente pediu ajuste, ou saiu do editor visual): rode o mesmo comando de novo — Wrangler versiona automaticamente, a URL `[slug].pages.dev` não muda.
      4. 4. Se o comando falhar por token inválido/expirado, NÃO tente adivinhar ou pedir o token no chat — oriente o usuário a gerar um novo em dash.cloudflare.com e salvar via dashboard (Configurações → Conexão Cloudflare).
        
         5. ## Domínio customizado (quando o cliente fecha)
        
         6. Se o usuário já tem um domínio próprio para revender (ex.: subdomínio do seu domínio, `[slug].suaagencia.com.br`, ou o domínio do próprio cliente apontado via Cloudflare DNS):
        
         7. ```bash
            npx wrangler pages project domain add [slug] "[dominio-desejado]" --account-id="[accountId]"
            ```

            Isso só funciona se o domínio já estiver na mesma conta Cloudflare (zona DNS gerenciada por ela). Se não estiver, explique ao usuário que precisa adicionar o domínio como zona na Cloudflare primeiro (dash → Add a Site) — passo único por domínio, fora do escopo desta skill.

            ## Verificação (obrigatória, após qualquer deploy)

            1. Abra `https://[slug].pages.dev` (e o domínio customizado, se configurado) — confirme que carrega com conteúdo certo.
            2. 2. HTTPS é automático no Pages (certificado gerenciado pela Cloudflare) — não precisa de passo extra, mas confirme que carregou com cadeado válido mesmo assim.
               3. 3. Atualize `leads.md` + dashboard com status `publicado` e a URL (`urlNova` = a URL do `.pages.dev` ou domínio customizado).
                 
                  4. ## Teste de conexão do /setup
                 
                  5. Publique um projeto Astro mínimo ("Funcionou!") como `prospector-teste` pelo fluxo acima; se der certo, a URL `https://prospector-teste.pages.dev` confirma token e account ID corretos. Pode apagar o projeto de teste depois com `npx wrangler pages project delete prospector-teste --account-id="[accountId]"`.
                  6. 
