# 当前状态 / Current State

更新时间：2026-09-11

- 项目定位：单文件 HTML 评论区模拟器，用于截图和演示。
- 技术边界：原生 HTML/CSS/JavaScript，无构建、无后端；数据保存在浏览器 localStorage。
- 外部依赖：html2canvas 通过 jsDelivr CDN，仅用于 PNG 导出；评论编辑、主题和 JSON 备份可离线运行。
- 真源：`index.html`；README/TUTORIAL 为说明文档。

## 已知限制

- 清理浏览器数据、切换设备或无痕模式会丢失 localStorage 内容。
- 外部头像可能因 CORS 无法截图。
- CDN 不可用时 PNG 导出失败，但核心编辑功能应继续可用。

## 本轮状态

只新增治理档案，没有修改 `index.html`、README 或未跟踪 TUTORIAL。
