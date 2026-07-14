---
description: Configura o plugin — assinatura, preferências e conexão com a Cloudflare (roda uma vez)
---

Configure o ambiente do Prospector de Sites. Siga esta ordem:

## 1. Pasta de trabalho

Verifique se há uma pasta do usuário conectada. Se não houver, peça para conectar uma pasta (ex.: "Clientes") usando a ferramenta de solicitação de pasta — tudo (config, leads e sites criados) será salvo nela para persistir entre sessões.

## 2. Verificar config existente

Procure `prospector-config.json` na pasta conectada. Se existir, mostre um resumo (sem exibir o token) e pergunte o que o usuário quer atualizar. Se não existir, colete os dados abaixo.

## 3. Dados do usuário (perguntar via AskUserQuestion / formulário)

Colete:

- **Assinatura da proposta**: nome completo, como quer se apresentar (ex.: "Designer de páginas de alta conversão") e WhatsApp/telefone de contato.
- **Nichos padrão de prospecção**: sugira nutricionistas, psicólogos, advogados e psiquiatras como ponto de partida, mas deixe o usuário editar livremente.
- **Cidade/região padrão**.
- **Leads qualificados por busca**: padrão 10.
- **Modo de envio da proposta**: padrão "criar rascunho no Gmail para revisão" (recomendado). Alternativa: enviar direto.

## 4. Conexão com a Cloudflare

Pergunte se o usuário já tem conta na Cloudflare (gratuita, cloudflare.com).

- **Se ainda não tem**: explique brevemente que é só criar a conta (grátis, sem cartão) e que depois de criada deve voltar e rodar `/setup` de novo. Salve o config parcial e encerre.
- **Se já tem**: NÃO colete nenhum dado da Cloudflare pelo chat (nem account ID, e JAMAIS o API Token). Tudo vai num lugar só, a aba Configurações do dashboard:
  1. Instrua: abra o dashboard (`iniciar-dashboard.bat` na pasta conectada) → aba **Configurações** → seção **Conexão Cloudflare**.
  2. Lá ele preenche 2 campos: **Account ID** (dash.cloudflare.com → barra lateral direita da tela inicial, "Account ID") e o **API Token** — orientar a criar em dash.cloudflare.com → ícone de perfil → **API Tokens** → **Create Token** → template "Edit Cloudflare Workers" (cobre Pages) ou custom com escopo `Cloudflare Pages: Edit`. Clica em "Salvar conexão" → tudo vai do navegador direto pro `prospector-config.json` no computador dele, sem passar pelo chat.
  3. Peça para ele avisar quando salvar ("salvei") — aí você LÊ o config (verificando que os campos estão preenchidos, sem nunca exibir o token) e roda o teste de conexão.

  Nunca exiba, imprima ou registre o token em nenhuma saída. Se ele preferir, editar o `prospector-config.json` na mão também vale.

## 5. Salvar e testar

Salve tudo em `prospector-config.json` na pasta conectada, neste formato:

```json
{
  "assinatura": { "nome": "", "apresentacao": "", "whatsapp": "" },
  "prospeccao": { "nichos": ["nutricionistas", "psicologos", "advogados", "psiquiatras"], "cidade": "", "leadsPorBusca": 10 },
  "envio": { "modo": "rascunho" },
  "cloudflare": { "apiToken": "", "accountId": "" }
}
```

Se os dados da Cloudflare foram informados, teste a conexão seguindo a skill `deploy-cloudflare`: publique o projeto Astro mínimo de teste (`prospector-teste`) e informe a URL `https://prospector-teste.pages.dev` ao usuário. Se o teste falhar, diagnostique (token sem escopo Pages, account ID incorreto, `wrangler`/`npx` ausente no ambiente) antes de concluir. Depois do teste, pode apagar o projeto com `npx wrangler pages project delete prospector-teste --account-id="[accountId]"`.

## 6. Dashboard inicial

Siga a seção "Setup" da skill `dashboard-leads`: copie `dashboard-server.py` e `iniciar-dashboard.bat` para a raiz da pasta conectada, crie o banco `prospector.db` (schema da skill) e gere o `dashboard.html` do template. Explique ao usuário: duplo clique em `iniciar-dashboard.bat` abre o painel completo em http://localhost:8765 com edição/exclusão salvando no banco (requer Python no Windows; sem ele, o dashboard.html abre no modo leitura).

## 7. Entregar o manual

Copie da pasta do plugin para a pasta conectada (sobrescrevendo versões antigas): `manual.html` (manual do usuário) e o iniciador do dashboard certo (`iniciar-dashboard.bat` ou `.command`). Não há mais publicador local para instalar — o deploy roda direto do chat via Wrangler. Apresente o `manual.html` ao usuário com a frase: "Esse é o seu manual — guarda ele que responde 90% das dúvidas."

## 8. Encerrar

Confirme o que foi salvo e explique o ciclo (guiando SEMPRE o próximo passo ao fim de cada comando): `/prospectar` → `/redesenhar` → `/publicar` → `/proposta`, com `/editor` opcional para ajustes manuais e o `dashboard.html` como painel de controle de tudo.
