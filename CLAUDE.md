# Game List - Projeto

## Resumo
Site estatico com a colecao de jogos do Bruno, deployado no GitHub Pages.
Single-page app (HTML+CSS+JS em um arquivo so) com ~273 jogos de PS3, PS4, PS5 e Switch 2.

- **Repo:** https://github.com/BrunexT3/game-list.git
- **Site:** https://brunext3.github.io/game-list/
- **Deploy:** Automatico via GitHub Actions (push na main)
- **Layout:** Tabela sortavel estilo Notion (tema claro padrao)
- **Ordenacao padrao:** Status (Concluido > Em andamento > Cancelado > Nao iniciada)

---

## Estrutura do Projeto

| Arquivo | Descricao |
|---------|-----------|
| `index.html` | App single-page (HTML+CSS+JS em um arquivo so, ~1100 linhas) |
| `.github/workflows/deploy.yml` | Workflow de deploy para GitHub Pages |
| `Gaming List 16c2100a7c72806a8288eb679bfa441a_all.csv` | CSV original exportado do Notion |
| `CLAUDE.md` | Este arquivo - contexto do projeto |

---

## Estrutura do index.html

| Secao | Linhas aprox. | Descricao |
|-------|---------------|-----------|
| CSS (variaveis, estilos) | 1-460 | Temas light/dark, tabela, filtros, modal |
| HTML (header, filtros, tabela, modal) | 460-540 | Estrutura da pagina |
| Array `games` | 543-816 | Dados de todos os jogos |
| `platformColors` | ~838 | Cores das plataformas |
| JavaScript (logica) | 838-1100 | Filtros, sort, render, modal, export |

### Objeto de jogo (formato)
```javascript
{ nome: "Nome do Jogo", adquirido: true/false, avaliacao: 0-5, dataFinal: "DD/MM/YYYY", dataInicio: "DD/MM/YYYY", dificuldade: "Normal", plataforma: "PS5", release: 2024, status: "Concluido", trofeus: "19/67 - 28%" }
```

### Jogos originais vs novos
- **Linhas 543-580:** 39 jogos originais (importados do Notion CSV)
- **Linhas 581-815:** 234 jogos adicionados via PSN API (sessao 09/02/2026)

---

## Funcionalidades do Site

- Tabela com colunas sortaveis (click no header)
- **Dropdown "Ordenar por"** com 5 opcoes: Nome, Trofeus (%), Avaliacao, Status, Plataforma
- Botao asc/desc ao lado do dropdown (sincronizado com clicks nos headers)
- Filtros: plataforma (PS3/PS4/PS5/Switch 2), status, avaliacao
- Busca por nome/plataforma/dificuldade
- Barra de progresso da colecao no header
- Stats: total jogos, concluidos, backlog, plataformas
- Modal com detalhes ao clicar na linha
- **Icone de platina** para jogos com 100% de trofeus (na tabela e no modal)
- Dark/light theme toggle
- Exportar CSV filtrado
- Copiar link
- Favicon de game (controller emoji)

## Icone de Platina
- URL: `https://i.redd.it/ys23cmt5l0u61.png`
- Aparece na tabela (18x18px) e no modal (28x28px) quando trofeus = 100%
- 7 jogos com platina: Farming Simulator 19, Os Cavaleiros do Zodiaco, God of War Ascension, Far Cry 3, PES 2013, CS:GO, God of War III

## Status disponiveis

| Status | Cor | Descricao |
|--------|-----|-----------|
| Concluido | Verde | Jogo finalizado |
| Em andamento | Azul | Jogando atualmente |
| Cancelado | Vermelho | Desistiu |
| Nao iniciada | Cinza | Backlog |

## Plataformas

| Plataforma | Cor do circulo | Hex |
|------------|----------------|-----|
| PS3 | Dourado/laranja | `#f5a623` |
| PS4 | Azul escuro | `#003087` |
| PS5 | Azul medio | `#00439C` |
| Switch 2 | Vermelho | `#E60012` |

## Regras de dados
- Jogos concluidos sem dificuldade especificada = **Normal**
- Excecao: Spider-Man Remastered = **Very Hard**
- 234 jogos novos (da PSN) entraram com: status "Concluido", avaliacao 3, dificuldade "Normal"
- Datas dos 234 jogos novos: preenchidas com `lastUpdated` da PSN API
- 7 jogos platinados: datas corrigidas com primeiro/ultimo trofeu real via `get_game_trophies`

---

## PSN Integration (MCP Server)

### Conta PSN
- **Online ID:** BrunexT3
- **Email:** bruno.dm28@gmail.com
- **Total de jogos na PSN:** 282

