# Sistema CachyOS (pacotes, kernel, disco, boot, GPU)

> Seções marcadas (geral) vêm de conhecimento comum de Arch/CachyOS e devem ser confirmadas na wiki (`docs.md`) antes de ações destrutivas. Troque por fatos verificados conforme usar.

## Pacotes
- `sudo pacman -Syu` atualiza tudo (rolling: nunca instale com `-Sy` sem `-u`). `paru` para AUR (`paru -Syu` atualiza repo + AUR).
- Consultas: `pacman -Qi/-Ql/-Qo <x>`, `pacman -Ss`, `paru -Ss`. Quem é dono do arquivo: `pacman -Qo /caminho`.
- Repositórios CachyOS otimizados (x86-64-v3/v4) e mirrorlists: pacotes `cachyos-v3-mirrorlist`, `cachyos-v4-mirrorlist`; ferramenta `cachyos-rate-mirrors` ranqueia mirrors. Config em `/etc/pacman.conf`.
- Interface gráfica: `cachyos-packageinstaller`. Hooks do CachyOS: `cachyos-hooks`. Não edite `/etc/pacman.conf` sem mostrar o diff ao usuário.
- Arquivos `.pacnew`: comparar com `pacdiff` (do `pacman-contrib`) após updates.

## Kernel
Instalados: `linux-cachyos(-nvidia-open)` e `linux-cachyos-lts(-nvidia-open)` (o LTS serve de fallback). Gerenciador: `cachyos-kernel-manager`. Ao trocar kernel, o módulo NVIDIA precisa casar (`-nvidia-open` por kernel). Initramfs: mkinitcpio ou dracut (confirmar qual está em uso antes de mexer). `ananicy-cpp` + `cachyos-ananicy-rules` e `cachyos-settings` aplicam ajustes de desempenho (sysctl, udev).

## Btrfs + snapper
- `/` é btrfs com subvolume `@`. `snapper list-configs` / `snapper -c root list`; GUI: `btrfs-assistant`. Pode exigir sudo.
- `refind-btrfs` gera entradas de boot a partir de snapshots no rEFInd (serviço systemd; o comando precisa de root).
- Antes de mudanças arriscadas no sistema, crie snapshot: `sudo snapper -c root create -d "antes de <mudança>"`.

## Boot (rEFInd)
Config em `/boot/EFI/refind/` ou `/efi/EFI/refind/` (confirmar o ponto de montagem da ESP com `findmnt`). A linha de comando do kernel atual inclui `quiet nowatchdog splash rw rootflags=subvol=/@`. Alterar parâmetros do kernel = confirmar com o usuário e fazer backup do arquivo.

## NVIDIA
Drivers: `nvidia-open` via pacotes do kernel CachyOS, `nvidia-utils`, `nvidia-settings`, `nvidia-prime` (híbrido: use `prime-run <app>`). Diagnóstico: `nvidia-smi`, `journalctl -b | grep -i nvidia`, `lsmod | grep nvidia`. Para DRM modeset/Wayland, ver https://wiki.hypr.land/Nvidia/ e a wiki do CachyOS.

## Jogos
Config de janela em `windowrules.lua` (workspace `gaming`, `steam_app.*`, gamescope). Wiki: https://wiki.cachyos.org/configuration/gaming/.

## Serviços e logs
`systemctl --user status noctalia*`, `journalctl --user -b -u <unit>`, `journalctl -b -p err`. Apps lançados via uwsm viram unidades systemd de usuário (`launch_apps_as_systemd_services = true` no Noctalia).
