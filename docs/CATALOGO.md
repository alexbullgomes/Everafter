# Catálogo de Produtos e Pacotes — Everafter

> **Snapshot de 18 de agosto de 2026.**
> Os dados deste documento foram lidos **ao vivo** da base Supabase de produção (tabelas `products`, `campaign_packages`, `promotional_campaigns`, `promotional_campaign_products`), **não** do repositório. As migrations sobem a tabela `products` vazia — o catálogo real é populado inteiramente pelo admin.
>
> **Este documento envelhece.** Qualquer edição feita em `/dashboard` → Products ou em Campanhas altera o catálogo sem alterar este arquivo. Veja [Como atualizar](#como-atualizar-este-documento) no fim.

**Moeda: USD.** Não existe nenhum valor em BRL em lugar nenhum do sistema. Todos os nomes de produto estão em inglês no ar e foram mantidos **verbatim** — traduzir aqui criaria divergência com o site e com o Stripe.

### Convenção de marcação

| Marca | Significado |
|---|---|
| ✅ | Agrupamento **confirmado no banco** — o produto está ligado a essa campanha via `promotional_campaign_products` |
| ⚠️ | Agrupamento **inferido editorialmente** — o produto não está ligado a campanha nenhuma; a categoria foi deduzida do nome, descrição e entregável |
| 🔒 | Pertence a uma campanha **inativa/oculta**, não legível publicamente |

---

## 1. Visão geral

**34 produtos ativos** (`is_active = true` em todos), de **$250** a **$5.000**.

| Categoria | Produtos | Faixa de preço |
|---|---:|---|
| 💍 Casamento & Casais | 17 | $250 – $5.000 |
| 🏢 Business | 8 | $350 – $1.550 |
| 👨‍👩‍👧 Família | 7 | $250 – $650 |
| 🚁 Drone Light Shows | 2 exclusivos (+1 compartilhado) | $500 |

### Os três sistemas que coexistem

A Everafter vende através de **três mecanismos diferentes**, e confundi-los é o erro mais fácil de cometer:

1. **Produtos** (`products`) — o catálogo real, 34 itens. No checkout cobra o **valor cheio**.
2. **Pacotes de campanha** (`campaign_packages`) — 15 cartões de vitrine dentro das landing pages `/promo/*`. O preço exibido é **texto livre de marketing** (`"Starting at $2200"`, `"Personalize"`); no checkout cobra **apenas um depósito de $150**.
3. **Quiz de casamento** (`/weddingquiz`) — 6 pacotes **hardcoded no código**, exibidos com selo "Estimated Price". **Não vendem nada** — não passam pelo checkout, servem só para captar lead.

> ⚠️ **Ponto comercial crítico:** produto e pacote de campanha têm preços que não se conversam. "The Celebration" custa **$2.200** como produto, enquanto o pacote "The Ever After Experience" anuncia **"Starting at $2200"** mas cobra só **$150** de depósito. Veja a [seção 7](#7-como-a-cobrança-realmente-funciona).

---

## 2. 💍 Casamento & Casais

A linha principal é a família **"The ___"** — 11 produtos em três degraus de cobertura. É o produto mais bem estruturado do catálogo, mas **apenas 3 dos 11 estão ligados a alguma campanha** — o resto não aparece em nenhuma landing page.

### 2.1 Cobertura completa de cerimônia (foto + vídeo)

| Produto | Preço | Cobertura | Entregável | Campanha |
|---|---:|---|---|---|
| **The Heirloom** | $5.000 | 8h | 500 fotos + doc completo + vídeo 5min + teaser 1min | ⚠️ nenhuma |
| **The Signature** | $3.800 | 5h | 300 fotos + doc completo + reel 4min + teaser 45s | ✅ `wedding-packages` |
| **The Celebration** | $2.200 | 3h | 170 fotos + doc completo + highlight 4min + sessão externa | ✅ `wedding-packages` |
| **The Ceremony** | $1.800 | 2h | 120 fotos + doc completo + vídeo 3min · 1 fotógrafo + 1 filmmaker | ⚠️ nenhuma |
| **The Vow** | $1.250 | 2h | 75 fotos + doc completo + highlight 2min · 1 fotógrafo + 1 filmmaker | ⚠️ nenhuma |

### 2.2 Sessões de casal — foto + vídeo

| Produto | Preço | Cobertura | Entregável | Campanha |
|---|---:|---|---|---|
| **The Keepsake** | $1.150 | 1h30 | 75 fotos + vídeo 90s · até 3 locações (até 25min de deslocamento) | ⚠️ nenhuma |
| **The Promise** | $750 | 1h | 50 fotos editadas + highlight 1min · 1–2 locações (até 20min) | ✅ `wedding-packages` |
| **Photo + Romantic Reel Session** | $550 | 1h30 | 25 fotos + reel 30s para Instagram/TikTok | 🔒 campanha oculta |
| **The Prelude** | $550 | 45min | 30 fotos + reel 30s · 1 locação | ⚠️ nenhuma |
| **Couple Cinematic Session** | $450 | 2h | vídeo de 1min | ⚠️ nenhuma |

### 2.3 Sessões só foto

| Produto | Preço | Cobertura | Entregável | Campanha |
|---|---:|---|---|---|
| **The Story** | $1.000 | 2h | 120 fotos editadas · até 3 locações | ⚠️ nenhuma |
| **The Memory** | $550 | 1h | 50 fotos editadas · 1–2 locações | ⚠️ nenhuma |
| **Valentine’s Best Moments** | $360 | 1h | 30 fotos — mix romântico e lifestyle | 🔒 campanha oculta |
| **The Portrait** | $300 | 45min | 30 fotos editadas · 1 locação | ⚠️ nenhuma |
| **Couple Mini Session** | $250 | 40min | 10 fotos — "perfeito para o Valentine's Day" | 🔒 campanha oculta |
| **Couple Photoshoot Session** | $250 | 1h | 40 fotos | ⚠️ nenhuma |

### 2.4 Pedido de casamento

| Produto | Preço | Cobertura | Entregável | Campanha |
|---|---:|---|---|---|
| **Surprise Proposal Drone Experience** | $500 | 10–15min | 200–600 drones sincronizados ao vivo — "Will You Marry Me?" no céu | ✅ `drone-light-shows` |

### 2.5 Pacotes de vitrine — campanha `/promo/wedding-packages`

Estes **não são produtos**; são cartões da landing page. Todos cobram **$150 de depósito**, independentemente do preço anunciado.

| Pacote | Preço exibido | Depósito real | Destaque |
|---|---|---:|---|
| **The Ever After Experience** | Starting at $2200 | $150 | ⭐ marcado como popular |
| **The Timeless** | Starting at $1200 | $150 | |
| **The Legacy Album** | Starting at $850 | $150 | |

- **The Ever After Experience** — "Complete coverage with both photography and videography". Preparativos de noiva e noivo, cerimônia e recepção · cobertura de até 12h · filme cinematográfico de 8–12min + teaser de 1,5min · até 800 fotos editadas em alta resolução. *Ideal para: casais que querem cobertura completa.*
- **The Timeless** — "Cinematic video production for your event". Cobertura de até 8h · filme highlight cinematográfico · teaser de 1,5min · entrega em 7 dias úteis · 2 filmmakers.
- **The Legacy Album** — "Professional photography capturing your special moments". Até 12h de cobertura · preparativos de noiva e noivo · cerimônia e recepção · até 800 fotos em alta resolução.

> ⚠️ **Incoerência de dados:** as descrições curtas destes três pacotes ainda são os textos genéricos herdados do template original ("Professional photography capturing your special moments"), que não correspondem ao conteúdo real listado nas features. O Legacy Album é descrito como pacote de foto, mas nada indica isso além da lista de features.

---

## 3. 🏢 Business

Oito produtos. O núcleo é a linha de **conteúdo para marca** a $950, que existe em três versões com **entregável e preço idênticos**, diferenciadas apenas pelo público-alvo no título.

| Produto | Preço | Cobertura | Entregável | Campanha |
|---|---:|---|---|---|
| **Event Package Coverage** | $1.550 | 4h | 75 fotos + vídeo 90s | ✅ `bussines-content`, `premium-vendor-partnership` |
| **Signature Content Session** | $950 | 3h | 2 reels (30–45s cada) + 10 fotos sociais | ✅ `premium-vendor-partnership` |
| **Wedding Planner Signature Content** | $950 | 3h | 2 reels (30–45s cada) + 10 fotos sociais | ✅ `premium-vendor-partnership` |
| **Wedding Catering Content** | $950 | 3h | 2 reels (30–45s cada) + 10 fotos sociais | ✅ `premium-vendor-partnership` |
| **Food & beverage vendors** | $650 | 1h | 1 reel (30–45s) + 10 fotos sociais | ✅ `bussines-content`, `premium-vendor-partnership` |
| **Business & Product Shooting** | $650 | 3h | 40 fotos + vídeo de 1min | ⚠️ nenhuma |
| **Corporate Headshots Session** | $350 | 40min | 15 fotos | ✅ `bussines-content`, `photo-services` |
| **Brand Mini Session** | $350 | 1h30 | 30 fotos | ⚠️ nenhuma |

**Descrições em uso:**
- Os quatro produtos de conteúdo ($950 e $650) compartilham a mesma descrição: *"High-impact visual content created for marketing, social media, and ads to boost your sales."* Todos levam o selo **"Best Price"**.
- **Event Package Coverage** — *"Professional photo and video coverage designed for corporate events, brand activations, conferences, and private business gatherings."*
- **Corporate Headshots Session** — *"Clean and modern headshots designed for LinkedIn, websites and corporate profiles."*
- **Brand Mini Session** — *"Professional branding photos to elevate your business presence online."*

### 3.1 Pacotes de vitrine — campanha `/promo/premium-vendor-partnership`

A campanha mais vista do site (**101 visualizações**). Programa de parceria para vendors e marcas.

| Pacote | Preço exibido | Depósito real | Destaque |
|---|---|---:|---|
| **Brand Content Package** | Personalize | $150 | ⭐ marcado como popular |
| **Brand Video Content** | Starting at $450 | $150 | |
| **Brand Photography Content** | Starting at $250 | **$100** | ← único depósito diferente de $150 em todo o sistema |

- **Brand Photography Content** — *"Ideal for who's building or refreshing their brand image."* Fotografia focada em marca para site e redes · imagens editadas profissionalmente · visuais para mostrar seu trabalho · conteúdo reutilizável em várias plataformas.
- **Brand Content Package** — *"Best combo to elevate your brand with cohesive photo and video content."* Vídeo cinematográfico para visibilidade de marca · clipes curtos para Instagram e TikTok · visuais narrativos que constroem confiança · edição focada em emoção e conexão.
- **Brand Video Content** — *"For businesses focused on video-first marketing."* Mesmas features do anterior, com ênfase em vídeo.

### 3.2 Pacotes de vitrine — campanha `/promo/bussines-content`

Esta campanha tem a seção de preços ligada, mas **não possui nenhum `campaign_package` próprio** — exibe só os 3 produtos vinculados. O slug tem um typo (`bussines`, com dois "s") que está publicado assim.

---

## 4. 👨‍👩‍👧 Família

Sete produtos, todos abaixo de $700. Inclui a sub-linha de **recém-nascido em casa** (Nest e Bloom), que usa wraps, acessórios e props fornecidos.

| Produto | Preço | Cobertura | Entregável | Campanha |
|---|---:|---|---|---|
| **Colorful Birthday Party** | $650 | 3h | 50 fotos + highlight de 1min30 | ✅ `family-content` |
| **Nest** | $500 | 3h | 10 fotos editadas — sessão em casa, wraps e props inclusos | 🔒 campanha oculta |
| **Tiny Memories Highlight** | $380 | 1h | highlight de 45s–1min | ✅ `family-content` |
| **Mini Baby Photoshoot** | $350 | 1h | 25 fotos + vídeo social de 30s | ✅ `family-content`, `photo-services` |
| **Bloom** | $350 | 1h | 6 fotos editadas — sessão em casa, wraps e props inclusos | 🔒 campanha oculta |
| **Family Photshoot Session** | $350 | 1h30 | 60 fotos | ⚠️ nenhuma |
| **Birthday Photoshoot** | $250 | 2h | 50 fotos — "ideal para festas infantis de 2 a 6 anos" | ✅ `photo-services` |

- **Tiny Memories Highlight** — *"A short, cinematic keepsake of your baby's birthday — the smiles, the cake, and the family moments you'll want forever."*
- **Nest / Bloom** — *"In-home session. Choice of [10/6] edited photos. Use of premium wraps, accessories, and props; props and wraps provided."*

> ⚠️ **Sobreposição de oferta:** "Colorful Birthday Party" ($650, 3h, 50 fotos + vídeo), "Birthday Photoshoot" ($250, 2h, 50 fotos) e "Tiny Memories Highlight" ($380, 1h, só vídeo) cobrem o mesmo evento em recortes diferentes, sem nenhuma indicação no site de como escolher entre eles.

---

## 5. 🚁 Drone Light Shows

Categoria própria — não cabe em casamento, business nem família, e tem um modelo de preço **diferente de todo o resto do catálogo**: `price_unit` é **"Reservation"**, não "session".

| Produto | Preço | Cobertura | Entregável | Campanha |
|---|---:|---|---|---|
| **Holiday Drone Show** | $500 | 10–15min show customizado | 200–600 drones sincronizados ao vivo | ✅ `drone-light-shows` |
| **Drone Light Show Event** | $500 | 10–15min show personalizado | 150–600 drones sincronizados ao vivo | ✅ `drone-light-shows`, `premium-vendor-partnership`, 🔒 oculta |
| **Surprise Proposal Drone Experience** | $500 | 10–15min show de pedido | 200–600 drones sincronizados ao vivo | ✅ `drone-light-shows` — *também listado em [Casamento](#24-pedido-de-casamento)* |

- **Holiday Drone Show** — *"A custom holiday drone show designed to light up the sky with festive shapes, synchronized formations, and a memorable Christmas experience."*
- **Drone Light Show Event** — *"A fully personalized drone light show designed for events, brand activations, weddings and unforgettable moments."* Selo **"Build Your Drone Show"**, CTA **"Talk with Us"**.
- **Surprise Proposal Drone Experience** — *"A once-in-a-lifetime drone light show crafted exclusively for an unforgettable marriage proposal. We design, choreograph, and illuminate your 'Will You Marry Me?' moment in the sky — synchronized, cinematic, and completely personalized."*

> 🚨 **O ponto mais importante desta seção.** O campo `description` de **Drone Light Show Event** termina com a frase **"Starting at $9,580 · Book Availability"**, mas o campo `price` do produto é **$500**. Ou seja: o valor real do show é da ordem de **$9.580** e os $500 são uma **taxa de reserva** — porém o sistema de checkout **não sabe disso**. Se alguém reservar por esse produto, o Stripe cobra $500 e registra como pagamento integral (`payment_type: "full"`). O campo próprio para isso (`booking_reserve_enabled`) existe e está **desligado**. Os três produtos de drone estão nessa situação.

A campanha `/promo/drone-light-shows` é **pública**, tem 35 visualizações e a seção de preços **desligada** — exibe só os produtos.

---

## 6. Campanhas promocionais

Sete campanhas ativas em `/promo/<slug>`. `visibility_mode: unlisted` significa que a página funciona mas não é divulgada nem indexada.

| Slug | Título | Visibilidade | Views | Preços | Produtos | Vendors |
|---|---|---|---:|:-:|:-:|:-:|
| `wedding-packages` | Wedding Packages | pública | 106 | ✓ | ✓ | |
| `premium-vendor-partnership` | Premium Partners Program | unlisted | 101 | | ✓ | ✓ |
| `drone-light-shows` | Drone Light Shows | pública | 35 | | ✓ | |
| `bussines-content` | Bussines Content | unlisted | 25 | ✓ | ✓ | |
| `family-content` | Family Content | unlisted | 23 | ✓ | ✓ | |
| `photo-services` | Photo Services | pública | 16 | ✓ | ✓ | |
| `fashion-films` | Fashion Films | unlisted | 7 | ✓ | | |

**Headlines em uso:**
- `wedding-packages` — "Wedding Packages" / "Memories That Lasts"
- `premium-vendor-partnership` — "Partners Program" / "Vendors & Brands" — *"A strategic partnership for vendors and businesses who want to offer premium visual experiences while gaining exclusive bonuses, content credits, or preferred production rates."*
- `drone-light-shows` — "Drone Light Show" / "Create an Unforgettable" — *"High-impact drone light shows for brands, launches, large-scale festivals and events."*
- `bussines-content` — "Business" / "Content Creation"
- `family-content` — "Family" / "Memories That Lasts"
- `photo-services` — "Everafter" / "Memories That Lasts"
- `fashion-films` — "Boost Your Sales" / "With High Quality Contents"

### 6.1 Campanha `/promo/photo-services`

Mantém os três pacotes genéricos originais do template, nunca personalizados:

| Pacote | Preço exibido | Depósito | Descrição |
|---|---|---:|---|
| **Photography Session** | $250–$1200 | $150 | Fotos em alta resolução · edição profissional · galeria online · direitos de impressão |
| **Photo & Video Combo** ⭐ | Personalize | $150 | Foto + vídeo · cobertura de dia inteiro · highlights editados · álbum digital |
| **Videography Session** | $350–$1750 | $150 | Gravação 4K · imagens de drone disponíveis · áudio profissional · highlight reel |

### 6.2 Campanha `fashion-films`

Tem a seção de preços ligada mas **nenhum pacote e nenhum produto vinculado** — a página está no ar sem oferta nenhuma. É a de menor tráfego (7 views).

### 6.3 Campanhas inativas 🔒

Existem **duas campanhas desativadas** que ainda guardam 6 pacotes e 6 vínculos de produto. Elas não são legíveis pela chave pública (RLS bloqueia campanhas inativas), então só é possível ver o conteúdo pelos pacotes:

**Campanha de Valentine’s / pedidos de casamento** (`9bc1e380…`) — produtos vinculados: Couple Mini Session, Valentine’s Best Moments, Photo + Romantic Reel Session.

| Pacote | Preço exibido | Depósito |
|---|---|---:|
| **Signature Valentine Story** ⭐ | Personalize | $150 |
| **The Proposal Experience** | Starting at $500 | $150 |
| **Love Mini Experience** | Starting at $350 | $150 |

- **Love Mini Experience** — *"An intimate way to turn a moment into a memory."* Fotografia discreta para surpresas e momentos íntimos · perfeito para presentes de Valentine's · narrativa natural focada em emoção.
- **Signature Valentine Story** — *"A complete experience designed to capture emotion as it unfolds."* Cobertura foto e vídeo para pedidos e surpresas · visuais que preservam reações reais. *Ideal para: disponibilidade limitada no Valentine's, reserva antecipada recomendada.*
- **The Proposal Experience** — *"A romantic way to preserve a once-in-a-lifetime moment."* Vídeo cinematográfico para pedidos surpresa · presença discreta · foco em reações e conexão.

**Campanha genérica** (`11e30294…`) — produtos vinculados: Bloom, Nest, Drone Light Show Event. Os 3 pacotes são cópias exatas dos genéricos de `photo-services`.

> Estas campanhas são **material comercial pronto e pago que está desligado**. A linha de Valentine's em particular tem copy completa e três produtos associados — vale saber que existe antes de escrever qualquer coisa nova para essa data.

---

## 7. Como a cobrança realmente funciona

Fonte: `supabase/functions/create-booking-checkout/index.ts`. O preço **nunca** vem do cliente — é sempre buscado no banco no momento do checkout.

### Produto → cobra o valor cheio
Usa `products.price` e `products.currency`. Metadata do Stripe: `payment_type: "full"`. É o valor integral da tabela, de uma vez.

### Pacote de campanha → cobra só o depósito
Usa `campaign_packages.minimum_deposit_cents`, ou seja **$150** (exceto Brand Photography Content, $100). Metadata: `payment_type: "campaign_deposit"`. O `price_display` (`"Starting at $2200"`) é **puramente texto de marketing e nunca é cobrado**.

### Fatos operacionais a ter em conta

- **Não existe controle de saldo restante.** Depois do depósito de $150, o sistema não registra, não cobra e não acompanha os valores que faltam. Toda a cobrança do restante acontece fora da plataforma.
- **A reserva de horário é global.** Um horário reservado bloqueia **todos** os produtos e **todos** os pacotes naquele mesmo horário — não é por produto. Dois clientes não conseguem reservar o mesmo horário nem para serviços completamente diferentes.
- **Janela padrão:** slots de 60min, das 10:00 às 18:00, com reserva segurada por 15min durante o checkout. Nenhum produto tem regra própria configurada (a tabela `product_booking_rules` está **vazia**).
- **Preço promocional é ignorado no checkout.** Mesmo que `has_promotional_price` seja ligado, o Stripe cobra o `price` cheio.

---

## 8. Quiz de casamento — preços que não vendem

Em `/weddingquiz`. Seis pacotes **hardcoded** em `src/components/quiz/utils/packageCalculator.ts`. Exibidos com selo "Estimated Price", enviados por webhook como lead. **Não têm nenhuma ligação com o catálogo real e não passam pelo checkout.** Alterá-los exige deploy.

| Tipo | Pacote | Preço estimado |
|---|---|---:|
| Videography Package | The Highlight Reel | $2.500 |
| Videography Package | The Legacy Film | $3.500 |
| Videography Package | The Cinematic Love Story | $5.000 |
| Photo + Video Package | Essential Love | $2.999 |
| Photo + Video Package | Dream Wedding | $4.999 |
| Photo + Video Package | Luxury Experience | $8.999 |

> ⚠️ Não existe trilha "só foto" no quiz: quem responde que quer apenas fotografia é silenciosamente convertido para "foto + vídeo" e recebe uma cotação de pacote combinado. Além disso, os valores do quiz ($2.999–$8.999) estão bem acima do catálogo real de casamento ($750–$5.000) — o lead chega com expectativa de preço diferente da tabela.

---

## 9. Observações e inconsistências

Levantamento factual do estado atual. **Nenhuma correção foi aplicada** — o pedido foi documentar, não alterar.

### Dados
- **Typos publicados:** `price_unit: "Resevartion"` (Surprise Proposal Drone), slug `Dronw-show` (Drone Light Show Event), título `Family Photshoot Session`, slug de campanha `bussines-content`.
- **Slug enganoso:** o produto **Business & Product Shooting** tem o slug `family-photshoot-session-copy` — foi duplicado de um produto de família e nunca renomeado. Idem `mini-session-copy` para Couple Cinematic Session.
- **Descrição placeholder:** quatro produtos (Business & Product Shooting, Couple Cinematic Session, Couple Photoshoot Session, Family Photshoot Session) usam a tagline institucional *"California-based visual storytelling brand capturing life's most precious moments"* no lugar de uma descrição real.
- **Colorful Birthday Party** está sem descrição nenhuma (campo vazio).

### Configuração
- **`booking_reserve_enabled` está desligado nos 34 produtos.** O recurso de "pagar só uma reserva" existe no banco e na interface, mas não está em uso — o que torna os produtos de drone cobrados errado (ver [seção 5](#5--drone-light-shows)).
- **`has_promotional_price` está desligado nos 34 produtos.** Nenhum preço promocional ativo hoje.
- **18 dos 34 produtos têm `show_full_price = false`** — o preço não aparece na vitrine, o cliente só descobre no checkout.
- **Só 6 dos 34 produtos têm `show_in_our_products = true`** — os outros 28 não aparecem na seção "Our Products" e só são alcançáveis por link de campanha. Os visíveis são: Event Package Coverage, Signature Content Session, Colorful Birthday Party, Mini Baby Photoshoot, Tiny Memories Highlight, Corporate Headshots Session.
- **13 dos 34 produtos não estão ligados a campanha nenhuma** — incluindo quase toda a linha premium de casamento (The Heirloom $5.000, The Ceremony, The Vow, The Keepsake, The Prelude, The Story, The Memory, The Portrait). São produtos vendáveis sem nenhuma página que os apresente.

### Navegação
- O card **"Wedding Packages"** da home aponta para `/wedding-packages`, **rota que não existe** — cai no 404. A página real é `/promo/wedding-packages`.
- A seção "Our Services" da home está **escondida no mobile** (`hidden sm:block`).
- Existe um default herdado de template no código: `price_unit: "per night"` — resquício de um projeto de hospedagem, sem sentido para fotografia.

---

## Como atualizar este documento

Todos os dados vêm da API REST do Supabase, usando a chave anon pública de `src/integrations/supabase/client.ts` (somente leitura):

```sh
BASE="https://hmdnronxajctsrlgrhey.supabase.co/rest/v1"
KEY=$(grep -oE "eyJ[A-Za-z0-9_.-]{20,}" src/integrations/supabase/client.ts | head -1)

curl -s "$BASE/products?select=*&order=sort_order"        -H "apikey: $KEY" -H "Authorization: Bearer $KEY"
curl -s "$BASE/campaign_packages?select=*"                -H "apikey: $KEY" -H "Authorization: Bearer $KEY"
curl -s "$BASE/promotional_campaigns?select=*"            -H "apikey: $KEY" -H "Authorization: Bearer $KEY"
curl -s "$BASE/promotional_campaign_products?select=*"    -H "apikey: $KEY" -H "Authorization: Bearer $KEY"
```

A chave anon só enxerga `products` com `is_active = true` e campanhas com `is_active = true` — campanhas desativadas exigem a `service_role` key.

**Outras fontes:**

| O quê | Onde |
|---|---|
| Regras de cobrança | `supabase/functions/create-booking-checkout/index.ts` |
| Pacotes do quiz (hardcoded) | `src/components/quiz/utils/packageCalculator.ts` |
| Admin de produtos | `src/pages/ProductsAdmin.tsx`, `src/components/admin/ProductForm.tsx` |
| Admin de pacotes de campanha | `src/components/admin/CampaignPackagesTab.tsx` |
| Categorias do portfólio | `src/components/admin/GalleryCardForm.tsx` (Weddings / Business / Family) |
