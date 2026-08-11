## yazhongCar (雅众汽车坐垫 — Astro 版)

**路径**: /home/ivan/Projects/yazhongCar | **GitHub**: https://github.com/BobDylans/yazhongCar.git
**技术栈**: Astro 7 + Tailwind v3 (3.4.17) + DaisyUI v4 (4.12.23) + PostCSS + Cloudflare Pages。npm 包管理器。
**品牌**: 雅众(Yazhong), 高端定制专车专用汽车坐垫, 面向国际客户(英文)。单页落地页, WhatsApp 询盘。
**设计**: 暗调 cinematic/editorial, #0a0a0a + 金色 #c8a45c。Playfair Display + Inter 字体。全屏汽车图 cinematic hero。
**部署**: Cloudflare Pages → yazhong-car.pages.dev。自定义域名 rimhappywoods.top (Cloudflare Dashboard 手工添加, wrangler CLI 无 add-domain)。 <!-- created=2026-06-24, last=2026-06-30 -->
§
## yazhongCar — 构建陷阱

- **Tailwind 版本**: v3 + DaisyUI v4 + PostCSS 最稳定。Tailwind v4 + @tailwindcss/vite 在 Astro 7 下 CSS 空输出 (dist/client/_astro/ 目录为空)。
- **CSS 导入**: Layout.astro frontmatter 必须有 `import "../styles/global.css"` 才能被构建输出。
- **图片**: public/images/ 不在 git 中 (.gitignore), 每次 clone 需手动恢复。本地图片源在 ~/Downloads/car/。
- **按钮图标对齐**: inline-flex + items-center + leading-5 + gap-2.5 与 SVG(w-5 h-5) 等高对齐。 <!-- created=2026-06-24, last=2026-06-30 -->
