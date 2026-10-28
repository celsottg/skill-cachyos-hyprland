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
