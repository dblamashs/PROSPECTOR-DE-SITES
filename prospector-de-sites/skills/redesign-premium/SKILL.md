---
name: redesign-premium
description: Esta skill deve ser usada ao redesenhar o site de um cliente prospectado — criar uma versão nova, premium e de alta conversão da página existente como projeto Astro estático, mantendo conteúdo, logo e paleta do cliente. Acione quando o usuário disser "redesenhar site", "melhorar página", "refazer o site do cliente" ou rodar /redesenhar ou /editor.
---

# Redesign premium de páginas (Astro estático)

Criar uma NOVA VERSÃO da página do cliente — não uma página nova. O cliente precisa reconhecer o próprio negócio, só que elevado ao padrão que o faturamento dele merece.

## Stack

Cada cliente é um projeto **Astro** (`output: 'static'`) independente em `sites/[slug]/`, pronto para `npm run build` → `dist/` → deploy na Cloudflare (skill `deploy-cloudflare`). Zero servidor, zero API routes, zero D1/KV — só HTML/CSS otimizados no build, igual à filosofia anterior de "arquivo único", só que com componentes reaproveitáveis em vez de HTML solto.

### Scaffold (uma vez por cliente novo)

```bash
mkdir -p sites/[slug] && cd sites/[slug]
npm create astro@latest . -- --template minimal --no-install --no-git --typescript strict
npm install --silent
```

Estrutura mínima esperada depois do scaffold + conteúdo:

```
sites/[slug]/
  astro.config.mjs      # output: 'static'
  package.json
  src/
    layouts/Base.astro  # <head>, meta, fonts, CSS global/tokens
    components/         # Hero, ProvaSocial, Servicos, Sobre, Oferta, Localizacao, Rodape, WhatsappFloat
    pages/index.astro   # monta a página com os components acima
  public/                # favicon; NÃO usar para fotos do cliente (ver Fotos abaixo)
```

`astro.config.mjs` mínimo:

```js
import { defineConfig } from 'astro/config';
export default defineConfig({ output: 'static' });
```

### Por que componentizar (não é só o build)

Com múltiplos clientes no mesmo padrão de nicho (nutricionistas, psicólogos, advogados, psiquiatras), os components de `src/components/` de um cliente servem de ponto de partida para o próximo do mesmo nicho — copiar a pasta `components/` inteira e só trocar os dados/textos/fotos é mais rápido e mais consistente do que escrever HTML do zero a cada lead. Ainda assim, cada seção deve ser adaptada ao conteúdo real daquele cliente — nunca reaproveitar texto ou fato de um cliente em outro.

## Regras invioláveis

1. **Nenhum FATO inventado — mas o texto deve ser APRIMORADO.** Todo serviço, credencial, número, endereço e contato vem do site original (ou do perfil do Google). Sem dados fictícios, sem depoimentos criados, sem serviços que o cliente não oferece. Porém o TEXTO não é copiado cru: reescreva com copy melhor — títulos mais fortes, frases mais claras, hierarquia de leitura — sempre dizendo a mesma verdade que o original diz.
2. **Fotos e logo originais são OBRIGATÓRIOS no site novo.** Toda foto utilizável do site existente (profissional, consultório, logo) deve constar na página nova. Colete via `img.currentSrc` no navegador, rolando a página inteira para vencer lazy-load. Baixe os arquivos para `sites/[slug]/src/assets/` (Astro otimiza imagens importadas em `src/assets` automaticamente com `astro:assets` — preferir isso a URLs externas, que podem cair). O cliente precisa se reconhecer na hora.
3. **Identidade preservada.** Manter logo, paleta de cores e fotos do cliente. Se a paleta original for fraca (ex.: cores puras saturadas), refinar os tons — nunca trocar a família de cores. Defina a paleta como CSS custom properties em `Base.astro` (tokens reaproveitáveis entre components).
4. **Mais completo que o original.** O site novo deve ser MUITO mais profissional e bem estruturado. Se o original tem poucas seções, CRIE as seções relevantes que faltam — desde que preenchidas só com informação real: prova social (nota + avaliações reais do Google), "como funciona o atendimento" (se dedutível do original), localização com mapa, horários (do perfil do Maps), FAQ com dúvidas respondíveis pelo conteúdo real. Seção que exigiria inventar fato = não criar.
5. **Responsividade TOTAL (inegociável).** A página será vista no celular do cliente E dentro da moldura da página-capa (~1000-1500px). Ela deve ser perfeita em QUALQUER largura: 360, 375, 768, 1024, 1280 e 1440px — sem rolagem horizontal, sem texto vazando, sem imagem esticada, sem seção quebrada em nenhum desses pontos. Usar grid/flex fluidos, `clamp()` para tipografia e breakpoints testados um a um. Página que quebra em alguma largura NÃO é entregue.
6. **Editor sempre.** Todo redesign gera junto uma página de edição visual — ver seção "Editor visual e comparador" abaixo. Nunca entregar sem a versão editável.
7. **Comparador sempre.** Todo lote de redesign termina com `comparar.html` na raiz da pasta conectada (fora dos projetos Astro, é estático simples), gerado a partir de `references/comparador-template.html` (substituir `__CLIENTES__` pelo array JSON; mesclar com clientes já existentes). A entrega padrão de cada cliente são: projeto Astro publicado + editor + aba no comparador.
8. **Build sempre verificado antes de considerar pronto.** Rodar `npm run build` dentro de `sites/[slug]/` e confirmar que termina sem erro antes de passar para o deploy — erro de build nunca vai para a fila de publicação.

## Estrutura da página (adaptar à profissão)

