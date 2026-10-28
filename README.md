# cachyos — skill do Claude Code para CachyOS + Hyprland

Skill para o [Claude Code](https://claude.com/claude-code) que ensina o agente a configurar, manter e diagnosticar um desktop **CachyOS** com **Hyprland** (config em Lua), shell **Noctalia** e sessão **uwsm**.

A skill existe porque este ambiente contraria boa parte dos tutoriais da internet:

| O que a maioria dos tutoriais assume | O que este sistema usa |
|---|---|
| `hyprland.conf` (hyprlang), `bind = ...`, `windowrulev2 = ...` | `hyprland.lua` + módulos `config/*.lua` (Hyprland 0.55+) |
| Waybar, rofi/wofi, swaync, hyprlock | Noctalia (barra, launcher, notificações, OSD, lock), controlado por `noctalia msg` |
| Variáveis em `env = ...` no config do Hyprland | `~/.config/uwsm/env` |
| `hyprctl dispatch <nome> <args>` | `hyprctl eval 'hl.dispatch(hl.dsp...)'` |

Sem a skill, o modelo tende a responder de memória com sintaxe obsoleta. Com ela, ele lê o guia do tema, verifica o estado real do sistema e só então edita.

> **Nota:** os guias são genéricos, mas a skill também guarda fatos medidos da máquina em que roda (monitores, GPU, atalhos, periféricos). Esses fatos ficam em `references/local/`, que está no `.gitignore` e não é publicado. Veja [Adaptar para o seu sistema](#adaptar-para-o-seu-sistema).

## O que ela cobre

- **Hyprland:** atalhos, monitores, regras de janela/workspace/layer, autostart, input/teclado, animações, decorações.
- **Noctalia:** barra e widgets, painéis, tema e cores, screenshots, lock/idle, greeter.
- **Sistema:** pacman/paru, repositórios otimizados do CachyOS, kernels, btrfs + snapper, rEFInd, NVIDIA, jogos, serviços systemd, áudio.
- **Troubleshooting** em geral ("como faço X no meu Linux"), mesmo quando o pedido não cita CachyOS nem Hyprland.

## Estrutura

```
cachyos/
├── SKILL.md                     # ponto de entrada: fatos essenciais, índice dos guias e regras de trabalho
├── LICENSE
└── references/
    ├── hyprland.md              # onde editar cada coisa, sintaxe Lua real, armadilhas, comandos hyprctl
    ├── noctalia.md              # config.toml, comandos IPC, integração com o Hyprland, screenshots
    ├── system.md                # pacotes, kernel, btrfs/snapper, rEFInd, NVIDIA, jogos, logs
    ├── docs.md                  # links oficiais verificados (wiki Hyprland, wiki CachyOS, Noctalia, ArchWiki)
    ├── learnings.md             # diário de descobertas gerais: data, sintoma, causa e solução
    ├── environment.example.md   # modelo do inventário da máquina
    └── local/                   # NÃO versionado (.gitignore)
        ├── environment.md       # fatos medidos desta máquina (versões, hardware, apps, monitores, atalhos)
        └── learnings.md         # descobertas que só valem para este hardware/config
```

O `SKILL.md` fica curto de propósito. O agente só abre o guia de referência do tema pedido, o que economiza contexto.

## Como o agente trabalha com a skill

1. **Verifica antes de assumir:** `hyprctl version`, `hyprctl monitors`, `pacman -Qi`... O CachyOS é rolling release e a sintaxe do Hyprland mudou bastante entre 0.54 e 0.55+.
2. **Edita o menor escopo possível**, mantendo o estilo do arquivo (`mainMod`, `noctCall`, `launchPrefix`, `digitCode()`).
3. **Faz backup** antes de mudanças não triviais (`arquivo.bak-AAAA-MM-DD`).
4. **Valida:** `hyprctl configerrors` para o Hyprland; `noctalia config` e `noctalia msg config-reload` para o Noctalia.
5. **Pede confirmação** antes de ações difíceis de reverter: atualização ou remoção de pacotes, bootloader, fstab, initramfs, kernel, snapper, drivers NVIDIA.
6. **Prefere documentação a memória:** consulta `docs.md`, `man` e `--help` da versão instalada.

### Skill autoatualizável

Ao terminar cada tarefa, o agente se pergunta se algo mudou ou se aprendeu algo novo, e atualiza a skill na mesma sessão:

- mudança no sistema → `local/environment.md` e o guia do tema;
- descoberta que vale para qualquer sistema → `learnings.md` (público), escrita de forma genérica;
- descoberta específica do hardware ou da config → `local/learnings.md` (privado);
- informação errada ou desatualizada → corrigida ou removida na hora.

Regras: só registrar o que foi verificado, nunca gravar segredos, tokens ou UUIDs de disco, não citar modelos de hardware fora de `local/` e manter o `SKILL.md` enxuto.

Na prática, o repositório muda com o uso. Revise o `git diff` antes de cada commit.

## Instalação

Requisitos: Claude Code instalado e um sistema CachyOS com Hyprland 0.55+ (config em Lua). O pacote base que gera essa configuração é o `cachyos-hypr-noctalia`.

```fish
git clone https://github.com/celsottg/cachyos.git ~/.claude/skills/cachyos
```

O Claude Code carrega skills de `~/.claude/skills/<nome>/SKILL.md` automaticamente. A skill é acionada por pedidos sobre o sistema ou o desktop. Também dá para chamá-la explicitamente com `/cachyos`.

Exemplos de pedidos:

- "cria um atalho Super+B que abre o btop no kitty"
- "o segundo monitor está à direita, mas fisicamente fica à esquerda"
- "adiciona o widget de bateria na barra"
- "meu headset USB não sai som"
- "quero fazer um snapshot antes de atualizar o kernel"

## Adaptar para o seu sistema

Os guias `hyprland.md`, `noctalia.md`, `system.md`, `docs.md` e `learnings.md` valem para qualquer instalação CachyOS + Hyprland Lua + Noctalia. Depois de clonar:

1. **Gere o inventário da sua máquina.** Peça ao Claude: *"levante o ambiente deste PC e crie o references/local/environment.md a partir do modelo"*. Ele roda `hyprctl`, `pacman -Q` e lê `~/.config/hypr/config/`. O arquivo fica só no seu disco.
2. **`SKILL.md`, seção "Fatos do ambiente":** ajuste layout de teclado, GPU, bootloader e shell, se forem diferentes.

Sem NVIDIA, sem rEFInd ou sem btrfs, as seções correspondentes de `system.md` simplesmente não se aplicam.

## Escopo

É uma skill de **usuário final**: personalização e manutenção do próprio sistema. Não serve para desenvolver o código-fonte do CachyOS, do Hyprland ou do Noctalia.

## Licença

[MIT](LICENSE)

## Referências

- [Wiki do Hyprland](https://wiki.hypr.land/Configuring/Start/)
- [Wiki do CachyOS](https://wiki.cachyos.org/)
- [Documentação do Noctalia](https://docs.noctalia.dev)
- [ArchWiki](https://wiki.archlinux.org)
- Inspirada na skill [omarchy](https://www.skills.sh/omacom/omarchy/omarchy)
