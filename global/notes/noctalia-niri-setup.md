---
description: "Noctalia 与 Niri+DMS 并用/切换方案（冲突分析、四种模式、推荐配置）"
tags:
  - noctalia
  - niri
  - dms
  - wayland
  - shell
  - setup
created: "2026-08-11"
updated: "2026-08-11"
---

# Noctalia × Niri + DMS 并用方案

## 现状

- **Niri 26.04**（config.kdl，dotfiles 管理：~/Projects/dotfiles/niri/）
- **DMS（Dank Material Shell）**：systemd user service `dms.service`，Quickshell-based
  - `BusName=org.freedesktop.Notifications`（DBus 独占通知总线）
  - 深度嵌入 niri：`include "dms/{binds,alttab,supertab,windowrules,cursor,colors,outputs,layout}.kdl"`
  - spawn dsearch、dms ipc settings、swayidle 调 `dms ipc call lock lock`
- **Noctalia v5 Beta**：原生 Wayland+OpenGL ES，TOML 配置（~/.config/noctalia/*.toml），支持 profile 切换

## 冲突矩阵

| 功能 | DMS | Noctalia | 能否并用 |
|---|---|---|---|
| 通知总线 | DBus 独占（BusName） | 抢 org.freedesktop.Notifications | ❌ 互斥（先占者赢，另一个静默） |
| 顶栏（layer-shell top） | 画 | 画 `[bar.default]` | ⚠️ 可叠但视觉重叠 |
| 启动器 | dsearch | `noctalia msg panel-toggle launcher` | ✅ 不同键位可并用 |
| 控制中心/设置 | dms ipc settings | `noctalia msg settings-toggle` | ✅ 不同键位可并用 |
| 锁屏 | `dms ipc call lock lock` | `noctalia msg session lock` | ⚠️ 用一个，swayidle 调谁谁管 |
| 壁纸 | ？ | noctalia-wallpaper layer | ⚠️ 选一个管 |
| 工作区/foreign toplevels | 只读监听 | 只读监听 | ✅ 不冲突 |
| Bar 组件级禁用 | 需改 qml | `[bar.default] enabled=false` | Noctalia 更易模块化 |

## 四种并用模式

### 模式 1：分时切换（最干净，推荐）

两者都装，任一时刻只跑一个。脚本/systemd 切换。

```bash
# 切到 Noctalia
systemctl --user stop dms.service
noctalia &            # 或 systemctl --user start noctalia.service（若有）

# 切回 DMS
pkill noctalia
systemctl --user start dms.service
```

niri config.kdl 用条件 spawn 或两套 include。Noctalia 用 profile：
```toml
# ~/.config/noctalia/config.toml
[include]
autoload = false
files = ["profiles/dms-coexist.toml"]   # 精简配置：只启用部分组件
```

- ✅ 无冲突，各跑各的
- ❌ 切换要重启 shell（~2s），不能同时看

### 模式 2：功能分工（DMS 主，Noctalia 辅，并存）

DMS 继续管通知+顶栏+锁屏，Noctalia 只跑启动器+控制中心+桌面 widget。

Noctalia 配置（禁用 bar，通知反正抢不到）：
```toml
# ~/.config/noctalia/config.toml
[bar.default]
enabled = false          # 不画顶栏，DMS 管
# 通知：DMS 占着 DBus，Noctalia 自然不接管，无需配置
# 启动器/控制中心/桌面 widget 默认启用
```

niri binds（加 Noctalia 键位，不撞 DMS）：
```kdl
binds {
  Mod+Space { spawn-sh "noctalia msg panel-toggle launcher"; }   # DMS 用别的
  Mod+N     { spawn-sh "noctalia msg settings-toggle"; }         # 避开 Mod+F2(DMS设置)
}
```

- ✅ 保留 DMS 稳定部分，试 Noctalia 新功能，不破坏现有
- ❌ 两个 shell 进程，资源略增；Noctalia 通知废（DMS 占）
- ⚠️ Noctalia 启动器在 DMS 顶栏之外，独立 layer，视觉可接受

### 模式 3：Noctalia 主，DMS 辅

不推荐。DMS 是一体 Quickshell，不像 Noctalia 能 `enabled=false` 单独禁组件，难只跑部分。

### 模式 4：纯并存试运行（最乱，短期体验）

两个都跑全功能，接受通知归 DMS、顶栏叠加。

- ❌ 顶栏两个叠在一起，难看
- ❌ 键位冲突要仔细避让
- 只用于"先看看 Noctalia 长啥样"

## 推荐路径

**先模式 2（并存试运行 Noctalia 启动器/widget），满意再模式 1（分时切换）或全切。**

### 具体步骤（模式 2）

1. 装：`sudo pacman -S noctalia`
2. Noctalia 配置 `~/.config/noctalia/config.toml`：
   ```toml
   [bar.default]
   enabled = false
   [theme]
   mode = "dark"
   ```
3. niri config.kdl 加（不动 DMS 的 include）：
   ```kdl
   spawn-at-startup "noctalia"
   window-rule {
     match app-id="dev.noctalia.Noctalia"
     open-floating true
   }
   debug { honor-xdg-activation-with-invalid-serial }
   binds {
     Mod+Space { spawn-sh "noctalia msg panel-toggle launcher"; }
     Mod+N     { spawn-sh "noctalia msg settings-toggle"; }
   }
   ```
4. **不动 dms.service**（让它继续跑，管通知+顶栏+锁屏）
5. 重载 niri：`niri msg action reload`（或重登）

### 回滚

```bash
pkill noctalia
# 删 niri 里加的 Noctalia spawn/binds
niri msg action reload
```

## 关键注意

- **Noctalia v5 是 Beta**，可能偶尔崩，崩了 DMS 还在（模式 2 的好处）
- **DMS 配置在 dotfiles**，改 niri config.kdl 要同步回 ~/Projects/dotfiles/niri/
- **swayidle 锁屏**：模式 2 保持 `dms ipc call lock lock`（DMS 管），不改
- **Noctalia 通知在模式 2 下废**：DMS 占 DBus。要 Noctalia 通知必须停 DMS（转模式 1）
