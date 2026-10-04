# PNG生成期间的样式快照

原流程已复制评论DOM与export设置，但截图克隆仍继承处理时的body主题类和自定义CSS变量。头像/背景处理或截图排队期间改变主题、颜色、字体、显示开关，会让已经开始生成的图片混入后续样式。

修正：在第一个异步步骤前创建离屏iframe，复制现行静态head样式、body与html的class/style属性及评论DOM。html2canvas从该独立文档读取样式，原编辑页面继续保留用户后续修改；评论文字/计数等仍使用生成开始时复制的DOM。无需临时切回用户页面的主题或禁用编辑。只在onclone恢复属性不足以冻结提前读取的回复伪元素和SVG颜色，已由真实PNG对比确认。

高度限制在独立文档中检查，以免实时页面字体变化造成错误放行或错误拒绝。记录本次隔离frame引用，在异常和成功的finally中清理，连同内部截图组件创建的frame一起释放；不查询或删除其他iframe。当前样式均为内联style，若未来引入外链样式或字体，须补齐资源复制和就绪检查。

范围：内置四主题、body内联自定义变量、显示类、根元素属性，以及已冻结的评论DOM/export设置。仍保留外部头像CORS、CDN和浏览器渲染能力限制。数据、存储和JSON schema均不变。

回滚：F:/Codex/work/comment-mock-snapshot/index.phase10.html，无数据迁移。

验证：27项Chromium检查通过。原版延迟截图时改主题/颜色/字体/隐藏属性，真实PNG从1080×602变成1080×668；修复后整段/单条/组合透明/背景合成均与基准PNG全RGBA一致，实时页面不回滚，后续新图反映新样式。另含自定义禁用、首个背景解码等待、冻结高度检查、失败保留旧预览及清理、JSON实际下载回导、390px无横向溢出。脚本与基线位于F:/Codex/work/comment-mock-snapshot/；未验证其他浏览器或真实手机。

独立收尾审查ship。额外四主题旧版/新版真实PNG全RGBA一致，见同目录review-compatibility.json。iframe初始文档为BackCompat，当前CSS未产生输出差异；未来引入依赖标准模式的CSS时应同步doctype并重新验证。
