<<<<<<< HEAD
# Descobertas e armadilhas

Formato: `AAAA-MM-DD · tema · sintoma → causa/solução`. Mais recentes no topo. Corrija ou apague entradas que ficarem obsoletas.

- 2026-10-27 · monitores · `hl.monitor` com `output` nomeado, declarado depois da regra genérica, a sobrescreve; recarrega ao salvar e `hyprctl monitors` confirma a posição. Posição é no espaço global, sem vão: o monitor da direita começa em x = largura do da esquerda (HDMI 3440 à esquerda → eDP em x=3440). Antes de assumir qual lado, confirme com o usuário.
- 2026-10-27 · hyprland · Hyprland 0.56 usa config em Lua (`hyprland.lua` + `config/*.lua`); tutoriais com `hyprland.conf`/`windowrulev2` estão obsoletos.
- 2026-10-27 · teclado · CORREÇÃO: o teclado NÃO é AZERTY (era `us` por padrão; agora `br` ABNT2 via `kb_layout` em `inputs.lua`). O `digitCode()` em `binds.lua` vem do template do CachyOS e continua funcionando. Confirme com `hyprctl devices -j` (campo `layout`) em vez de supor.
- 2026-10-27 · noctalia · O Noctalia substitui waybar/rofi/swaync/hyprlock aqui; todo controle (volume, brilho, lock, screenshot) passa por `noctalia msg`.

- 2026-10-27 — Super+número trocava para workspace relativo ao monitor e só Super+Ctrl+número o fazia; Super+1/2/3 focava monitores e conflitava. Agora: Super+número = workspace relativo (`m~`); foco de monitor movido para Super+Ctrl+Alt+1/2/3. Backup: `~/.config/hypr/config/binds.lua.bak-2026-10-27`.

- 2026-10-27 — Workspaces só apareciam num monitor: `MONITOR1/2` vazios em `variables.lua` (= "qualquer monitor") e regras de workspace 4-6 do eDP comentadas em `workspaces.lua`. Corrigido: `MONITOR1="HDMI-A-1"`, `MONITOR2="eDP-1"` e regras 4-6 ativas. Workspace já ocupado (4) não se move sozinho. Backups `*.bak-2026-10-27`.

- 2026-10-27 — Solaar (gerenciador de mouse/teclado Logitech, pacote `solaar` do repo extra) iniciado no autostart do Hyprland em `config/autostart.lua`: `hl.exec_cmd("solaar --window=hide")`, depois do noctalia. Backup `autostart.lua.bak-2026-10-27`. `hyprctl configerrors` ficou vazio.

- 2026-10-27 — Super+4/5/6 não faziam nada: os loops de binds usam `NUM_WPM` (= 3), então só 1..3 eram criados. Além disso `m~N` é POSICIONAL dentro do monitor (no eDP, `m~4` caiu no workspace 6, que é o último existente), não pelo ID. Correção em `config/binds.lua`: 4..6 viram absolutos (`workspace = i`) nos loops de foco e de mover (Super+Shift+Ctrl/Alt). Teste: `hyprctl eval 'hl.dispatch(hl.dsp.focus({ workspace = 4 }))'` vai para o eDP-1. Backup `binds.lua.bak-2026-10-27-ws46`. Observação: `hyprctl dispatch <nome> <args>` não funciona nesta versão; use `hyprctl eval` com `hl.dispatch(hl.dsp...)`.

## Áudio: headset USB sem som com sink "RUNNING" (2026-10-28)
- **Sintoma:** headset USB (Fuxi-H3, card `alsa_card.usb-XiiSound_Technology_Corporation_Fuxi-H3-00`) é o sink padrão e está RUNNING, mas não sai som.
- **Causa:** o volume de hardware do canal ALSA `PCM` do card estava em 0% (`amixer -c <card> sget PCM`). O volume do sink no PipeWire (40%) é software e não compensa isso.
- **Correção:** `amixer -c <card> sset PCM 75% unmute`. Depois confira com `pactl get-sink-volume @DEFAULT_SINK@`.
- **Diagnóstico rápido:** `pactl get-sink-mute @DEFAULT_SINK@`, `pactl list short sinks`, `amixer -c <card> scontents`.

