---
description: "Noctalia 快捷键对应表 + 配置参考（DMS ↔ Noctalia 键位映射、bar/widget/theme 配置）"
tags:
  - noctalia
  - dms
  - keybinds
  - config
  - reference
created: "2026-08-12"
updated: "2026--08-12"
---

# Noctalia 快捷键对应表 + 配置参考

## 一、Shell 功能键对应表（DMS ↔ Noctalia）

这些是"shell 层"功能（启动器/设置/壁纸/电源/媒体），窗口管理键（聚焦/移动/浮动等）是 niri 内置的，和 shell 无关，切换不影响。

| 功能 | DMS 键位 | DMS 命令 | Noctalia 命令 | Noctalia 键位（一致） |
|---|---|---|---|---|
| 应用启动器 | Mod+Z | `dms ipc call spotlight toggle \|\| fuzzel` | `noctalia msg panel-toggle launcher` | Mod+Z |
| 设置中心 | Mod+F2 | `dms ipc call settings focusOrToggle` | `noctalia msg settings-toggle` | Mod+F2 |
| 壁纸选择 | Mod+Alt+W | `dms ipc call dankdash wallpaper` | `noctalia msg panel-toggle wallpaper` | Mod+Alt+W |
| 电源/会话菜单 | Super+X | `dms ipc call powermenu toggle` | `noctalia msg panel-toggle session` | Super+X |
| 窗口切换器 | Alt+Tab | dms alttab.kdl | `noctalia msg window-switcher` | Alt+Tab |
| 锁屏 | (swayidle) | `dms ipc call lock lock` | `noctalia msg session lock` | (swayidle 改) |
| 音量+ | XF86AudioRaiseVolume | dms binds | `noctalia msg volume-up` | 同键 |
| 音量- | XF86AudioLowerVolume | dms binds | `noctalia msg volume-down` | 同键 |
| 静音 | XF86AudioMute | dms binds | `noctalia msg volume-mute` | 同键 |
| 麦克风静音 | XF86AudioMicMute | dms binds | `noctalia msg mic-mute` | 同键 |
| 下一首 | XF86AudioNext | dms binds | `noctalia msg media next` | 同键 |
| 上一首 | XF86AudioPrev | dms binds | `noctalia msg media previous` | 同键 |
| 播放/暂停 | XF86AudioPlay | dms binds | `noctalia msg media toggle` | 同键 |
| 亮度+ | XF86MonBrightnessUp | dms binds | `noctalia msg brightness-up` | 同键 |
| 亮度- | XF86MonBrightnessDown | dms binds | `noctalia msg brightness-down` | 同键 |

**设计原则**：键位完全一致，启用谁谁生效。因为只会启用一个 shell，不存在冲突。

## 二、Noctalia Bar 配置参考

基于官方文档 + CachyOS 配置。

### Bar 结构

```toml
[bar]
order = ["main"]                    # layer-shell 创建顺序

[bar.main]
position = "top"                    # top | bottom | left | right
enabled = true                      # 并存模式设 false（DMS 管顶栏）
auto_hide = false                  # 鼠标离开滑出
smart_auto_hide = false             # 空工作区显示，有窗口隐藏
reserve_space = true                # 占位（推开窗口）
layer = "top"                       # top | overlay（overlay 在全屏之上）
thickness = 34                      # 栏粗细
background_opacity = 1.0             # 0.0 透明 ~ 1.0 不透明
radius = 12                         # 圆角
margin_ends = 100                   # 两端缩进
margin_edge = 0                     # 离屏幕边缘距离
padding = 14                        # 内边距
widget_spacing = 6                 # widget 间距
scale = 1.0                         # 内容缩放
font_weight = 500                   # 字重

# widget 布局（start/center/end 三段）
start  = ["launcher", "wallpaper", "workspaces"]
center = ["clock"]
end    = ["media", "tray", "notifications", "clipboard", "network", "bluetooth", "volume", "brightness", "battery", "control-center", "session"]
```

### 常用 Widget 配置

```toml
# 时钟
[widget.clock]
format = "{:%H:%M}"
tooltip_format = "{:%A, %B %d, %Y}"

# 工作区
[widget.workspaces]
style = "regular"                  # regular | minimal | focus_hint
display = "id"                     # none | id | name
max_label_chars = 2
hide_when_empty = true

# 托盘
[widget.tray]
hidden = ["nm-applet", "blueman"]  # 隐藏的托盘项
pinned = ["nm-applet"]             # 固定显示
drawer = true                      # 抽屉式

# 电池
[widget.battery]
display_mode = "icon"              # icon | graphic
show_label = true
warning_threshold = 20
```

### Capsule（widget 胶囊样式）

```toml
[bar.main]
capsule = true
capsule_fill = "surface_variant"
capsule_opacity = 0.9
capsule_radius = 8.0

# 单个 widget 覆盖
[widget.volume]
capsule_fill = "secondary"
capsule_radius = 0.0               # 这个 widget 方角
```

## 三、Theme 配置

```toml
[theme]
mode = "dark"                      # dark | light

# 壁纸驱动配色（自动从壁纸提取）
# Noctalia 自动生成终端/编辑器/浏览器主题
[theme.templates.custom_colors]
warning = "#f97316"
```

## 四、输入法（已修复）

niri environment 补全了：
```kdl
environment {
    XMODIFIERS "@im=fcitx"
    GTK_IM_MODULE "fcitx"      # ← 补的
    QT_IM_MODULE "fcitx"       # ← 补的
}
```
切换脚本切完后重启 fcitx5，确保继承新环境变量。

## 五、参考配置来源

1. **CachyOS 官方**：github.com/cachyos/cachyos-niri-noctalia（niri cfg/*.kdl + noctalia config.toml）
2. **Noctalia 文档**：docs.noctalia.dev（bar/widgets/theme/theming）
3. **本地**：~/.config/noctalia/config.toml（并存模式，bar.enabled=false）