1. **Hero**: nome + especialidade, promessa clara em 1 linha, CTA primário (WhatsApp) visível sem rolar, foto do profissional/clínica.
2. **Prova social**: nota do Google em destaque ("5.0 ★ · 121 avaliações no Google") — é real e verificável. Citar 2-3 trechos de avaliações reais do Google Maps se coletados.
3. **Serviços/áreas de atuação**: cards clicáveis — cada card leva à âncora da seção detalhada ou direto ao WhatsApp com mensagem pré-preenchida (`https://wa.me/55DDDNUMERO?text=Olá! Vim pelo site e quero saber sobre [serviço]`).
4. **Sobre**: formação e credenciais reais (geram autoridade — nunca cortar).
5. **Oferta estruturada** (quando fizer sentido): transformar "agende uma consulta" em opções de engajamento (ex.: sessão pontual, acompanhamento 90 dias, plano semestral) — SEM preços, apenas nomes e o que incluem, todos levando ao WhatsApp. Só criar planos que sejam agrupamento óbvio do serviço já oferecido.
6. **Localização e contato**: endereço, mapa (iframe do Google Maps), horários, telefone, redes.
7. **Rodapé**: dados do profissional (registro de classe se existir no original).

## Copywriting (aprimorar sem inventar — reescrever é obrigatório)

O texto do site novo NUNCA é o texto do site velho colado. Reescreva tudo com técnica, dizendo apenas o que o cliente já diz/oferece:

- **Headline do hero = benefício, não rótulo.** "Nutrição esportiva em SP" é rótulo; "Seu treino merece resultados que aparecem" é headline (com o rótulo virando kicker/subtítulo pra SEO).
- **Estrutura PAS suave** ao longo da página: toque na dor real do público, mostre o caminho, apresente o serviço como solução — no tom do nicho, sem agressividade de lançamento.
- **Escaneabilidade**: ninguém lê parágrafo de 8 linhas. Quebre em blocos de 2-3 linhas, bullets com verbo, subtítulos que contam a história sozinhos (quem só lê os títulos entende a página).
- **1 CTA por dobra**, sempre orientado à ação e ao benefício ("Quero minha avaliação" > "Clique aqui"), todos pro WhatsApp com mensagem pré-preenchida contextual.
- **Prova social costurada**, não empilhada: nota do Google perto do CTA, citação real perto da seção a que se refere.
- **Microcopy**: legendas sob botões ("resposta em poucos minutos"), rótulos humanos em formulários e seções.
- Proibido: clichês vazios ("qualidade e compromisso", "excelência no atendimento") sem fato que os sustente; superlativos inventados; promessas de resultado que o cliente não faz.

## Barra de qualidade estrutural (o "profissional de verdade")

A página pronta deve parecer feita por um estúdio de design — teste honesto: colocada ao lado de um template premium do nicho (clínicas/consultórios de alto padrão), ela não pode dever nada. Isso significa: grid consistente (mesmo espaçamento entre TODAS as seções), alinhamento impecável, alternância de ritmo entre seções (fundo claro/escuro/acento, largura cheia/contida), imagens com tratamento coerente (mesmo raio de borda, mesma temperatura), tipografia com no máximo 2 famílias e escala harmônica, e nenhuma seção "órfã" que pareça colada de outro site.

## Padrão estético

- Tipografia: uma serifada elegante para títulos (Playfair Display, Fraunces, Lora) + uma sans limpa para corpo (Inter, Sora, DM Sans), pesos 400/600. Hierarquia forte: h1 ≥ 40px desktop / 30px mobile. Usar `@fontsource/*` via npm (bundlado no build, sem chamada externa em runtime) em vez de link do Google Fonts.
- Espaçamento generoso: seções com 80-120px de respiro vertical desktop; nada encostado.
- Paleta: 1 cor da marca + neutros quentes + 1 tom de destaque para CTA. Contraste AA no mínimo.
- Botão de WhatsApp flutuante fixo no canto inferior direito.
- Micro-toques premium: bordas 12-16px, sombras suaves, transições de 0.2s em hovers. Sem carrosséis, sem animações pesadas, sem JS além do essencial (o pouco JS necessário como `<script>` inline em components Astro, sem framework de UI).
- Velocidade: build estático já garante isso — mas evitar imagens não otimizadas (usar `astro:assets`) e no máximo 2 famílias de fonte.

## Checklist final (obrigatório antes de entregar)

- [ ] `npm run build` roda sem erro em `sites/[slug]/`
- [ ] Zero texto placeholder / lorem ipsum
- [ ] Todos os links e CTAs apontam para contato REAL do cliente
- [ ] Número do WhatsApp no formato wa.me correto (55 + DDD + número)
- [ ] Responsivo verificado em 360, 375, 768, 1024, 1280 e 1440px — zero rolagem horizontal e zero quebra em TODAS
- [ ] Título e meta description preenchidos com nome + especialidade + cidade (em `Base.astro`)
- [ ] Comparação com o original: todo conteúdo importante do site antigo está presente
- [ ] Logo e fotos ORIGINAIS do cliente presentes na página nova (importadas em `src/assets`)
- [ ] Editor visual gerado e `comparar.html` atualizado

## Editor visual e comparador

Como o site agora é Astro (build gera HTML estático em `dist/`), a camada de edição visual injeta o script de `references/editor-visual.md` no `dist/index.html` PÓS-build (não nos fontes `.astro`) e salva como `sites/[slug]/dist-editor/index.html` — assim o cliente edita o HTML final sem precisar rebuildar. Se o usuário salvar alterações no editor, aplique-as de volta manualmente nos componentes `.astro` correspondentes (não deixe o `dist/` como única fonte da verdade) e rode `npm run build` de novo antes do próximo deploy.

O comparador antes/depois está em `references/comparador-template.html` — substituir `__CLIENTES__` pelo array JSON e salvar como `comparar.html` na raiz da pasta conectada (mesclando com clientes existentes).
