# Gigi's Shop

App de gestão para brechó/revenda, otimizado para uso no iPhone. Funciona como PWA — pode ser salvo na tela inicial e abre em tela cheia, sem barra do Safari.

## Funcionalidades

### Dashboard
- Resumo financeiro: total investido, arrecadado, lucro líquido e margem média
- Estimativa de receita por plataforma (sem duplicar SKUs listados em múltiplos lugares)
- Top 5 itens mais lucrativos
- Relatório semanal e mensal de vendas com drill-down por semana

### Inventário
- Cadastro de itens com SKU automático (`GP-XXXX`), categoria, estado, local de compra, localização física e sexo (para roupas)
- Filtro por status (Em Estoque / Listado / Vendido), categoria e sexo
- Ordenação por data, nome ou valor
- Flag de **item retornável** — alerta no dashboard quando passa de 20 dias sem vender

### Listagem
- Registra anúncios em eBay, Marketplace, Depop, Vinted (configurável)
- Controla valor listado, valor alterado e valor final de venda
- Rastreio de envio com detecção automática de transportadora (USPS, UPS, FedEx, DHL)
- Duplicar anúncio para outra plataforma com um toque

### Gastos
- **Custos fixos**: compras únicas de equipamento (etiquetadora, estantes etc.)
- **Insumos**: embalagens e materiais consumíveis com controle de estoque, estoque mínimo e média ponderada de custo unitário

### Relatórios
- **Performance**: mais lucrativos, melhor margem, mais rápidos e mais lentos para vender
- **Padrões**: análise por categoria, sexo, plataforma, local de compra e estado da peça — com drill-down
- **Tempo**: sazonalidade histórica, últimos 12 meses, velocidade de giro, itens encalhados
- **Returns**: monitora itens retornáveis em estoque, destaca os com prazo vencendo

## Stack

- **Frontend**: HTML + CSS + JavaScript vanilla (single-file, sem build)
- **Backend**: [Supabase](https://supabase.com) — PostgreSQL via PostgREST
- **Offline**: dados em cache no `localStorage`
- **PWA**: suporte a apple-touch-icon, abre em tela cheia no iOS

## Como usar

### 1. Criar projeto no Supabase

1. Acesse [supabase.com](https://supabase.com) e crie uma conta gratuita
2. Crie um novo projeto
3. Vá em **Project Settings → API** e copie:
   - **Project URL** (ex: `https://xxxxxxxxxxx.supabase.co`)
   - **Anon public key**

### 2. Criar tabelas

No **SQL Editor** do Supabase, execute:

```sql
create table inventario (
  sku text primary key,
  nome text,
  categoria text,
  dataCompra text,
  valorPago numeric,
  localCompra text,
  estadoCompra text,
  statusItem text,
  sexo text,
  localizacao text,
  retornavel boolean default false,
  created_at timestamptz default now()
);

create table listagem (
  id text primary key,
  sku text,
  plataforma text,
  dataListagem text,
  valorListado numeric,
  valorAlterado numeric,
  valorVendido numeric,
  dataVenda text,
  formaPgto text,
  status text,
  embalagens jsonb,
  custoEmbalagem numeric,
  trackingCode text,
  embalagemId text,
  created_at timestamptz default now()
);

create table gastos (
  id text primary key,
  item text,
  valor numeric,
  data text,
  categoria text,
  insumoId text,
  quantidade numeric,
  created_at timestamptz default now()
);

create table insumos (
  id text primary key,
  nome text,
  qtdEstoque numeric,
  qtdMinima numeric,
  valorUnitario numeric,
  valorPago numeric,
  dataCompra text,
  created_at timestamptz default now()
);

create table config (
  chave text primary key,
  valores text
);
```

### 3. Abrir o app

Abra `index.html` no browser (ou hospede em qualquer servidor estático — GitHub Pages, Netlify, Vercel etc.).

Na tela inicial, cole a **Project URL** e a **Anon Key** do Supabase e clique em **Salvar e conectar**.

### 4. Instalar no iPhone (opcional)

1. Abra o app no Safari
2. Toque em **Compartilhar** → **Adicionar à Tela de Início**
3. O app abrirá sem barra do Safari, em tela cheia

## Estrutura de arquivos

```
GigisShop/
├── index.html        ← aplicação completa
├── icons/            ← ícones PWA
│   ├── icon.svg
│   ├── icon-120.png
│   ├── icon-152.png
│   ├── icon-167.png
│   ├── icon-180.png
│   ├── icon-192.png
│   ├── icon-512.png
│   └── icon-1024.png
├── CLAUDE.md         ← contexto para AI
└── README.md         ← este arquivo
```

## Hospedagem

Por ser um único arquivo estático, pode ser hospedado em qualquer CDN/servidor. Recomendado: **GitHub Pages** (gratuito, entrega direto do repositório).

> **Segurança**: a `anon key` do Supabase é pública por design (acesso controlado por Row Level Security). Se quiser adicionar autenticação, configure RLS nas tabelas do Supabase.
