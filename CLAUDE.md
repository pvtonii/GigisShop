# Gigi's Shop — Contexto para Claude

## O que é este projeto

PWA (Progressive Web App) single-file para gestão de um brechó/revenda. Toda a lógica, estilos e markup vivem em `index.html`. Não há bundler, npm, servidor local nem processo de build.

## Arquitetura

```
index.html        ← aplicação inteira (HTML + CSS inline + JS inline)
icons/            ← ícones PWA (apple-touch-icon, favicon)
CLAUDE.md         ← este arquivo
README.md         ← documentação pública
```

## Stack

- **Frontend**: HTML/CSS/JS vanilla, sem frameworks
- **Backend**: [Supabase](https://supabase.com) (PostgreSQL + PostgREST)
  - Credenciais salvas em `localStorage` (`gigi_sb_url`, `gigi_sb_key`)
  - Sync bidirecional manual — usuário puxa com botão de refresh
- **Persistência offline**: `localStorage` com chave `gigi_shop_v3`
- **PWA**: `apple-mobile-web-app-capable`, touch icons — sem service worker, sem manifest.json

## Estado global

```js
let S = {
  inventario: [],   // itens comprados (chave: sku)
  listagem:   [],   // anúncios em plataformas (chave: id)
  gastos:     [],   // custos fixos e compras de insumos (chave: id)
  insumos:    [],   // embalagens/materiais consumíveis (chave: id)
  config: { categorias, locaisCompra, estadosCompra, statusVenda, plataformas, formasPgto, statusItem }
}
```

Estado lido do localStorage na inicialização, sobrescrito pelo Supabase após sync.

## Tabelas Supabase

| Tabela | Chave | Notas |
|---|---|---|
| `inventario` | `sku` (GP-XXXX) | Itens comprados |
| `listagem` | `id` (L-timestamp) | Anúncios por plataforma |
| `gastos` | `id` (G-timestamp) | Custos fixos e insumos |
| `insumos` | `id` (I-timestamp) | Materiais/embalagens consumíveis |
| `config` | `chave` | Pares chave-valor JSON (listas configuráveis) |

## Funcionalidades principais

- **Dashboard**: métricas financeiras, estimativa por plataforma, top 5 lucrativos, relatório semanal/mensal
- **Inventário**: CRUD de itens comprados, filtros por status/categoria/sexo, ordenação, badge de retornável
- **Listagem**: anúncios em eBay/Marketplace/Depop/Vinted, rastreio de envio (USPS/UPS/FedEx/DHL)
- **Gastos**: custos fixos únicos + insumos com controle de estoque e média ponderada de custo
- **Relatórios**: performance, padrões por categoria/sexo/plataforma/local, sazonalidade, retornáveis
- **Config**: listas configuráveis sincronizadas com Supabase

## Padrões de código

- Funções de render retornam HTML string e são atribuídas a `innerHTML`
- IDs de elementos do DOM são criados dinamicamente — não existem no HTML base
- `cm()` fecha qualquer modal limpando `innerHTML` do `#mc`
- `render()` redesenha a aba ativa completa
- Funções `open*Modal()` recebem objeto ou nada (novo item)
- Sync Supabase: `sbGet/sbUpsert/sbDelete` → chamadas PostgREST diretas

## Convenções de ID

- SKU inventário: `GP-0001`, `GP-0002`...
- ID listagem: `L-<timestamp>`
- ID gasto: `G-<timestamp>`
- ID insumo: `I-<timestamp>`

## Como testar

Abrir `index.html` direto no browser. Configurar Supabase URL + anon key na tela de setup.
Sem build, sem `npm install`, sem servidor necessário.

## Versão atual

`APP_VERSION = '08'` (definido no JS, exibido em Configurações)