### MCP Server `psn`
- **Pasta:** `E:\Claude-Projetos\psn-mcp\`
- **Config:** Registrado em `C:\Users\bruno\.claude.json` (projeto `C:/Users/bruno/.local/bin`)
- **Comando:** `node E:/Claude-Projetos/psn-mcp/server.mjs`
- **Env:** `PSN_NPSSO` (token de autenticacao)

### Tools disponiveis

| Tool | Descricao | Uso |
|------|-----------|-----|
| `list_games` | Lista jogos com progresso de trofeus | Paginado (limit, offset). Max 282 jogos |
| `search_game` | Busca jogo por nome na biblioteca PSN | Parametro: query |
| `get_game_trophies` | Trofeus detalhados de um jogo | Parametro: npCommunicationId |
| `trophy_summary` | Estatisticas gerais da conta PSN | Sem parametros |

### Como usar os tools

1. **Listar todos os jogos (paginado):**
   - `list_games` com limit=100, offset=0 (3 chamadas para cobrir 282 jogos)

2. **Buscar trofeus detalhados de um jogo:**
   - Primeiro: `search_game` com o nome do jogo
   - Pegar o `npCommunicationId` do resultado
   - Depois: `get_game_trophies` com esse ID
   - Retorna cada trofeu individual com `earnedDateTime`

3. **Atualizar trofeus na game-list:**
   - Buscar dados via MCP PSN
   - Atualizar o campo `trofeus` no array `games` do `index.html`
   - Formato: `"earned/total - XX%"` (ex: `"19/67 - 28%"`)

### Autenticacao PSN (NPSSO Token)

**O token NPSSO expira em ~60 dias.**

Quando expirar, o Bruno precisa:
1. Acessar https://store.playstation.com e logar
2. Visitar https://ca.account.sony.com/api/v1/ssocookie
3. Copiar o valor `npsso` do JSON retornado
4. Atualizar em `C:\Users\bruno\.claude.json` > projects > psn > env > PSN_NPSSO

**Token atual obtido em:** 09/02/2026

---

## Historico de sessoes

### Sessao 1 - 09/02/2026 (Parte 1)
- Testou os 4 tools do MCP PSN (todos funcionando)
- Atualizou trofeus dos jogos existentes no HTML via PSN API
- Corrigiu datas de inicio/fim baseado nos trofeus

### Sessao 2 - 09/02/2026 (Parte 2)
- Adicionou 234 jogos novos da PSN ao HTML (77 PS5, 99 PS4, 58 PS3)
- Adicionou cor para plataforma PS3
- Deduplicou jogos cross-platform (manteve versao mais nova)
- Commit, push e deploy

### Sessao 3 - 09/02/2026 (Parte 3)
- Adicionou icone de platina para jogos com 100% trofeus (tabela + modal)
- Atualizou 234 jogos novos: status "Concluido", avaliacao 3
- Preencheu dificuldade "Normal" em todos jogos sem dificuldade
- Preencheu datas de 188 jogos com `lastUpdated` da PSN
- Buscou datas precisas para 7 jogos platinados via `get_game_trophies`
- Commit `c4c38c5`, push, deploy (run #10 success)

### Sessao 4 - 09/02/2026 (Parte 4)
- Adicionou dropdown "Ordenar por" com 5 opcoes (Nome, Trofeus %, Avaliacao, Status, Plataforma)
- Botao asc/desc ao lado do dropdown
- Coluna Trofeus agora tambem e sortavel pelo header
- Sincronizacao bidirecional entre dropdown e headers da tabela
- Diferenciou cores dos circulos: PS3 dourado, PS4 azul escuro, PS5 azul medio
- Commits `9668955` e `ec3e16e`, push, deploy (runs #11 e #12 success)

---

## Fluxo de trabalho

### Adicionar jogo novo manualmente
1. Adicionar objeto no array `games` no `index.html`
2. Campos: nome, adquirido, avaliacao, dataFinal, dataInicio, dificuldade, plataforma, release, status, trofeus
3. Commit e push (deploy automatico)

### Atualizar status de jogo
1. Editar o objeto do jogo no array `games`
2. Mudar status, datas, avaliacao, dificuldade conforme necessario
3. Se concluido sem dificuldade especifica -> "Normal"

### Sincronizar trofeus com PSN
1. Usar tool `list_games` para pegar progresso geral (paginado, 100 por vez)
2. Para detalhes: `search_game` + `get_game_trophies` com npCommunicationId
3. Calcular earned/total e porcentagem
4. Atualizar campo `trofeus` no `index.html`
5. Commit e push

### Bulk updates (PowerShell)
- Para alteracoes em massa, criar script `.ps1` temporario e executar com `powershell -ExecutionPolicy Bypass -File`
- Nao usar PowerShell inline no bash (problemas com escape de `$variaveis`)
- Deletar script temporario apos uso

---

## Proximos passos possiveis

- [ ] Automatizar atualizacao de trofeus (script ou rotina periodica)
- [ ] Revisar avaliacao/dificuldade dos 234 jogos novos (todos entraram com nota 3 e Normal)
- [ ] Adicionar mais opcoes de avaliacao no filtro (1 e 2 estrelas)
- [ ] Adicionar filtro por plataforma no dropdown de ordenacao
- [ ] Melhorar responsividade mobile
- [ ] Adicionar contagem de platinas no header stats
