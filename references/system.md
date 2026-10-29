# Sistema CachyOS (pacotes, kernel, disco, boot, GPU)

Leia antes de mexer em pacotes, kernel, disco, boot, GPU, jogos ou serviços.

> Este guia é genérico e condicional: cada seção só vale se o componente existir no sistema (confirme com os comandos do Passo 0 do `SKILL.md`; fatos desta máquina ficam em `local/environment.md`). O conteúdo vem de conhecimento comum de Arch/CachyOS; confirme na wiki (`docs.md`) antes de ações destrutivas e troque por fatos verificados conforme usar.

## Pacotes
- `sudo pacman -Syu` atualiza tudo (rolling: nunca instale com `-Sy` sem `-u`). `paru` para AUR (`paru -Syu` atualiza repo + AUR).
- Consultas: `pacman -Qi/-Ql/-Qo <x>`, `pacman -Ss`, `paru -Ss`. Quem é dono do arquivo: `pacman -Qo /caminho`.
- Repositórios CachyOS otimizados (x86-64-v3/v4) e mirrorlists: pacotes `cachyos-v3-mirrorlist`, `cachyos-v4-mirrorlist`; ferramenta `cachyos-rate-mirrors` ranqueia mirrors. Config em `/etc/pacman.conf`.
- Interface gráfica: `cachyos-packageinstaller`. Hooks do CachyOS: `cachyos-hooks`. Não edite `/etc/pacman.conf` sem mostrar o diff ao usuário.
- Arquivos `.pacnew`: comparar com `pacdiff` (do `pacman-contrib`) após updates.

## Kernel
Veja o que há instalado: `uname -r` e `pacman -Q | grep -E '^linux'`. Variantes comuns: `linux-cachyos`, `linux-cachyos-lts` (útil como fallback), com sufixo `-nvidia-open` quando o driver vem junto. Gerenciador: `cachyos-kernel-manager`. Ao trocar kernel, o módulo de vídeo precisa casar com ele (ex.: `-nvidia-open` por kernel). Initramfs: mkinitcpio ou dracut (confirmar qual está em uso antes de mexer). `ananicy-cpp` + `cachyos-ananicy-rules` e `cachyos-settings` aplicam ajustes de desempenho (sysctl, udev).

## Btrfs + snapper
- Só se `findmnt -no FSTYPE /` for `btrfs` (o instalador do CachyOS permite outros, como ext4 ou xfs). Use `snapper list-configs` / `snapper -c root list`; GUI: `btrfs-assistant`. Pode exigir sudo.
- Com rEFInd, `refind-btrfs` gera entradas de boot a partir de snapshots no rEFInd (serviço systemd; o comando precisa de root).
- Antes de mudanças arriscadas no sistema, crie snapshot: `sudo snapper -c root create -d "antes de <mudança>"`.

## Boot
O bootloader varia (o instalador do CachyOS oferece GRUB, systemd-boot, rEFInd, entre outros). Identifique antes: `bootctl status`, `efibootmgr`, `ls /boot /efi`. Parâmetros atuais do kernel: `cat /proc/cmdline`. Onde editá-los depende do bootloader (ex.: rEFInd em `EFI/refind/` na ESP, achada com `findmnt`; GRUB em `/etc/default/grub` + `grub-mkconfig`; systemd-boot em `loader/entries/`). Alterar parâmetros do kernel = confirmar com o usuário e fazer backup do arquivo.

## NVIDIA
Só se `lspci` mostrar GPU NVIDIA. Drivers: `nvidia-open` via pacotes do kernel CachyOS, `nvidia-utils`, `nvidia-settings`, `nvidia-prime` (híbrido: use `prime-run <app>`). Diagnóstico: `nvidia-smi`, `journalctl -b | grep -i nvidia`, `lsmod | grep nvidia`. Para DRM modeset/Wayland, ver https://wiki.hypr.land/Nvidia/ e a wiki do CachyOS.

## Jogos
Config de janela em `windowrules.lua` (workspace `gaming`, `steam_app.*`, gamescope). Wiki: https://wiki.cachyos.org/configuration/gaming/.

## Serviços e logs
`systemctl --user status noctalia*`, `journalctl --user -b -u <unit>`, `journalctl -b -p err`. Apps lançados via uwsm viram unidades systemd de usuário (`launch_apps_as_systemd_services = true` no Noctalia).
