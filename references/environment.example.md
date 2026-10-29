# Ambiente deste computador (modelo)

> Modelo público. A cópia real fica em `references/local/environment.md`, fora do git. Se ela não existir, copie este arquivo para lá e preencha com fatos medidos (comandos sugeridos em cada seção). Não grave segredos, senhas, tokens nem UUIDs de disco.

Última verificação completa: AAAA-MM-DD.

## Sistema
<!-- uname -r · pacman -Q | grep linux-cachyos · hyprctl version · pacman -Qi uwsm greetd · echo $SHELL · findmnt / · lspci | grep -i vga -->
- Kernel(s) instalado(s):
- Versão do Hyprland e portal:
- Sessão / display manager:
- Shell e terminal padrão:
- Bootloader e filesystem de `/` (snapper?):
- Parâmetros do kernel (`cat /proc/cmdline`):
- GPU e drivers (variáveis NVIDIA ativas ou comentadas?):
- AUR helper e layout de teclado (`hyprctl devices -j`):

## Arquivos de configuração
<!-- ls ~/.config/hypr/config/ ~/.config/ -->
```
~/.config/hypr/hyprland.lua          # entrada; só faz require dos módulos
~/.config/hypr/config/               # módulos .lua
~/.config/uwsm/env                   # variáveis de ambiente da sessão
~/.config/noctalia/config.toml       # barra, painéis, screenshot, sessão
```
Defaults originais do pacote: `/etc/skel/.config/hypr/` (comparar com `diff -r`).

## Apps padrão (`config/variables.lua`)
<!-- TERMINAL, FILE_MANAGER, BROWSER, EDITOR, MONITOR1..3, NUM_WPM -->

## Monitores (`config/monitors.lua`)
<!-- hyprctl monitors: nome da saída, modelo, modo, posição, escala -->

## Variáveis em `~/.config/uwsm/env`

## Atalhos principais (`config/binds.lua`)
<!-- hyprctl binds -->

## Noctalia (resumo do `config.toml`)
<!-- widgets da barra (start/center/end), ui_scale, destino dos screenshots (`[shell.screenshot]`) -->

## Serviços e autostart próprios
<!-- config/autostart.lua · systemctl --user list-unit-files --state=enabled -->
