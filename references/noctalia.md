# Noctalia (shell: barra, launcher, painéis, OSD, lock, screenshots)

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

## Barra atual
`start = launcher, clock, spacer_1, group:g1` · `center = workspaces, spacer_1, active_window` · `end = media, spacer_1, tray, notifications, network, volume, session`. Grupo `g1` (cápsula) = `temp, sysmon_2, sysmon_3`. Reordenar/adicionar widgets = editar essas listas.

## Integração com o Hyprland
- Os atalhos chamam `noctalia msg ...` (ver `binds.lua`, variável `noctCall`). Para adicionar um atalho a uma função do Noctalia: `hl.bind(mainMod .. " + KEY", hl.dsp.exec_cmd(noctCall .. "<comando>"))`.
- Inicia por `hl.exec_cmd("noctalia")` em `autostart.lua`; polkit agent é do próprio Noctalia (`polkit_agent = true`).
- Layer rules de blur/animação para os namespaces `noctalia-*` estão em `windowrules.lua`.
- Janelas de configuração do Noctalia são flutuantes (regra de classe `noctalia.Noctalia`).

## Screenshots
`Print` → `screenshot-region`, `Super+Print` → `screenshot-fullscreen`. Config atual: não copia para clipboard, não salva em arquivo, **envia por pipe ao `swash`** (visualizador/editor), que salva como `swash-AAAA-MM-DD_HH:MM:SS.png`. Mude em `[shell.screenshot]`.

## Sessão/greeter
Ações de sessão (lock, logout, ...) em `[[shell.session.actions]]`. O login usa `noctalia-greeter` via greetd; sincronizar tema/wallpaper: `noctalia msg greeter-sync`.
