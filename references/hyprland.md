# Hyprland (config em Lua, 0.55+)

Os exemplos abaixo foram extraídos da config real deste PC (`~/.config/hypr/config/*.lua`). Antes de inventar sintaxe, procure um exemplo parecido nesses arquivos; se não houver, consulte a wiki (`docs.md`). Muito conteúdo da internet ainda usa hyprlang (`bind = ...`, `windowrulev2 = ...`): **não copie** esse formato.

## Onde editar cada coisa
| Quero mexer em | Arquivo |
|---|---|
| Atalhos | `config/binds.lua` |
| Monitores (resolução, escala, posição) | `config/monitors.lua`; nomes de saída em `config/variables.lua` (`MONITOR1..3`) |
| Apps padrão, nº de workspaces por monitor | `config/variables.lua` |
| Regras de janela, layer e workspace | `config/windowrules.lua`, `config/workspaces.lua` |
| Programas que iniciam com a sessão | `config/autostart.lua` (ou XDG autostart) |
| Variáveis de ambiente | `~/.config/uwsm/env` (usamos uwsm; `environment.lua` só serviria sem uwsm) |
| Teclado/mouse/touchpad/gestos | `config/inputs.lua` |
| Gaps, bordas, blur, sombras | `config/decorations.lua`, `config/variables.lua` |
| Animações | `config/animations.lua` |

## Sintaxe observada

```lua
-- atalho: hl.bind(teclas, ação, opções?)
hl.bind(mainMod .. " + Q", hl.dsp.window.close())
hl.bind(mainMod .. " + Return", hl.dsp.exec_cmd(launchPrefix .. TERMINAL))
hl.bind("XF86AudioMute", hl.dsp.exec_cmd(noctCall .. "volume-mute"), { locked = true })
hl.bind(mainMod .. " + Minus", function() zoomfunction(-0.3) end, { repeating = true })

-- dispatchers comuns
hl.dsp.focus({ direction = "left" })        hl.dsp.focus({ workspace = "m+1" })
hl.dsp.window.move({ workspace = "special" })   hl.dsp.window.fullscreen({ mode = 1 })
hl.dsp.window.float({ action = "toggle" })  hl.dsp.layout("togglesplit")

-- configuração
hl.config({ input = { accel_profile = "flat" } })
hl.gesture({ fingers = 3, direction = "up", action = "fullscreen" })

-- monitor
hl.monitor({ output = "DP-1", mode = "2560x1440@144", position = "0x0", scale = "1" })

-- regra de janela (match + efeitos)
hl.window_rule({ match = { class = "^(pavucontrol)$" }, float = true, size = { "monitor_w*0.4", "monitor_h*0.5" } })
-- regra de layer / workspace
hl.layer_rule({ name = "x", match = { namespace = "^foo$" }, blur = true })
hl.workspace_rule({ workspace = "1", monitor = MONITOR1, default = true, persistent = true })

-- evento
hl.on("hyprland.start", function() hl.exec_cmd("noctalia") end)
```

Convenções do arquivo de binds: `mainMod = "SUPER"`, `noctCall = "noctalia msg "`, `launchPrefix = "uwsm app -- "` (todo app lançado por atalho passa por uwsm).

## Armadilhas
- **Atalhos com número** usam `digitCode(n)` (`code:10`..`code:19`, keycode físico) herdado do template do CachyOS, que vale para qualquer layout. Mantenha o padrão em vez de usar `"1"`, `"2"` direto.
- **Teclado**: `hl.config({ input = { kb_layout = "br" } })` em `inputs.lua` (ABNT2 é o padrão do layout `br`; `kb_variant` só para variantes como `nodeadkeys`). Vários layouts: `kb_layout = "br,us"` + `kb_options = "grp:alt_shift_toggle"`; o Noctalia tem `noctalia msg keyboard-layout-cycle`.
- Regex em `match` é aplicado à string inteira; use `^(...)$` e escape de `\\.` dentro de strings Lua. `negative:` inverte o match.
- Descobrir `class`/`title` de uma janela: `hyprctl clients` ou `hyprctl activewindow`.
- Nome de monitor: `hyprctl monitors`. `MONITOR1=""` significa "qualquer", então para regras por monitor específico coloque o nome real.

## Comandos úteis
```
hyprctl version | monitors | clients | activewindow | workspaces | binds
hyprctl configerrors          # erros da última carga da config
hyprctl reload                # recarrega manualmente
hyprctl dispatch <...>        # teste rápido (a sintaxe de dispatch pode variar na 0.55+; confira `hyprctl dispatch --help`)
hyprctl getoption <opção>
hyprctl kill                  # clicar numa janela para matá-la (Super+Esc)
```

## Fluxo para mudar algo
1. Ache o módulo certo na tabela. 2. Leia o arquivo antes de editar. 3. Edite. 4. `hyprctl configerrors` (deve vir vazio). 5. Teste o efeito (ex.: `hyprctl binds | grep ...`). 6. Registre em `learnings.md` se descobriu algo novo.

## NVIDIA + Hyprland
As variáveis (`GBM_BACKEND`, `__GLX_VENDOR_LIBRARY_NAME`, `LIBVA_DRIVER_NAME`, `__GL_GSYNC_ALLOWED`) existem comentadas em `~/.config/uwsm/env`. Ative só se houver problema real; veja https://wiki.hypr.land/Nvidia/ e `system.md`.
