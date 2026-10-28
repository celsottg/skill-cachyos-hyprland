# Ambiente deste computador

> Atualize este arquivo quando algo mudar. Última verificação completa: 2026-10-27.

## Sistema
- CachyOS (rolling, base Arch), kernel `linux-cachyos-nvidia-open` (também instalados: `linux-cachyos`, `linux-cachyos-lts`, `linux-cachyos-lts-nvidia-open`).
- Hyprland 0.56.2, config em Lua. Portal: `xdg-desktop-portal-hyprland` (`~/.config/hypr/xdph.conf`).
- Sessão: uwsm. Display manager/greeter: `greetd` + `noctalia-greeter`.
- Shell do usuário: fish (`cachyos-fish-config`). Terminal padrão: kitty. Também instalado: alacritty.
- Bootloader: rEFInd (+ `refind-btrfs`). Filesystem `/`: btrfs, subvolume `@`, snapper + btrfs-assistant.
- GPU: NVIDIA (`nvidia-utils`, `nvidia-prime`, `libva-nvidia-driver`, lib32). As variáveis NVIDIA em `environment.lua` e `uwsm/env` estão **comentadas** (desligadas).
- AUR helper: `paru`. Teclado: `br` (ABNT2), definido em `config/inputs.lua` (`kb_layout`); console já era `br-abnt2`.

## Arquivos de configuração
```
~/.config/hypr/hyprland.lua          # entrada; só faz require dos módulos abaixo
~/.config/hypr/config/               # animations autostart binds colors decorations
                                     # environment inputs misc monitors variables
                                     # windowrules workspaces  (.lua)
~/.config/hypr/xdph.conf             # portal de captura
~/.config/uwsm/env                   # variáveis de ambiente da sessão
~/.config/noctalia/config.toml       # barra, painéis, screenshot, sessão
~/.config/{kitty,alacritty,fish,btop,micro,gtk-3.0,gtk-4.0,qt5ct,qt6ct}
```
Defaults originais do pacote: `/etc/skel/.config/hypr/` (comparar com `diff -r`).

## Apps padrão (`config/variables.lua`)
TERMINAL=kitty · FILE_MANAGER=dolphin · BROWSER=firefox (uwsm/env) · EDITOR=`gnome-text-editor --new-window` · CALCULATOR=gnome-calculator.
Monitores: `MONITOR1..3` vazios (qualquer saída); `NUM_WPM = 3` workspaces por monitor.

## Monitores (`config/monitors.lua`)
- `eDP-1`: tela do notebook, 1920x1080@144, posição 3440x0 (à direita do HDMI).
- `HDMI-A-1`: Samsung S34CG50 acoplado, 3440x1440@60, posição 0x0 (à esquerda).
- Regra genérica (`position="auto"`) vem primeiro como fallback; as duas específicas vêm depois e a sobrescrevem. Backup: `monitors.lua.bak-2026-10-27`.

## Variáveis em `~/.config/uwsm/env`
`BROWSER`, `TERM=xterm-kitty`, `QT_QPA_PLATFORM`, `QT_QPA_PLATFORMTHEME=qt6ct`, `ELECTRON_OZONE_PLATFORM_HINT=auto`, cursor Bibata-Modern-Ice tamanho 24.

## Atalhos principais (`config/binds.lua`, mainMod = SUPER)
Return terminal · E arquivos · W navegador · T editor · C calculadora · Space launcher (Noctalia) · X central de controle · Z configurações do Noctalia · Tab alternador de janelas · Q fechar · F/D tela cheia · L bloquear · V clipboard · A notificações · P color picker · Print/Super+Print screenshot · S/Shift+S scratchpad · Alt+Super+Space flutuar · Super+Alt+N foco no workspace N · Super+Ctrl+N workspace relativo ao monitor.
(Lista completa e atual: `hyprctl binds`.)

## Noctalia (resumo do `config.toml`)
Barra `default`: start = launcher, clock, group g1 (temp, sysmon_2, sysmon_3); center = workspaces, active_window; end = media, tray, notifications, network, volume, session. `ui_scale = 1.20`. Screenshots são enviados por pipe ao `swash`. Pacotes relacionados: `noctalia`, `noctalia-greeter`, `swash`.
