# Noctalia (shell: barra, launcher, painéis, OSD, lock, screenshots)

Leia antes de mexer na barra, widgets, painéis, tema, lock/idle ou screenshots. Só se aplica se o Noctalia estiver instalado (`pacman -Q noctalia`); caso contrário, descubra qual shell/barra o usuário usa (`ls ~/.config`) e use `docs.md`.

Documentação: https://docs.noctalia.dev. A ajuda do binário instalado é a fonte mais fiel da versão local:
```
noctalia --help
noctalia msg            # lista todos os comandos IPC (muitos: bar-*, brightness-*, volume-*, media, panel-toggle, session, screenshot-*, color-scheme-*, config-get/set/reload, dock-*, caffeine-*, bluetooth-*, dpms-*, desktop-widgets-*, ...)
noctalia config --help  # validação da config
noctalia theme <imagem> # gera paleta a partir de imagem
```

## Config
- Arquivo do usuário: `~/.config/noctalia/config.toml` (TOML; seções `[bar.default]`, `[[bar.default.capsule_group]]`, `[shell]`, `[shell.panel]`, `[shell.screenshot]`, `[[shell.session.actions]]`, `[accessibility]`...).
- Também pode haver `settings.toml` (esquema de cores etc.) gerenciado pela UI; prefira a UI (`SUPER+Z`) para ajustes visuais, ou `noctalia msg config-set <caminho> <valor>` para script.
- Após editar à mão: `noctalia config` (validar) e `noctalia msg config-reload`.

## Barra
O layout fica em `[bar.default]` do `config.toml`, em listas `start`, `center` e `end` de nomes de widgets; grupos em cápsula usam `[[bar.default.capsule_group]]` e entram na lista como `group:<id>`. Reordenar/adicionar widgets = editar essas listas (leia a barra atual do usuário antes). A barra desta máquina está em `local/environment.md`.

### Widget dinâmico (saída de comando na barra) = plugin local
O `custom_button` é estático (`label`, `glyph`, `tooltip` e cliques em `[widget.<nome>.actions]` com `left = "exec <cmd>"`): não roda um comando periodicamente. Para algo no estilo `custom/*` do Waybar, crie um plugin Luau local (verificado em 2026-11-23, Noctalia 5.6):
1. Pasta `~/.local/share/noctalia/plugins/<nome>/`, descoberta como fonte `[local]` (`noctalia msg plugins list` deve listá-la).
2. `plugin.toml`: `id = "<autor>/<plugin>"`, `name`, `version = "1.0.0"`, `plugin_api = 3` (o mínimo que o plugin exige) e um bloco `[[widget]]` com `id` e `entry = "widget.luau"`.
3. `widget.luau`: `noctalia.runAsync(cmd, function(res) ... end)` (`res.exitCode`, `res.stdout`), `barWidget.setText/setGlyph/setTooltip/setColor` (glyph = nome do Tabler, cor = papel do tema, como `secondary`/`error`), `noctalia.setUpdateInterval(ms)`, e os callbacks globais `update()`, `onClick()` e `onRightClick()`. Use `noctalia.expandPath("~/...")` para os caminhos.
4. Ative com `noctalia msg plugins enable <autor>/<plugin>`, que grava em `~/.local/state/noctalia/settings.toml` e não no `config.toml`.
5. No `config.toml`: `[widget.<nome>]` com `type = "<autor>/<plugin>:<id do widget>"`, e `"<nome>"` na lista `start`/`center`/`end`. Depois rode `noctalia config validate` e `noctalia msg config-reload`.

Docs: https://docs.noctalia.dev/noctalia/plugins/development/ (manifest, runtime-api, plugin-api). Pacotes feitos para o Omarchy/Waybar (ex.: `omarchy-*`) costumam falhar no passo que edita `~/.config/waybar/config.jsonc`, mas os scripts deles podem ser usados assim.

## Integração com o Hyprland
- No template, os atalhos chamam `noctalia msg ...` (ver `binds.lua`, variável `noctCall`). Para adicionar um atalho a uma função do Noctalia: `hl.bind(mainMod .. " + KEY", hl.dsp.exec_cmd(noctCall .. "<comando>"))`.
- Inicia por `hl.exec_cmd("noctalia")` em `autostart.lua`; polkit agent é do próprio Noctalia (`polkit_agent = true`).
- Layer rules de blur/animação para os namespaces `noctalia-*` estão em `windowrules.lua`.
- Janelas de configuração do Noctalia são flutuantes (regra de classe `noctalia.Noctalia`).

## Screenshots
No template, `Print` → `screenshot-region` e `Super+Print` → `screenshot-fullscreen` (confira com `hyprctl binds`). Destino (clipboard, arquivo, pipe para um visualizador/editor como o `swash`) e pasta são definidos em `[shell.screenshot]`; leia essa seção para saber o que o usuário tem antes de responder onde as capturas vão parar.

## Wallpaper
- Trocar pela CLI (persiste): `noctalia msg wallpaper-set [connector] <path>`; sem `connector` aplica em todas as saídas. Para monitores de resoluções diferentes, gere uma imagem por saída e aplique por connector (`hyprctl monitors` mostra os nomes).
- Consultar: `noctalia msg wallpaper-get [connector]` (sem connector, mostra o default). Também: `wallpaper-next|previous|random [connector]`.
- Com `[theme] source = "wallpaper"`, trocar o wallpaper regenera as cores do tema (barra, terminal, GTK/Qt). Depois rode `noctalia msg greeter-sync` para levar à tela de login.

## Sessão/greeter
Ações de sessão (lock, logout, ...) em `[[shell.session.actions]]`. O login usa `noctalia-greeter` via greetd; sincronizar tema/wallpaper: `noctalia msg greeter-sync`.
