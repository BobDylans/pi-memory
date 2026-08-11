---
description: "Noctalia 与 DMS 切换配置（niri 片段 + 切换脚本，默认不碰 DMS）"
tags:
  - noctalia
  - dms
  - niri
  - shell
  - config
  - dotfiles
created: "2026-08-12"
updated: "2026-08-12"
---

# Noctalia 切换配置（已实施）

## 文件清单（全部在 dotfiles 仓库，符号链接到 ~/.config/niri/）

```
~/Projects/dotfiles/niri/.config/niri/
├── noctalia/                    ← 新建，Noctalia 的 niri 配置片段
│   ├── autostart.kdl            spawn-at-startup "noctalia"（默认注释）
│   ├── binds.kdl                键位（与 DMS 对齐：Mod+Z/F2/Alt+W 等）
│   ├── windowrules.kdl          Noctalia 窗口浮动 + debug xdg-activation
│   └── layerrules.kdl           壁纸固定(Option 2) + 表面模糊 xray off
├── scripts/
│   └── noctalia-switch          ← 一键切换脚本
└── config.kdl                   ← 加了 Noctalia include 块（默认全注释）

~/.config/noctalia/
└── config.toml                  ← Noctalia 本体配置（bar.enabled=false，并存模式）
```

## 切换命令

```bash
noctalia-switch status   # 查看当前 shell
noctalia-switch start    # 切到 Noctalia（停 DMS，改 include，启 noctalia）
noctalia-switch stop     # 切回 DMS（停 noctalia，恢复 include，启 DMS）
```

脚本原理：sed 编辑 config.kdl 的 include 行（注释 dms/ ↔ 取消注释 noctalia/），备份在 config.kdl.noctalia-backup。

## 键位映射（DMS ↔ Noctalia）

| 键 | DMS | Noctalia |
|---|---|---|
| Mod+Z | dms ipc spotlight toggle | noctalia msg panel-toggle launcher |
| Mod+F2 | dms ipc settings focusOrToggle | noctalia msg settings-toggle |
| Mod+Alt+W | dms ipc dankdash wallpaper | noctalia msg panel-toggle wallpaper |
| Super+X | dms ipc powermenu toggle | noctalia msg panel-toggle session |
| Alt+Tab | dms alttab.kdl | noctalia msg window-switcher |
| XF86Audio* | dms binds | noctalia msg volume/media |

## 并存模式要点

- Noctalia `bar.enabled=false`：不画顶栏，DMS 管
- 通知：DMS 占 DBus BusName=org.freedesktop.Notifications，Noctalia 抢不到
- swayidle 调 `dms ipc call lock lock`：DMS 管锁屏，切 Noctalia 后需改脚本
- Noctalia v5.0.0，pacman extra 仓库

## 注意

- config.kdl 的 include 块默认全注释，不影响 DMS
- 切换脚本改 config.kdl 后 niri msg action reload
- config.kdl 在 dotfiles 和本地都要同步
