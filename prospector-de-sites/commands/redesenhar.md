---
description: Redesenha os sites dos leads com estética premium (lote de 5 ou mais), como projetos Astro
argument-hint: "[URLs ou nomes dos leads] — opcional, usa os 5+ melhores de leads.md"
---

Redesenhe as páginas dos leads seguindo a skill `redesign-premium`. Ela é obrigatória — leia a skill ANTES de escrever qualquer componente Astro.

## Seleção dos clientes

1. Leia `prospector-config.json` e `leads.md` na pasta conectada.
2. Se `$ARGUMENTS` trouxer URLs ou nomes, use-os. Senão, selecione os leads com status `novo` mais bem ranqueados — **mínimo de 5 clientes por lote** (se houver menos de 5 leads novos, use todos e avise que rodar `/prospectar` de novo aumenta o lote).
3. Confirme a lista com o usuário antes de começar.

## Para cada cliente do lote

1. **Extração**: abra o site original no Claude in Chrome (o sandbox costuma bloquear fetch direto a esses domínios). Extraia TODO o conteúdo real: textos, serviços, formação/credenciais, endereço, telefone/WhatsApp, e-mail, redes sociais, horários, paleta de cores e — OBRIGATÓRIO — as URLs reais do logo e das fotos (via JavaScript no navegador: colete `img.currentSrc` de todas as imagens; se forem lazy-load, role a página até o fim antes de coletar). Tire um screenshot do site original para referência.
2. **Redesign**: aplique a skill `redesign-premium` na íntegra. Regra de ouro: NADA inventado — é uma nova versão da página do cliente, não uma página nova. O logo original e as fotos originais DEVEM aparecer na página nova (se o cliente não tem site/logo, use composição tipográfica — nunca invente logo).
3. **Salvar** na pasta conectada, com o slug do cliente na estrutura Astro:
   - `sites/[slug]/` — o projeto Astro completo (scaffold + `src/layouts/Base.astro` + `src/components/*` + `src/pages/index.astro`), conforme a skill `redesign-premium`.
   - Rode `npm run build` dentro de `sites/[slug]/` e confirme que termina sem erro — build quebrado não é entrega.
   - Editor visual: gere a partir do `dist/index.html` pós-build injetando o script de `references/editor-visual.md` (skill `redesign-premium`), salvando como `sites/[slug]/dist-editor/index.html`. Gere SEMPRE, sem esperar o usuário pedir.
4. **Comparador (OBRIGATÓRIO — não é opcional)**: crie/atualize `comparar.html` na RAIZ da pasta conectada usando o template pronto `references/comparador-template.html` da skill `redesign-premium`: copie o template, substitua `__CLIENTES__` pelo array JSON dos clientes (formato documentado no rodapé do próprio template). Se `comparar.html` já existir, LEIA o array atual e acrescente os novos clientes no topo — nunca perca os antigos.
5. **Atualizar** o status do lead em `leads.md` para `redesenhado` e o `dashboard.html` (skill `dashboard-leads`): `status: redesenhado`.

## Checklist de saída (bloqueante)

Antes de apresentar qualquer resultado ao usuário, confirme que TODOS estes itens existem — se faltar algum, gere-o agora:

- [ ] `sites/[slug]/` com build (`npm run build`) rodando sem erro, para CADA cliente do lote
- [ ] `sites/[slug]/dist-editor/index.html` para CADA cliente do lote
- [ ] `comparar.html` na raiz, com abas para TODOS os clientes do lote

Um redesign sem build limpo, sem o editor ou sem o comparador é entrega incompleta — o usuário usa o comparador na proposta e no conteúdo dele, e o build limpo é pré-requisito do `/publicar`.

## Verificação do lote

Antes de encerrar, para cada página criada: renderize/revise o `dist/index.html` procurando textos placeholder esquecidos, links quebrados, seções vazias e problemas de contraste. Todos os CTAs devem apontar para o WhatsApp ou contato REAL do cliente.

## Saída (TRAVADA — siga exatamente este formato)

A entrega final ao usuário DEVE conter, nesta ordem, sem exceção:

1. **Cards de arquivo apresentados no chat** (via ferramenta de apresentação de arquivos): o `comparar.html` PRIMEIRO, depois o `dist-editor/index.html` de cada cliente (a página em si só existe publicada — apresente o link após `/publicar`). Se você não apresentou o card do `comparar.html`, a entrega está errada — apresente antes de escrever qualquer resumo.
2. **Resumo de 1 linha por cliente** (o que melhorou).
3. **Confirmação do dashboard**: frase explícita "Dashboard atualizado: [N] leads com status redesenhado" após atualizar o banco/dashboard conforme a skill `dashboard-leads` (se a pasta ainda não tem dashboard, CRIE-o agora pela skill — pasta nova nunca é desculpa para pular).
4. Orientação curta: `comparar.html` = antes/depois lado a lado · `dist-editor/index.html` = editar textos/imagens · próximo passo `/publicar` (builda e sobe cada projeto na Cloudflare).

É PROIBIDO encerrar a resposta sem os itens 1 e 3. Se qualquer item do checklist não existir, gere-o antes de responder.
