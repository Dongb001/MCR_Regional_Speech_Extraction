# 论文展示页：上传 GitHub Pages

这是已写好的纯静态论文展示站。没有后端、构建步骤或 CDN 依赖；下载并解压后可直接打开 `index.html` 预览。

## 网站内容

- 论文概览、区域语音提取任务说明及与最终 EXP6 代码对齐的 HTML 结构图（0.685 M 参数、6 blocks、5 FiLM、ConvTranspose2d）。
- 统一 profmean 的模型对比，以及单独标明 cell-median 口径的验证结果；不再直接展示初稿未溯源基线和 RCE。
- 最终连续半径 + 一致性模型 ep48 的五组精选音频，每组包含输入和 1 / 2 / 3 / 4 m 四档输出。
- 63 条混音的全部 0–4 m 查询，每 0.1 m 一个点；可切样本、查看单点和下载 CSV。
- 中文 / English 切换；手机、平板、电脑自适应。
- 原始论文 PDF、数据下载及实验来源记录。

## 最简单的发布方法（浏览器上传）

1. 解压网站 ZIP。找到同一层的 `index.html`、`styles.css`、`app.js` 和 `assets` 文件夹。
2. 登录 GitHub，右上角 **+ → New repository**。
3. 仓库名可填 `continuous-radius-demo`；为了使用免费 GitHub Pages，可选择 **Public**，然后创建仓库。
4. 打开新仓库，点击 **uploading an existing file**；若已有文件，选择 **Add file → Upload files**。
5. 将 **解压目录里面的全部文件和整个 `assets` 文件夹**一起拖入上传区。不要上传 ZIP 本身，不要再套一层 `paper-demo` 文件夹。提交到 `main` 分支。
6. 打开仓库 **Settings → Pages**。
7. 在 **Build and deployment** 中，Source 选择 **Deploy from a branch**；Branch 选择 **main**，目录选择 **/ (root)**，点击 **Save**。
8. 等 Pages 部署完成，点击页面中的 **Visit site**。通常地址为：

   `https://你的GitHub用户名.github.io/continuous-radius-demo/`

部署会有短暂等待；可在 **Actions** 查看 `pages build and deployment` 是否完成。若修改了仓库名，网址最后一段也随之变化。

## 上传后仓库应是这个样子

```text
continuous-radius-demo/
├── index.html
├── styles.css
├── app.js
├── README.md
├── .nojekyll
└── assets/
    ├── icon.svg
    ├── audio/                  # 25 个 WAV，请全部上传
    ├── data/
    │   ├── audio.js
    │   ├── curves.js
    │   ├── provenance.json
    │   ├── radius-full.csv     # 63 样本 × 41 半径，2583 行
    │   └── radius-short.csv    # 3 样本 × 41 半径，123 行
    └── paper/
        ├── manuscript.pdf
        ├── model-overview.png
        └── conditioning.png
```

`.nojekyll` 是可选的空文件，本包已提供。浏览器若隐藏它也不影响本页面其他普通路径的使用。

## 怎样更新

直接在 GitHub 替换对应文件并提交，Pages 会自动重新部署。首次上传时请完整上传 `assets`，不要只上传 HTML。

- 修改页面文字：`index.html` 内为默认英文，`app.js` 顶部 `zh` 为中文。
- 修改样式和颜色：`styles.css`。
- 更换论文初稿：用新 PDF 替换 `assets/paper/manuscript.pdf`。
- 作者信息：在 `index.html` 的标题区域填写真实作者及单位，再同步调整 `app.js` 中的中文说明。目前使用“研究初稿”标记，没有编写作者、会议或引用。
- 音频和指标：`assets/data/audio.js`；音频文件在 `assets/audio/`。若替换音频，需同时更新对应样本距离、指标和来源记录。
- 实验表：`index.html` 的 `results` 区域。模型对比使用 profmean；指标卡和带噪表使用 cell-median，两者不能直接比较数值或排名。所有已展示结果来自保存的 validation 诊断，不是本次新增推理。

## 本次模型与论文审计修订

- 主干：STFT → 递归归一化 → 实/虚/对数幅度 → Conv2d + LN → Block0 → FiLM0 → … → FiLM4 → Block5 → ConvTranspose2d → 反归一化 → ISTFT。连续版 CPU 实例化参数量为 685,070；初稿约 0.66 M 对应离散版数量级。
- 最终模型使用对称 target-RMS 一致性，不是非对称 anchor 版本。相邻目标集合相同且非空、RMS > 1e-4 才计算 MAE/RMS；无有效对时为零。训练每个 1 m 区间各抽一个 query，验证固定 1/2/3/4 m。
- 统一 profmean 后，Discrete+consistency / Continuous / Continuous+consistency 三行分别为 28.75/3.15/57.70、27.04/3.08/51.13、26.45/3.64/57.45。最后一行同时改变了训练 profile 权重，不能解释为纯 loss 消融。初稿离散无一致性基线来源未确认，留空而非补造数据。
- 指标卡和带噪表的 29.25/4.41/62.00 等数值采用每格中位数再平均；并非初稿 29.25/4.40/62.01 的精确复现。0–2 / 0–3 源套件并非固定两人/三人；0 源始终是纯噪声。
- RCE 的网格、pair 筛选、均值/中位数及最终 0.72% 来源没有统一，因此不提供未确认的 RCE 主指标。
- 原始 PDF、PNG、CSV、音频和 provenance.json 均保留原样；它们是来源材料，不因网页修订而自动成为已核实的最终稿。尤其 provenance 内既有哈希与 seed replay 声明是此前打包记录，本轮未重新逐项验证。
- 试听为预计算音频，曲线滑块不是实时推理。0.1 m 是采样步长，不是定位精度；总输出 RMS 平稳不能单独证明目标语音提取正确。
- 本轮未修改服务器项目、重跑模型或部署网站。

## 试听与图表说明

音频来自同一 ep48 检查点，按逐样本指标定向选出五组效果清楚的演示；它们不是随机抽样，也未进行主观听感评分。全部 WAV 保持下载前原始字节，未对输出单独归一化。空目标音频接近静音是正常现象。波形使用同组公共尺度。

试听使用 1 / 2 / 3 / 4 m 预计算音频；曲线浏览使用独立的 0.1 m 扫描数据，二者不是同一组输入。滑块用于浏览实测查询点，不是在浏览器中实时推理。

缩略 CSV 保留样本 1、8、12 的完整 41 个查询点，只缩小样本数量，没有省略任何 0.1 m 半径档位。

## 已验证范围

已在 Chromium 中检查桌面、手机布局、中文切换、半径选项、CSV 下载和全部 25 个音频解码/时长，以及仓库子路径与本地直接打开的资源路径。尚未部署到你的 GitHub 账号；上线网址以仓库 Pages 页面显示为准。

若网站 404，检查 Pages 选中的分支、`/ (root)` 和仓库根目录的 `index.html`。若页面有样式但不能试听，检查 `assets/audio` 是否完整上传，以及文件名大小写是否保持不变。
