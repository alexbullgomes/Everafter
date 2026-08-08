# Everafter

Site da Everafter California — portfólio de fotografia e videografia, landing pages de campanhas promocionais, blog, chat de visitantes, agendamento e pagamentos, com dashboards de admin e de usuário.

- **Produção**: https://www.everafterca.com
- **Hospedagem**: Vercel (deploy automático a partir da branch `main`)
- **Backend**: Supabase (Postgres, Auth, Storage, Realtime, Edge Functions) — projeto `hmdnronxajctsrlgrhey`

## Stack

Vite · React 18 · TypeScript · Tailwind CSS · shadcn/ui · React Router v6 · TanStack Query · Supabase · Stripe

## Desenvolvimento local

Requer Node 22.x (ver `engines` no `package.json`).

```sh
npm ci          # respeita o .npmrc (legacy-peer-deps)
npm run dev     # http://localhost:8080
```

Outros comandos:

```sh
npm run build                            # build de produção -> dist/
npm run lint                             # ESLint
npx tsc -p tsconfig.app.json --noEmit    # typecheck (NÃO faz parte do build)
npm run preview                          # serve o build local
```

Não há framework de testes configurado neste projeto.

### Variáveis de ambiente

Nenhuma é obrigatória. As credenciais do Supabase (URL + anon key, públicas por design e protegidas por RLS) estão em `src/integrations/supabase/client.ts`. Veja `.env.example` para as opcionais.

Segredos de servidor (Stripe, webhooks n8n, service role) vivem nos **secrets do Supabase**, não na Vercel — as edge functions os leem via `Deno.env.get()`.

### Sobre o `.npmrc`

`react-day-picker@8` declara peer dependency `date-fns ^2 || ^3`, mas o projeto usa `date-fns@4`. Sem `legacy-peer-deps=true` o `npm ci` falha com ERESOLVE, local e na Vercel. Remover quando o `react-day-picker` for atualizado para a v9.

## Deploy

### Frontend (Vercel)

Push na `main` dispara o deploy de produção. PRs geram preview deployments em `*.vercel.app`.

A configuração está em `vercel.json`. O ponto crítico é o rewrite `/(.*) → /index.html`: a app é uma SPA com `BrowserRouter`, então sem ele qualquer deep link (`/blog/<slug>`, `/promo/<slug>`, `/dashboard/*`) retorna 404 quando acessado diretamente.

### Edge Functions (Supabase CLI)

**Não são deployadas pela Vercel.** Após alterar qualquer coisa em `supabase/functions/`:

```sh
supabase link --project-ref hmdnronxajctsrlgrhey
supabase functions deploy <nome-da-funcao>
```

As funções `webhook-proxy` e `consultation-webhook-proxy` têm uma allowlist de CORS — qualquer origem nova (um domínio novo, por exemplo) precisa ser adicionada lá, senão os formulários falham silenciosamente no browser.

### Migrations

Alterações de schema entram como novos arquivos SQL timestamped em `supabase/migrations/`.

## DNS

O domínio `everafterca.com` é gerenciado na **Hostinger**, com nameservers `ns1/ns2.dns-parking.com`. Apenas os registros `A` (apex) e `www` apontam para a Vercel.

⚠️ **Nunca trocar os nameservers para a Vercel.** Os registros `MX` apontam para o Google Workspace — trocar os NS derrubaria o e-mail da empresa.

## Convenções

Ver `CLAUDE.md` para as convenções de arquitetura (padrão de hooks de acesso a dados, theming dinâmico via CSS variables, sistema de chat). `KNOWLEDGE_BASE.md` tem contexto de schema, mas é parcialmente desatualizado — o código é a fonte de verdade.
