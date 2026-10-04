# 单文件内置截图组件

日期：2026-10-04

## 问题与决定

当前唯一外部脚本为 jsDelivr 的 html2canvas 1.4.1，冷启动断网时无法生成 PNG。将同版本发行文件原样内嵌进 index.html，让下载的单个 HTML 在没有浏览器缓存的情况下也能生成图片。不添加安装、构建、后端、Service Worker 或备用 CDN。

使用 npm 官方 registry 的 html2canvas 1.4.1 tarball 中 dist/html2canvas.min.js，与原 CDN 返回内容逐字节一致。库代码独立放在带版本的 script 块，业务脚本仍单独维护；完整上游 MIT 许可及原版权头随单文件分发。

- 源包：https://registry.npmjs.org/html2canvas/-/html2canvas-1.4.1.tgz
- 上游：https://github.com/niklasvh/html2canvas/releases/tag/v1.4.1
- 许可：https://github.com/niklasvh/html2canvas/blob/v1.4.1/LICENSE
- 原始 JS：198689 字节
- SHA-256：e87e550794322e574a1fda0c1549a3c70dae5a93d9113417a429016838eab8cb

## 行为与边界

导出算法、数据格式、主题、缓存和存储键均不变。库随文档同步加载，不再等待网络。保留组件不可用的错误分支，提示重新下载完整 HTML 和先备份数据，不再要求联网刷新。

离线支持适用于保存到本地的完整 HTML、字母头像和已上传内嵌的本地图片；外部图片 URL 仍依赖网络及跨域许可。在线网站首次访问仍需下载网页，不承诺离线导航缓存。剪贴板支持仍由浏览器能力和权限决定。

代价是 HTML 增加约 195 KiB；无需单独分发 JS 文件。更新依赖时应从正式发布包核对版本、原文、SHA-256 和完整许可证，再验证真实 PNG 回归，不手改压缩库内部代码。

## 验证计划

- 无缓存新浏览器上下文，启动前断网，旧版本复现失败、新版 file:// 生成真实 PNG。
- 禁止所有 HTTP(S) 请求，验证新版本本地素材流程没有网络请求。
- 四主题、透明、单条、组合及背景合成与旧版本同库生成的 PNG 全 RGBA 通道一致。
- 本地上传图、预览下载、真实剪贴板写入、JSON 下载回导及刷新保存。
- 组件损坏时错误提示和 JSON 备份可用；桌面/手机截图检查。

28 项 Chromium 检查通过，错误列表为空。旧版在启动前断网的新上下文中复现无法生成；新版隔离目录中仅含 HTML 文件，启动前设 offline 后成功生成 1080px PNG。四主题透明、单条、组合、背景合成的新旧实际 PNG 均逐 RGBA 通道一致；750px 透明与 750×600 背景合成尺寸正确。

本地头像/背景上传、预览同字节下载、显式授予测试剪贴板权限后的真实写入（仅读取测试刚写入的图）、JSON 全状态回导、刷新恢复和组件缺失恢复均通过，整个新版流程无 HTTP(S) 请求。桌面 1440×1000 与 390×844 截图已自查，未发现本批布局问题；截图背景为测试用透明卡片合成图，颜色选择不代表固定主题。

源 HTML 由 174381 增至 374205 字节。内置 JS 与 npm 正式包及原 CDN 原文一致，完整 MIT 许可包含在 HTML 内。JS 语法和 diff 空白检查通过；独立收尾审查ship。

- 中间材料：F:/Codex/work/comment-mock-offline/（基线、下载原包、散列、验证脚本和 PNG）
- 报告：C:/Users/Harly/Documents/Codex/2026-10-04/new-chat-6/outputs/offline-verification.json
- 截图：同 outputs 下 offline-desktop.png、offline-mobile.png
- 测试边界：Chromium headless 的 file:// 冷启动；旧版对照通过测试路由提供原 CDN 字节。未验证其他浏览器、真实手机或外部粘贴应用。在线站点离线导航及外站资源不在离线承诺范围。
