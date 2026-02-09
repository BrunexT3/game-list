# Game List - Projeto

## Resumo
Site estatico com a colecao de jogos do Bruno, deployado no GitHub Pages.

- **Repo:** https://github.com/BrunexT3/game-list.git
- **Site:** https://brunext3.github.io/game-list/
- **Deploy:** Automatico via GitHub Actions (push na main)
- **Layout:** Tabela sortavel estilo Notion (tema claro padrao)
- **Ordenacao padrao:** Status (Concluido > Em andamento > Cancelado > Nao iniciada)

---

## Estrutura do Projeto

| Arquivo | Descricao |
|---------|-----------|
| `index.html` | App single-page (HTML+CSS+JS em um arquivo so) |
| `.github/workflows/deploy.yml` | Workflow de deploy para GitHub Pages |
| `Gaming List 16c2100a7c72806a8288eb679bfa441a_all.csv` | CSV original exportado do Notion |
| `CLAUDE.md` | Este arquivo - contexto do projeto |

---

## Funcionalidades do Site

- Tabela com colunas sortaveis (click no header)
- Filtros: plataforma (PS4/PS5/Switch 2), status, avaliacao
- Busca por nome/plataforma/dificuldade
- Barra de progresso da colecao no header
- Stats: total jogos, concluidos, backlog, plataformas
- Modal com detalhes ao clicar na linha
- Dark/light theme toggle
- Exportar CSV filtrado
- Favicon de game (controller emoji)

## Status disponiveis

| Status | Cor | Descricao |
|--------|-----|-----------|
| Concluido | Verde | Jogo finalizado |
| Em andamento | Azul | Jogando atualmente |
| Cancelado | Vermelho | Desistiu |
| Nao iniciada | Cinza | Backlog |

## Regra: dificuldade
- Jogos concluidos sem dificuldade especificada = **Normal**
- Excecao: Spider-Man Remastered = **Very Hard**

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
| `list_games` | Lista jogos com progresso de trofeus | Paginado (limit, offset) |
| `search_game` | Busca jogo por nome na biblioteca PSN | Parametro: query |
| `get_game_trophies` | Trofeus detalhados de um jogo | Parametro: npCommunicationId |
| `trophy_summary` | Estatisticas gerais da conta PSN | Sem parametros |

### Como usar os tools

1. **Buscar trofeus de um jogo:**
   - Primeiro: `search_game` com o nome do jogo
   - Pegar o `npCommunicationId` do resultado
   - Depois: `get_game_trophies` com esse ID

2. **Atualizar trofeus na game-list:**
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

## Dados atuais dos trofeus (via PSN API - 09/02/2026)

| Jogo | Progresso | Earned |
|------|-----------|--------|
| Resident Evil 2 Remake | 20% | 15B |
| Days Gone Remastered | 18% | 17B 2S |
| Assassin's Creed Shadows | 11% | 8B 1S |

---

## Plataformas

| Plataforma | Cor no site |
|------------|-------------|
| PS4 | `#003087` (azul escuro) |
| PS5 | `#00439C` (azul) |
| Switch 2 | `#E60012` (vermelho) |

---

## Fluxo de trabalho

### Adicionar jogo novo
1. Adicionar objeto no array `games` no `index.html`
2. Campos: nome, adquirido, avaliacao, dataFinal, dataInicio, dificuldade, plataforma, release, status, trofeus
3. Commit e push (deploy automatico)

### Atualizar status de jogo
1. Editar o objeto do jogo no array `games`
2. Mudar status, datas, avaliacao, dificuldade conforme necessario
3. Se concluido sem dificuldade especifica → "Normal"

### Sincronizar trofeus com PSN
1. Usar tool `search_game` para buscar o jogo
2. Usar tool `get_game_trophies` com o npCommunicationId
3. Calcular earned/total e porcentagem
4. Atualizar campo `trofeus` no `index.html`
5. Commit e push

---

## Proximos passos possiveis

- [ ] Sincronizar trofeus de todos os jogos concluidos via MCP PSN
- [ ] Adicionar jogos que estao na PSN (282) mas nao estao na lista
- [ ] Automatizar atualizacao de trofeus (script ou rotina)
- [ ] Adicionar coluna de platina (sim/nao) na tabela
