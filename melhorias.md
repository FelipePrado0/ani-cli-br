# Brainstorm ani-cli — QoL, performance, features

Baseado no código lido (`ani-cli`, `hacking.md`). Puro brainstorm, nada implementado.

## UX / CLI

- **Preview no fzf** — `--preview` mostrando sinopse/poster ASCII (via `chafa`/`viu`) na busca de anime. fzf já suporta `--preview`; hoje `menu_extra_flags` existe mas ninguém usa preview built-in.
- **Progresso salvo por timestamp**, não só episódio — hoje história é `ep_no\tanime_id\ttitle`, perde posição dentro do episódio. mpv tem `--save-position-on-quit` + watch-later; integrar leitura desse arquivo pra resumir "de onde parou" de verdade.
- **Cores/emoji no status "Ongoing/Finished"** já existe em `time_until_next_ep` — estender pro menu principal (marcar na lista de busca quais séries estão em exibição vs completas).
- **Confirmação antes de re-assistir** episódio já visto no histórico (hoje `-c` só pula pro último ep salvo, sem avisar "você já viu até aqui").
- **Fuzzy typo-tolerance na busca** — hoje search é literal contra site; um "did you mean" local via `agrep`/levenshtein simples quando `anime_list` vem vazio, antes de `die "No results found!"`.

## Performance / Robustez

- **Cache de busca/episódios local** (TTL curto, tipo `~/.cache/ani-cli`) — hoje toda busca e toda lista de episódio bate a rede sempre, mesmo repetindo a mesma série na mesma sessão. `select` no submenu já re-chama `anidb_episodes` do zero.
- **Paralelizar pré-busca em range** — quando `-S`/range seleciona múltiplos episódios, pré-buscar o próximo link enquanto o atual ainda toca.
- **Retry com backoff no curl** — hoje falha seca (`die`) em qualquer erro de rede transitório. `--retry 2 --retry-delay 1` no `anidb_curl` é 1 linha, resolve boa parte dos "Connection error" falso-positivo.
- **Timeout configurável** — `--max-time 10` hardcoded; em conexão lenta mata busca que ia funcionar. `ANI_CLI_TIMEOUT` env var, 1 linha.
- **Checksum/verificação do `update_script`** antes de `patch` — hoje confia 100% no conteúdo puxado do raw GitHub sem validar assinatura/hash. Baixo risco prático (só troca próprio script), mas é superfície de supply-chain se alguém comprometer o repo/CDN.

## Features novas

- **Discord Rich Presence** — outros forks (`jerry`, `Curd`, `GoAnime`) já têm; pra ani-cli seria hook opcional chamando presence via named pipe, atrás de flag pra não virar dependência obrigatória.
- **Notificação de novo episódio** — já existe scraping de countdown (`time_until_next_ep`), falta só um modo "watch" que roda em cron/systemd timer e dispara notify-send/toast quando contagem zera pra série da história.
- **Fila (playlist) multi-anime** — hoje `-S` multi-select só serve seleção de episódio dentro da MESMA série. Um modo "watch next 3 shows from history in sequence" tipo maratona.
- **Export/import de histórico** — hoje preso em `~/.local/state/ani-cli/ani-hsts`, formato TSV simples já é portável, só falta comando `ani-cli --export`/`--import` de/pra outra máquina.
- **Skip automático de recap/filler**, não só intro — `ani-skip` já cobre OP/ED; filler/recap precisaria de banda de dados externa (tipo AniSkip API já usada, ou MAL/AniList tags) — mais caro, avaliar se vale.

## Deixei de fora (YAGNI)

- GUI/TUI completo — muda natureza do projeto, `hacking.md` deixa claro que maintainers evitam escopo maior.
- Multi-source scraping — explicitamente rejeitado pelos maintainers originais (hacking.md linha 2). *(Nota: virou objetivo explícito do fork depois desta lista — ver discussão de arquitetura multi-provider separada.)*
- Legendas separadas — impossível, vídeo vem com legenda queimada do source; não é algo que ani-cli controla.
