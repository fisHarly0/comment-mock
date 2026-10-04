# 复制已经生成的预览图片

目标：生成后的PNG可以直接复制并粘贴到支持图片的应用，减少下载和寻找文件步骤。复制作用于已经生成的previewAsset.blob，不重新截图，因此与「保存这张PNG」保持同一快照。

交互：预览底部并列「复制图片」与原下载按钮；复制期间按钮禁用并显示进度，持久状态提示成功或失败。不支持图片剪贴板或没有写入权限时说明可保存PNG，不自动下载，不复制文本URL冒充图片。

边界：只在用户点击时调用Clipboard.write，传入PNG ClipboardItem，不读取剪贴板，不提前查询或申请权限。没有预览时不调用API。复制过程中编辑/关闭/替换预览不会改变已提交的Blob；旧任务完成不把新预览标成已复制，只用明确的旧快照提示反馈。无持久化或备份格式变化。

技术依据：[Clipboard API草案](https://w3c.github.io/clipboard-apis/#dom-clipboard-write)、[WebKit Async Clipboard API说明](https://webkit.org/blog/10855/async-clipboard-api/)。实现同步发起写入，保留用户手势；HTTPS/本地环境、权限和PNG支持由运行时检测及失败捕获决定，不保证所有浏览器或粘贴目标支持。系统可能转码图片，因此验收比较尺寸和RGBA像素，不要求剪贴板PNG文件字节不变。

验收：真实生成→复制→读取测试写入的PNG并逐像素比较、透明通道与尺寸；快照不重渲染/深浅检查底色不入图；重复点击、API缺失、权限拒绝、PNG不支持、构造失败；关闭/替换竞态、键盘与手机布局、旧下载及JSON回归。工作基线F:/Codex/work/comment-mock-clipboard/index.phase14.html。

结果：28项Chromium检查通过。测试上下文显式授予剪贴板权限，本地HTTP和file://真实写入成功；仅在本次自身PNG写入成功后读取测试剪贴板，750px透明PNG与下载图全RGBA一致。失败由API注入验证，另有非安全HTTP来源的真实能力检查。禁用复制按钮会丢失键盘焦点，已修为任务结束且仍为原预览、焦点为BODY时回到按钮；用户移到其他控件则不抢焦点。未验证实际外部粘贴应用、Firefox/Safari或真实移动系统。