## Áudio: após religar o headset, volta a não sair som (2026-10-28)
- **Sintoma:** depois de desconectar e reconectar o headset USB, o som some de novo, mesmo com o sink padrão RUNNING.
- **Causa:** o controle `PCM` tem dois índices. `PCM,0` (Front L/R) e `PCM,1` (Mono, o master). O Mono pode voltar a 0% ao reconectar, e o kernel loga `broken mixer GET_CUR` para esse card. Ajustar só `PCM,0` não basta.
- **Correção:** `amixer -c <card> sset PCM,0 75% unmute` e `amixer -c <card> sset PCM,1 75% unmute`. Confira com `amixer -c <card> sget PCM`.
- **Correção persistente (2026-10-28):** serviço de usuário `fuxi-h3-volume.service` (`~/.config/systemd/user/`), que roda `~/.local/bin/fuxi-h3-volume.sh`. O script aplica o volume ao iniciar e de novo a cada evento `'new' on sink` do `pactl subscribe`. Usa o nome do card (`FuxiH3`), não o índice. Não precisa de root.
=======
# Descobertas e armadilhas (gerais)

Formato: `AAAA-MM-DD · tema · sintoma → causa/solução`. Mais recentes no topo. Corrija ou apague entradas que ficarem obsoletas.

Este arquivo é público: só entra aqui o que vale para qualquer CachyOS + Hyprland Lua + Noctalia. Descobertas que dependem desta máquina (nomes de monitores, modelos de hardware, caminhos de backup) vão em `local/learnings.md`.

- 2026-10-28 · áudio · Headset USB é o sink padrão e está RUNNING, mas não sai som → o volume de **hardware** do card ALSA (controle `PCM`) está em 0%; o volume do sink no PipeWire é software e não compensa. Alguns cards têm dois índices (`PCM,0` Front L/R e `PCM,1` Mono/master) e o Mono pode voltar a 0% ao reconectar (kernel loga `broken mixer GET_CUR`). Correção: `amixer -c <card> sset PCM,0 75% unmute` e `amixer -c <card> sset PCM,1 75% unmute`. Diagnóstico: `pactl list short sinks`, `pactl get-sink-mute @DEFAULT_SINK@`, `amixer -c <card> scontents`. Para persistir, um serviço systemd de usuário pode reaplicar o volume a cada evento `'new' on sink` do `pactl subscribe`, identificando o card pelo nome, não pelo índice.
- 2026-10-27 · hyprland · `hyprctl dispatch <nome> <args>` não funciona na 0.56 → use `hyprctl eval 'hl.dispatch(hl.dsp.focus({ workspace = 4 }))'`.
- 2026-10-27 · workspaces · `m~N` é **posicional** dentro do monitor, não o ID do workspace: num monitor com os workspaces 4 a 6, `m~4` cai no último existente. Os loops de binds do template usam `NUM_WPM`, então só cria atalhos de 1 a `NUM_WPM`; para mais, use workspaces absolutos (`workspace = i`).
- 2026-10-27 · workspaces · Workspaces aparecem só num monitor → `MONITOR1/2` vazios em `variables.lua` significam "qualquer monitor". Ponha o nome real da saída (`hyprctl monitors`) e ative as regras em `workspaces.lua`. Workspace já ocupado não muda de monitor sozinho.
- 2026-10-27 · monitores · `hl.monitor` com `output` nomeado, declarado depois da regra genérica (`position = "auto"`), a sobrescreve; recarrega ao salvar e `hyprctl monitors` confirma. Posição é no espaço global, sem vão: o monitor da direita começa em x = largura do da esquerda. Antes de assumir qual lado, confirme com o usuário.
- 2026-10-27 · teclado · Não suponha o layout: confira com `hyprctl devices -j` (campo `layout`). O `digitCode()` de `binds.lua` (template do CachyOS) usa keycode físico e funciona em qualquer layout.
- 2026-10-27 · hyprland · Hyprland 0.55+ usa config em Lua (`hyprland.lua` + `config/*.lua`); tutoriais com `hyprland.conf`/`windowrulev2` estão obsoletos.
- 2026-10-27 · noctalia · O Noctalia substitui waybar/rofi/swaync/hyprlock; todo controle (volume, brilho, lock, screenshot) passa por `noctalia msg`.
>>>>>>> 7e5a2d3 (Skill do Claude Code para CachyOS com Hyprland e Noctalia)
