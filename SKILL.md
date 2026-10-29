---
name: cachyos
description: Gerencia e personaliza um sistema CachyOS com Hyprland (config em Lua, shell Noctalia, uwsm). Use sempre que o usuário pedir algo sobre o sistema CachyOS ou o desktop Hyprland - atalhos/keybinds, monitores, regras de janela e workspace, autostart, variáveis de ambiente, barra/painéis/tema do Noctalia, pacotes (pacman, paru, repositórios CachyOS), kernels, btrfs/snapper, rEFInd, NVIDIA, jogos, áudio, rede, troubleshooting ou "como faço X no meu Linux". Use mesmo que o usuário não diga "CachyOS" ou "Hyprland" explicitamente, se o pedido for sobre configurar, consertar ou entender este computador. Também deve ser usada para atualizar a si mesma quando algo mudar ou for descoberto.
---

# CachyOS + Hyprland

Skill para personalização e manutenção do sistema **do usuário final**, num CachyOS com Hyprland. Não serve para contribuir com o código-fonte do CachyOS ou do Hyprland.

Os guias descrevem o **setup padrão** do CachyOS com Hyprland: config em **Lua** (não `hyprland.conf`) e shell **Noctalia** (não Waybar/rofi), gerados pelo pacote `cachyos-hypr-noctalia`. Muitos tutoriais da internet não valem para ele. Mas cada pessoa pode ter mudado coisas, então: leia o guia certo antes de agir e **confirme o que o sistema realmente tem** (próxima seção). Não responda de memória.

## Regras críticas de segurança

- **Leitura é sempre segura; escrita não.** Nunca edite arquivos pertencentes a pacotes (`/usr/share/`, `/etc/skel/`, `/usr/lib/`): são sobrescritos nas atualizações. `/etc/skel/.config/` guarda os defaults do pacote, úteis para comparar (`diff -r`) ou restaurar, mas edite sempre a cópia em `~/.config/`.
- **Privilégio:** use `sudo` quando houver terminal para a senha; `pkexec` só se não houver como digitar a senha (processo gráfico ou de fundo). Não prefixe com `sudo` o que já pede elevação sozinho.
- **Não rode comandos interativos** (que pedem senha ou escolha no terminal) sem avisar; prefira flags não interativas.
- **Reset de config** (apagar/regenerar arquivos, copiar defaults por cima): sempre peça confirmação antes e faça backup.

## Passo 0: detectar o que este sistema tem

Antes de aplicar qualquer guia, confirme as premissas (rápido, só leitura):

```
ls ~/.config/hypr/                       # hyprland.lua = Lua; hyprland.conf = hyprlang (guia de sintaxe NÃO se aplica)
hyprctl version                          # versão: Lua exige 0.55+
pacman -Q cachyos-hypr-noctalia noctalia uwsm 2>&1   # quais peças do setup padrão existem
echo $XDG_CURRENT_DESKTOP; ls ~/.config/{noctalia,waybar,rofi,swaync} 2>/dev/null
findmnt -no FSTYPE /                     # btrfs? só então vale snapper
lspci | grep -iE 'vga|3d'                # GPU: só há NVIDIA se aparecer aqui
```

Se `references/local/environment.md` existir, leia-o: ele já registra o resultado. Se não existir, crie-o a partir de `references/environment.example.md` medindo o sistema.

Se algo divergir do padrão (ex.: `hyprland.conf` em vez de Lua, Waybar em vez de Noctalia, ext4 em vez de btrfs, GRUB/systemd-boot em vez de rEFInd), **avise o usuário** e use o guia só no que ainda se aplica, completando com `docs.md`. Os nomes de arquivos/variáveis dos guias (`config/binds.lua`, `noctCall`, `MONITOR1`...) são convenções do template padrão; se não existirem, ache o equivalente com `grep -r` em `~/.config/hypr/`.

## Guias por tema (leia o que se aplica antes de começar)

| Leia antes de... | Arquivo |
|---|---|
| Keybinds, monitores, window/workspace/layer rules, autostart, env, input, animações (Lua) | `references/hyprland.md` |
| Barra, widgets, painéis, tema/cores, `noctalia msg`, lock/idle, screenshots | `references/noctalia.md` |
| Pacotes, kernels, repositórios otimizados, btrfs/snapper, bootloader, NVIDIA, jogos, serviços | `references/system.md` |
| Consultar versões, hardware, apps padrão e layout de arquivos **desta** máquina | `references/local/environment.md` |
| Links oficiais, quando o guia local não bastar | `references/docs.md` |
| Armadilhas e correções que valem para qualquer sistema | `references/learnings.md` |
| Descobertas específicas deste hardware e desta config | `references/local/learnings.md` |

