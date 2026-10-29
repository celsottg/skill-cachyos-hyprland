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

## Integração com o Hyprland
- No template, os atalhos chamam `noctalia msg ...` (ver `binds.lua`, variável `noctCall`). Para adicionar um atalho a uma função do Noctalia: `hl.bind(mainMod .. " + KEY", hl.dsp.exec_cmd(noctCall .. "<comando>"))`.
- Inicia por `hl.exec_cmd("noctalia")` em `autostart.lua`; polkit agent é do próprio Noctalia (`polkit_agent = true`).
- Layer rules de blur/animação para os namespaces `noctalia-*` estão em `windowrules.lua`.
- Janelas de configuração do Noctalia são flutuantes (regra de classe `noctalia.Noctalia`).

## Screenshots
No template, `Print` → `screenshot-region` e `Super+Print` → `screenshot-fullscreen` (confira com `hyprctl binds`). Destino (clipboard, arquivo, pipe para um visualizador/editor como o `swash`) e pasta são definidos em `[shell.screenshot]`; leia essa seção para saber o que o usuário tem antes de responder onde as capturas vão parar.

## Sessão/greeter
Ações de sessão (lock, logout, ...) em `[[shell.session.actions]]`. O login usa `noctalia-greeter` via greetd; sincronizar tema/wallpaper: `noctalia msg greeter-sync`.
