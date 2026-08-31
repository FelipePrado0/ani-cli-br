<p align=center>
<br>
<a href="https://github.com/pystardust/ani-cli"><img src="https://img.shields.io/badge/fork%20de-pystardust%2Fani--cli-blue"></a>
<a href="LICENSE"><img src="https://img.shields.io/badge/license-GPL--3.0-brightgreen"></a>
<a href="#Linux"><img src="https://img.shields.io/badge/os-linux-brightgreen">
<a href="#MacOS"><img src="https://img.shields.io/badge/os-mac-brightgreen">
<a href="#Android"><img src="https://img.shields.io/badge/os-android-brightgreen">
<a href="#Windows"><img src="https://img.shields.io/badge/os-windows-yellowgreen">
</p>

<h3 align="center">
ani-cli-br: fork de <a href="https://github.com/pystardust/ani-cli">ani-cli</a> com suporte a múltiplas fontes, incluindo sites BR.
</h3>

<p align="center"><a href="README.md">Read in English</a></p>

## Sobre este fork

Este repositório é um **fork** do [ani-cli](https://github.com/pystardust/ani-cli) original, criado e mantido por [pystardust](https://github.com/pystardust) e colaboradores, licenciado sob **GPL-3.0**. Todo crédito pela base do projeto (arquitetura de scraping, sistema de histórico, integração com players) é dos autores originais. Veja [LICENSE](LICENSE) pro texto completo da licença.

O projeto original **não aceita** suporte a múltiplos sites por decisão consciente dos mantenedores (ver [hacking.md](hacking.md)). Este fork existe justamente pra isso: expandir ani-cli com fontes adicionais e funcionalidades de qualidade de vida, mantendo compatibilidade com o fluxo original sempre que possível.

Se você só quer o ani-cli original (site anidb.app, mantido ativamente pelos autores), use o [repositório oficial](https://github.com/pystardust/ani-cli).

## O que mudou neste fork

- **Múltiplas fontes de conteúdo** (`-p`/`--provider`): `anidb` (original), `animefire`, `animesdigital`. Menu de seleção interativo, ou pule direto com a flag
- **Maratona de histórico** (`--marathon`): reproduz o próximo episódio de cada série não assistida, em sequência
- **Notificação de novo episódio** (`--watch`): verificação única pra rodar via cron/systemd timer
- **Estatísticas locais** (`--stats`): total de séries/episódios assistidos, por provider
- **Diagnóstico de fontes** (`--check-providers`): testa se cada site está no ar antes de buscar
- **Download em lote** (`--all` + `-d`): baixa a série inteira numa passada
- **Export/import de histórico** (`--export`/`--import`)
- **Arquivo de configuração opcional** (`~/.config/ani-cli/config`), além das env vars
- **Cache local** de busca/episódios (TTL configurável). Não cacheia links de vídeo, que são assinados/expiram
- **Ficha do anime**: sinopse, gêneros e status (em exibição/finalizado) antes de reproduzir
- **Busca aproximada**: sem match exato, tenta de novo com a query mais curta
- **Prévia de sinopse no fzf** ao selecionar anime (provider anidb)
- **Confirmação de reassistir** episódio já visto
- Novas tentativas e tempo limite configuráveis, guarda de sanidade na autoatualização

Ver o histórico de commits pra detalhes de cada mudança.

### Fontes suportadas e limitações conhecidas

| Provider | Sub | Dub | Observação |
|---|---|---|---|
| `anidb` (padrão) | ✅ | ✅ | Site original, toggle sub/dub por episódio |
| `animefire` | ✅ | ✅ | Dublado é anime separado na busca (filtrado automaticamente com `--dub`) |
| `animesdigital` | ✅ | ✅ | Mesma limitação de dublado do animefire |

Sites BR investigados e **não suportados** por bloqueio real de terceiro (documentado no histórico de commits): Anroll e Goyabu (vídeo atrás do player do Google Blogger, que passou a rodar só no navegador via JavaScript, sem JS real não dá pra resolver), BetterAnime (mesma causa), Animes Vision (servidor de vídeo do player saiu do ar), AnimesOrion (site fora do ar). Isso pode mudar se os sites atualizarem a infra, não é recusa por preguiça, é bloqueio técnico verificado.

Troca de temporada (`change_season`) só funciona no provider `anidb`, os demais não expõem essa relação de forma confiável.

Histórico, `-c`, `--marathon` e `--watch` já sabem qual provider cada série usa (salvo automaticamente).

## Instalação

Este fork não está publicado em gerenciador de pacote nenhum (scoop/brew/AUR/apt são do projeto original). Instale a partir do código-fonte:

```sh
git clone https://github.com/FelipePrado0/ani-cli-br.git
cd ani-cli-br
sudo cp ani-cli /usr/local/bin   # linux/mac
# ou copie ani-cli pra algum diretório no seu PATH
```

No Windows, use Git Bash + [scoop](https://scoop.sh/) só pras dependências (`fzf`, `mpv`), não pro ani-cli em si. Veja a seção [Windows](#windows) abaixo.

### Windows

1. Instale [scoop](https://scoop.sh/) e Git for Windows
2. `scoop bucket add extras && scoop install fzf mpv`
3. Clone este repositório e rode `ani-cli` via Git Bash (`sh ./ani-cli` ou adicione ao PATH)

Problemas de fzf travando em `Search anime:` são do terminal (mintty), não do fork. Use Windows Terminal com o perfil Git Bash. Veja mais detalhes na seção "Known Problems" do [README original](https://github.com/pystardust/ani-cli#windows-known-problems-and-solutions).

## Dependências

- `grep`, `sed`, `curl`
- `mpv`: player de vídeo (ou `iina` no Mac, `vlc` como alternativa)
- `fzf`: menu interativo (ou `rofi`/`dmenu`)
- `yt-dlp` ou `ffmpeg`: necessário só pra `-d`/download
- `patch`: necessário só pra `-U`/autoatualização
- `ani-skip`: opcional, pra pular abertura (`--skip`)

Se aparecer `Blocked by cloudflare`, instale [curl-impersonate](https://github.com/lwthiker/curl-impersonate).

## Uso

```sh
ani-cli [opções] [busca]
```

### Opções principais

```
  -p, --provider          Fonte de conteúdo (anidb, animefire, animesdigital)
  -q, --quality           Qualidade do vídeo
  -e, --episode, -r       Episódio ou intervalo (ex: -e 5-8)
  --all                   Todos os episódios (usar com -d pra baixar a série inteira)
  --dub                   Versão dublada
  -d, --download          Baixa em vez de reproduzir
  -c, --continue          Continua do histórico
  --marathon              Reproduz o próximo episódio de cada série do histórico, em sequência
  -S, --select-nth        Seleciona o N-ésimo resultado sem menu interativo
  -v, --vlc               Usa VLC
  -s, --syncplay          Assiste em grupo via Syncplay
  --skip                  Pula abertura (ani-skip, mpv)
```

### Comandos utilitários

```
  --stats                 Estatísticas locais de histórico
  --check-providers       Testa disponibilidade de cada fonte
  --export <arquivo>      Exporta histórico
  --import <arquivo>      Importa/mescla histórico
  -D, --delete            Apaga histórico
  --watch                 Verificação única de novos episódios (pra cron/systemd timer)
  -N, --nextep-countdown  Contagem regressiva pro próximo episódio
  -U, --update            Atualiza o script (puxa do branch configurado)
  -V, --version           Versão
  -h, --help              Ajuda completa
```

### Exemplos

```sh
ani-cli -p animefire --dub one piece
ani-cli -p animesdigital -q 720p banana fish
ani-cli --marathon
ani-cli -d --all mushoku tensei III
ani-cli --check-providers
ani-cli --stats
```

## Configuração

Além das variáveis de ambiente `ANI_CLI_*` (mesmo padrão do projeto original: `ANI_CLI_QUALITY`, `ANI_CLI_PLAYER`, `ANI_CLI_MODE` etc), este fork lê um arquivo opcional:

```sh
# ~/.config/ani-cli/config  (ou $ANI_CLI_CONFIG)
ANI_CLI_QUALITY=720
ANI_CLI_PROVIDER=animefire
ANI_CLI_CACHE_TTL=10
```

Variáveis novas deste fork: `ANI_CLI_PROVIDER`, `ANI_CLI_CACHE_DIR`, `ANI_CLI_CACHE_TTL`, `ANI_CLI_TIMEOUT`, `ANI_CLI_CONFIG`.

## FAQ

- **Posso trocar idioma de legenda?** Não. Legenda vem embutida no vídeo, o site define, não o ani-cli.
- **Como funciona o streaming?** Reproduz direto via URL, nada é salvo em disco (a menos que use `-d`).
- **Cache guarda vídeo?** Não. Só busca e lista de episódios (TTL curto). Links de vídeo nunca são cacheados, são assinados e expiram.
- **Por que meu site favorito não está na lista?** Provavelmente foi investigado e bloqueado por infraestrutura de terceiro, ver seção de limitações acima. Contribuições de engenharia reversa são bem-vindas.

Mais perguntas gerais (instalação, players, etc): [FAQ do projeto original](https://github.com/pystardust/ani-cli#faq).

## Aviso legal

Este projeto acessa conteúdo público hospedado por terceiros não afiliados, da mesma forma que um navegador comum, só que de forma mais direta e específica. Uso por sua conta e risco, conforme as leis do seu país. Ver [disclaimer.md](disclaimer.md) do projeto original pra detalhes completos, incluindo processo de DMCA/copyright.

## Licença e créditos

Licenciado sob **GPL-3.0**, mesma licença do projeto original. Ver [LICENSE](LICENSE).

- **Projeto original:** [pystardust/ani-cli](https://github.com/pystardust/ani-cli), por [pystardust](https://github.com/pystardust)/[port19x](https://github.com/port19x) e colaboradores
- **Este fork:** mudanças de multi-provider, histórico multi-fonte e utilitários de QoL descritos acima

Por ser GPL-3.0, este fork é e continua sendo software livre: você pode copiá-lo, modificá-lo e redistribuí-lo, desde que mantenha a mesma licença e os créditos ao projeto original. Ver texto completo em [LICENSE](LICENSE).