Se o pedido cruza temas (ex.: atalho que chama um comando do Noctalia), leia ambos. Os arquivos em `references/local/` são opcionais: se não existirem, siga sem eles.

## Como trabalhar

1. **Verifique o estado real antes de assumir.** Rode `hyprctl version`, `hyprctl monitors`, `hyprctl binds`, `pacman -Qi <pkg>` etc. As versões mudam (rolling release) e a sintaxe do Hyprland mudou bastante entre 0.54 e 0.55+.
2. **Edite o módulo certo**, no menor escopo possível. Mantenha o estilo existente (indentação, comentários e as variáveis auxiliares que o arquivo já usa, como `mainMod`).
3. **Faça backup antes de mudanças não triviais** (`cp arquivo arquivo.bak-$(date +%F)`), ou lembre que snapper tem snapshots do `/`.
4. **Valide**: Hyprland recarrega a config sozinho ao salvar; confira erros com `hyprctl configerrors`. Para o Noctalia, `noctalia config` valida e `noctalia msg config-reload` recarrega.
5. **Confirme com o usuário** antes de ações difíceis de reverter: `pacman -Syu`/remoção de pacotes, mexer em bootloader, fstab, initramfs, kernel, snapper, ou mudar drivers de vídeo. Mudanças em `~/.config` são baixo risco, mas diga o que mudou.
6. **Prefira documentação a memória.** Se o guia local não cobrir, consulte `references/docs.md` (wiki do Hyprland, wiki do CachyOS, docs do Noctalia, ArchWiki) e use `man`/`--help` do próprio sistema, que refletem a versão instalada.
7. **Responda em português**, com comandos exatos e o caminho dos arquivos alterados.

## Manter esta skill atualizada (obrigatório)

Esta skill é um documento vivo. Sempre que for usada, ao terminar a tarefa pergunte-se: *mudou algo ou aprendi algo que a próxima execução precisaria saber?* Se sim, **proponha a atualização na mesma sessão**, sem esperar o usuário pedir, seguindo o fluxo abaixo:

1. **Confirme antes de gravar.** Mostre ao usuário, de forma curta, o que entendeu e o que pretende registrar: o texto da entrada, em qual arquivo e se é público ou `local/`. Pergunte se o entendimento está certo e se pode gravar. Só edite a skill depois do "sim"; se ele corrigir algo, ajuste e confirme de novo.
2. **Grave** conforme as regras abaixo.
3. **Prepare o commit.** Depois de gravar, rode `git -C ~/.claude/skills/cachyos status` e `git diff`. Confira que não há marcadores de conflito (`<<<<<<<`) nem detalhes pessoais nos arquivos versionados, e então faça `git add` só dos arquivos que você alterou e `git commit` com uma mensagem curta em português (ex.: `learnings: headset USB sem som por PCM em 0%`). Se a mudança foi só em `references/local/`, não há commit: avise isso. Não faça `push` sem o usuário pedir; ao final, mostre o hash do commit e pergunte se pode enviar.

O que gravar e onde:

- **Mudança no sistema** (pacote instalado/removido, versão nova, atalho criado, arquivo de config novo, app padrão trocado, monitor adicionado): atualize `references/local/environment.md` e o guia do tema.
- **Descoberta nova** (comando que funciona, armadilha, erro e sua causa, diferença entre a documentação e o que ocorre de fato, sintaxe que mudou): acrescente com data (`AAAA-MM-DD`), o sintoma e a solução. Se vale para qualquer sistema, vai em `references/learnings.md`, escrita de forma genérica; se depende deste hardware ou desta config (nomes de monitores, modelos, caminhos de backup), vai em `references/local/learnings.md`. Se a descoberta corrigir algo escrito num guia, corrija o guia e não só acrescente.
- **Privacidade:** tudo fora de `references/local/` é publicado no GitHub. Nos guias e no `learnings.md` público, não cite modelos de hardware, nomes de cards, números de série nem detalhes pessoais.
- **Informação desatualizada ou errada** encontrada em qualquer arquivo: proponha a correção ou remoção na hora (com a mesma confirmação do passo 1). Conteúdo velho é pior que ausência de conteúdo.

Regras para as atualizações: edições pequenas e pontuais; só registre o que é verificado (testado ou lido na doc/`--help`); não grave segredos, senhas, tokens nem UUIDs de disco; mantenha este `SKILL.md` curto e leve os detalhes para `references/`. Ao final, diga ao usuário em uma linha o que foi atualizado na skill e o hash do commit.
