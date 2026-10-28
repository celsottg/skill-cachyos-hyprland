---
name: cachyos
description: Gerencia e personaliza um sistema CachyOS com Hyprland (config em Lua, shell Noctalia, uwsm). Use sempre que o usuário pedir algo sobre o sistema CachyOS ou o desktop Hyprland - atalhos/keybinds, monitores, regras de janela e workspace, autostart, variáveis de ambiente, barra/painéis/tema do Noctalia, pacotes (pacman, paru, repositórios CachyOS), kernels, btrfs/snapper, rEFInd, NVIDIA, jogos, áudio, rede, troubleshooting ou "como faço X no meu Linux". Use mesmo que o usuário não diga "CachyOS" ou "Hyprland" explicitamente, se o pedido for sobre configurar, consertar ou entender este computador. Também deve ser usada para atualizar a si mesma quando algo mudar ou for descoberto.
---

# CachyOS + Hyprland

Skill para personalização e manutenção do sistema **do usuário final**, num CachyOS com Hyprland. Não serve para contribuir com o código-fonte do CachyOS ou do Hyprland.

O ambiente deste computador tem particularidades que contrariam o que a maioria dos tutoriais da internet diz (config em **Lua**, não `hyprland.conf`; barra e launcher são o **Noctalia**, não Waybar/rofi). Por isso: leia o guia certo antes de agir, não responda de memória.

## Fatos do ambiente (resumo)

Detalhes e versões em `references/environment.md`. O essencial:

- Config do Hyprland: `~/.config/hypr/hyprland.lua`, que faz `require("config.*")` dos módulos em `~/.config/hypr/config/`. Sintaxe hyprlang (`hyprland.conf`) está obsoleta desde a 0.55.
- Shell/barra/launcher/OSD/lock: **Noctalia** (`~/.config/noctalia/config.toml`, controle via `noctalia msg ...`).
- Sessão via **uwsm**; variáveis de ambiente vão em `~/.config/uwsm/env`, não em `environment.lua`.
- Teclado: layout **`br` (ABNT2)** definido em `inputs.lua`. Atalhos numéricos usam `code:N` (keycode físico), que independe do layout; mantenha esse padrão.
- Pacote base do desktop: `cachyos-hypr-noctalia` (os defaults ficam em `/etc/skel/.config/hypr/`, úteis para comparar/restaurar).
- Btrfs + snapper, bootloader rEFInd (`refind-btrfs`), GPU NVIDIA (kernel `linux-cachyos-nvidia-open`), `paru` como AUR helper, shell fish.

## Guias por tema (leia o que se aplica antes de começar)

| Tema | Arquivo |
|---|---|
| Keybinds, monitores, window/workspace/layer rules, autostart, env, input, animações (Lua) | `references/hyprland.md` |
| Barra, widgets, painéis, tema/cores, `noctalia msg`, lock/idle, screenshots | `references/noctalia.md` |
| Pacotes, kernels, repositórios otimizados, btrfs/snapper, rEFInd, NVIDIA, jogos, serviços | `references/system.md` |
| Fatos medidos deste PC (versões, hardware, apps padrão, layout de arquivos) | `references/environment.md` |
| Links oficiais de documentação, para consultar quando o guia local não bastar | `references/docs.md` |
| Descobertas, armadilhas e correções acumuladas | `references/learnings.md` |

Se o pedido cruza temas (ex.: atalho que chama um comando do Noctalia), leia ambos.

## Como trabalhar

1. **Verifique o estado real antes de assumir.** Rode `hyprctl version`, `hyprctl monitors`, `hyprctl binds`, `pacman -Qi <pkg>` etc. As versões mudam (rolling release) e a sintaxe do Hyprland mudou bastante entre 0.54 e 0.55+.
2. **Edite o módulo certo**, no menor escopo possível. Mantenha o estilo existente (indentação, comentários, uso de `mainMod`, `noctCall`, `launchPrefix`).
3. **Faça backup antes de mudanças não triviais** (`cp arquivo arquivo.bak-$(date +%F)`), ou lembre que snapper tem snapshots do `/`.
4. **Valide**: Hyprland recarrega a config sozinho ao salvar; confira erros com `hyprctl configerrors`. Para o Noctalia, `noctalia config ...` valida e `noctalia msg config-reload` recarrega.
5. **Confirme com o usuário** antes de ações difíceis de reverter: `pacman -Syu`/remoção de pacotes, mexer em bootloader, fstab, initramfs, kernel, snapper, ou mudar drivers NVIDIA. Mudanças em `~/.config` são baixo risco, mas diga o que mudou.
6. **Prefira documentação a memória.** Se o guia local não cobrir, consulte `references/docs.md` (wiki do Hyprland, wiki do CachyOS, docs do Noctalia, ArchWiki) e use `man`/`--help` do próprio sistema, que refletem a versão instalada.
7. **Responda em português**, com comandos exatos e o caminho dos arquivos alterados.

## Manter esta skill atualizada (obrigatório)

Esta skill é um documento vivo. Sempre que for usada, ao terminar a tarefa pergunte-se: *mudou algo ou aprendi algo que a próxima execução precisaria saber?* Se sim, **atualize a skill na mesma sessão**, sem esperar o usuário pedir:

- **Mudança no sistema** (pacote instalado/removido, versão nova, atalho criado, arquivo de config novo, app padrão trocado, monitor adicionado): atualize `references/environment.md` e o guia do tema.
- **Descoberta nova** (comando que funciona, armadilha, erro e sua causa, diferença entre a documentação e o que ocorre de fato, sintaxe que mudou): acrescente em `references/learnings.md` com data (`AAAA-MM-DD`), o sintoma e a solução. Se a descoberta corrigir algo escrito num guia, corrija o guia e não só acrescente.
- **Informação desatualizada ou errada** encontrada em qualquer arquivo: corrija ou remova na hora. Conteúdo velho é pior que ausência de conteúdo.

Regras para as atualizações: edições pequenas e pontuais; só registre o que é verificado (testado ou lido na doc/`--help`); não grave segredos, senhas, tokens nem UUIDs de disco; mantenha este `SKILL.md` curto e leve os detalhes para `references/`. Ao final, diga ao usuário em uma linha o que foi atualizado na skill.
