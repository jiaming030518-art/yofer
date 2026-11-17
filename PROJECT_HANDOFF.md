# YOFER 网站：新对话交接

整理日期：2026-11-17（UTC+8）。

本次交接核对了当前本地源码、文件目录、Git 状态及部署工作流；没有修改网页，也没有重新进行全站浏览器验收。历史记录与待确认事项在下文区分。后续以最新用户指令和磁盘文件为准。

## 1. 接手时先做什么

1. 阅读根目录 AGENTS.md，了解长期约定。
2. 检查 git status、git log、当前分支和暂存区，再阅读 outputs/index.html 中与本次任务有关的部分。
3. 当前没有已经批准但必须立刻执行的新 UI 任务。先等用户说明本轮需求；下面“可选后续”不等于施工指令。
4. 用户如要求讨论或先确认方案，先给具体布局或方案；获得确认后再改。

## 2. 正式项目与运行方式

- 正式项目：D:/DevTools/Git/YOFER网页。
- 入口：D:/DevTools/Git/YOFER网页/outputs/index.html。
- 素材：D:/DevTools/Git/YOFER网页/outputs/assets/。
- 项目是静态网站，HTML、CSS、数据及客户端交互主要写在同一个 index.html 内。当前没有 package.json 或必须运行的 npm 构建流程。
- 页面使用 Manrope、DM Mono 外部字体；无网络时字体表现可能不同。
- 可以直接在浏览器打开入口文件；做交互和移动端验收时建议启动 HTTP 预览。

在项目根目录执行：

```powershell
python -m http.server 4173 --bind 127.0.0.1 --directory outputs
```

然后打开 http://127.0.0.1:4173/#home 。

本机已核对 Python 路径为 D:/DevTools/Python312/python.exe，Node 为 D:/DevTools/NodeJS/node.exe。若命令不在 PATH，可使用完整路径。本次没有确认端口 4173 上是否仍有服务；如被占用，先确认现有服务是否指向本项目，再选择复用或更换端口。

旧目录 C:/Users/Administrator/Documents/Codex/2026-08-13/kai 不是正式网站源码目录，但保存了上一轮 QA 脚本和报告。不要在那里新建另一套网站。

## 3. 页面、数据与代码定位

### 主路由

| URL 片段 | 内容 | DOM |
| --- | --- | --- |
| #home | 首页 | homeView |
| #catalog | 产品目录、品牌筛选 | catalogView |
| #model=车型slug | 共用车型详情模板 | modelView |
| #about | About Me | aboutView |
| #company | Company 与市场地图 | companyView |
| #contact | 联系方式、二维码、个人信息 | contactView |
| #wechatQR | 展示 Contact 页并定位微信二维码 | wechatQR |

这些由 showRoute() 及 bindRoute() 处理，是客户端 hash 路由，不是独立 HTML 文件。

### 当前已有详情入口（共 10 个）

| 车型 | slug | 当前内容范围 |
| --- | --- | --- |
| Tesla Model Y Juniper 2025+ | tesla-model-y-juniper | Dragon Flame PRO、图库、套件总览、零件、参数、对比 |
| Tesla Model X 2016–2020 | tesla-model-x-2016-2020 | 图库、零件、参数、对比 |
| Toyota 9th Camry 2025+ | toyota-9th-camry-2025 | 安装后主图、零件、参数、对比；不显示图库 |
| Tesla Model 3 2023–2026 | tesla-model-3-2023-2026 | 对比 |
| Zeekr 009 | zeekr-009 | 对比 |
| Zeekr 001 | zeekr-001 | 对比 |
| Zeekr 7X | zeekr-7x | 对比 |
| Zeekr 007 GT | zeekr-007-gt | 对比 |
| Zeekr X | zeekr-x | 对比 |
| Xiaomi YU7 | xiaomi-yu7 | 对比 |

catalog 还保留其他待补素材车型。当前品牌为 Tesla、Honda、Toyota、Zeekr、Li Auto、Xiaomi；不要根据早期设计设想自行添加尚未落实的 BMW、Audi、XPeng 等品牌或产品数据。

### 修改入口索引

| 区域 | 搜索标记 / 函数 | 说明 |
| --- | --- | --- |
| 配色、字号、间距 | :root | 当前蓝灰 + 黑白灰体系，完整色表见 AGENTS.md |
| 首页主视觉 | .hero、.hero-copy、.hero-card、renderHero() | 保留矢量 Logo 和图片融入背景；桌面左右构图，窄屏分行 |
| 首页轮播名单 | heroSlides() | 排除 comparisonOnly 及 Camry，目前为 Model Y Juniper、Model X |
| 车型与素材 | img、productMedia、catalog | 名称、年份、主图、图库、零件与样式数据 |
| 通用对比素材 | comparisonMedia()、comparisonModel() | 常规采用 assets/slug-comparison-before.webp / after.webp |
| 目录 | catalogCard()、renderCatalogPage() | 默认已就绪车型卡片、品牌筛选及独立 Coming soon 分组 |
| 首页车型区 | renderModels()、platformBrand() | 推荐车型图片与品牌索引 |
| 详情模板 | renderModel() | 统一标题字段；按数据可用性生成图库、总览、零件、参数、对比 |
| 图库交互 | initModelGalleries() | 固定舞台、前后箭头、圆点/短线及键盘切换 |
| 对比交互 | initCompare()、initAllCompares() | 固定全幅图片，用 clip-path 揭示；Pointer Events 支持鼠标和触摸 |
| 移动菜单 | renderMobileNav()、closeMobileNav()、menuToggle | 展开、关闭、Escape、点击外部关闭、语言按钮 |
| 市场地图 | marketData、selectMarket()、mountMarketExperience() | 关联国家高亮、推荐车型与跳转 |
| 个人信息 | renderChenjiamingPortraits() | 共用个人照片及联系页个人信息 |

建议使用这些标记查找，避免依赖会随编辑变化的固定行号。

## 4. 已确认的设计方向

- YOFER / ORIGINAL AUTOMOTIVE DESIGN STUDIO：高端、克制、现代、设计品牌、国际商务感。
- 叙事围绕品牌、原创设计、车辆及案例、设计到交付能力、直接联系人展开。
- 基于现有模块优化，不增加一堆新模块，不重写项目。
- 矩形目录卡片配品牌筛选；未来最终推荐车型名单由用户补齐车型后指定，不擅自决定长期“热门”名单。
- 首页大字与真实车图是主角，车图融入浅白背景；文字不能挡车，车身只适度靠右。
- 品牌色、图片规则、主动移除的内容、Git 保存习惯详见 AGENTS.md。

当前首页内容顺序为 Hero → Design statement → Featured / 品牌索引 → Case 001 → Capability / 全球市场地图 → Personal contact → Contact 色带。它是当前实现，不表示允许新 AI 自行重新排列。

## 5. 重要素材与联系信息

- Model Y 主图：assets/model-y-juniper-hero.jpg。
- Model Y 竖版套件排布：assets/model-y-juniper-kit-overview.jpg，必须完整 contain。
- Model Y 对比：assets/model-y-before.webp / assets/model-y-after.jpg。
- Model X、Camry 的素材映射见 productMedia；主图和对比素材不一定使用同一个文件。
- Camry 当前目录 / 详情主图为 assets/toyota-9th-camry-2025-comparison-after.webp；实际对比优先采用 style 上的 comparisonMedia 映射。不要误换回安装前图片。
- Camry 的旧图库资源和 productMedia.gallery 仍在，但 catalog 没有接入该图库，这是此前用户要求的显示状态。
- Zeekr 007 GT 对比使用 assets/zeekr-007-gt-before.webp / after.webp，是通用命名之外的现有例外。
- 其他比较车型通常使用 assets/slug-comparison-before.webp / after.webp；新素材接入前核对车辆、视角和安装前后关系。
- 人像 assets/chenjiaming.jpg；二维码 assets/whatsapp-qr.jpg 与 assets/wechat-qr.jpg。
- 地图 assets/world.svg、assets/market-highlights/*.svg；授权说明 assets/world-map-LICENSE.md 必须保留。

当前公开业务联系资料（来自现有网站；不由本交接授权修改）：

| 字段 | 值 |
| --- | --- |
| 姓名 | Chenjiaming |
| 首页职位 | International Trade Sales Manager |
| WhatsApp / 电话 | +86 130 5935 2142 |
| WhatsApp 地址 | https://wa.me/8613059352142 |
| Email | jiaming030518@gmail.com |
| WeChat | Ccc_362573 |

网站其他位置也有 Sales Manager at YOFER 的现有表述，后续若统一头衔，先按用户要求核对，不扩大本次任务。

## 6. 最近一次网站修改及历史验收

最近网站提交：7b3710b，2026-11-06，Refine YOFER typography, catalog spacing, and responsive layout。

该轮完成了：

- 调轻标题字重、提高辅助文字可读性，整理首页文字和车辆的比例及平板构图。
- 目录 / 首页车型卡片增加留白，统一文字与图片边缘；手机筛选可换行。
- 主图与图库优先 contain；零件图 4:3，竖版套件总览完整展示。
- 保留轮播，并增加暂停 / 播放、键盘焦点保留及减少动态效果设置支持。
- 优化菜单关闭、联系二维码定位、手机布局及 DM 遮挡问题。
- 地图高亮从旧荧光绿改为现有蓝灰色；提升黑色案例区和联系区的文字对比度。

2026-11-06 的历史检查记录：

- 42 个路由 / 尺寸组合未发现横向溢出、图片加载失败或脚本报错。
- 260 项功能检查通过，覆盖 10 个详情入口、品牌筛选、图库键盘、鼠标与模拟触摸对比、菜单、微信定位与刷新、地图及轮播。
- 尺寸包含 1440×1000、1024×900、820×1180、768×1024、390×844、430×932。
- 浏览器为系统 Chrome 的 Playwright 无头检查；触摸由 CDP 模拟，不是真机测试。
- Taste 通用一致性 / 布局检查不是全通过：其部分模板规则与保留的轮播、真实联系方式、hash 路由、分栏和模块结构不兼容；真实对比度及遮挡问题曾据截图修正。不要把“260 项功能通过”描述成技能全部规则满分。

历史 QA 位于旧工作目录：

- C:/Users/Administrator/Documents/Codex/2026-08-13/kai/work/yofer_taste_qa.py
- C:/Users/Administrator/Documents/Codex/2026-08-13/kai/work/yofer_taste_regression.py
- C:/Users/Administrator/Documents/Codex/2026-08-13/kai/work/yofer_taste_finishing.py
- 同目录 taste-after/report.json、taste-final/regression.json、taste-final/finishing.json、taste-final/QA-NOTES.md。

脚本没有纳入本仓库，可能包含旧路径；使用前先读脚本，确认服务、依赖与输出目录。后续新的 QA 工件放到本项目被忽略的 work/，不要放 outputs/。本次交接没有把历史脚本迁入仓库。

## 7. 已知限制与可选后续

以下仅是记录，未批准自动处理：

- 图片性能仍可优化：存在缺少原始宽高属性、非首屏图未懒加载、手机仍下载大图等情况，尚未建立完整响应式小图方案。
- 一些 Before / After 原片的拍摄角度、车体比例、位置不完全相同；固定画框与拖动稳定不代表车体几何精确对齐。Camry 曾反复讨论，用户拒绝过某种处理并要求先暂停；未来需重新明确其可接受方案。
- 用户讨论过方图，但没有执行全站方图改造；当前车辆区域仍为 3:2。
- 用户还会继续提供车型图片，并在车型补齐后指定目录默认推荐车型；不凭空补素材或销量排名。
- 语言切换是现有功能，不能据此承诺全部静态英文内容已经完整翻译。
- 未进行本次线上实时状态核验；历史部署故障不能当作今天仍存在或已经排除的结论。

## 8. Git 与发布状态

本次文档整理前核对结果：

- 分支 main，HEAD 为 7b3710b；已跟踪文件和暂存区均无未提交差异。
- 本地 origin/main 记录为 cdd15f7，显示 ahead 2；本次没有 fetch，因此这不是远程实时状态证明。
- 远程仓库：https://github.com/jiaming030518-art/yofer.git。
- 已配置的公开地址：https://jiaming030518-art.github.io/yofer/。
- 早期仓库地址中的单个短横线属于旧记录，后续以当前 origin 为准。
- 本次交接文档将另存为一个新提交；接手后用 git log -1 核对最新提交号，不把上面的“整理前 HEAD”当作永远的当前版本。

最近历史：

| 提交 | 内容 |
| --- | --- |
| 7b3710b | Taste 视觉、目录间距和响应式优化 |
| adcbad6 | 手机菜单图标居中、移除边框 |
| cdd15f7 | 移除首页公司统计条 |
| ba095d4 | 移除目录图片状态角标 |
| 7f7929a | 强调色改为蓝灰 |
| ad0a13c | 中性色改为统一灰阶 |

当前仍有未跟踪项目：.claude/、backups/、outputs/ 中多张 QA PNG，以及 assets/hero-model-x-cut.webp、assets/hero-model-y-juniper-cut.webp。它们不属于本次交接改动，不删除、不批量暂存。_unused-assets-backup/、assets/_orig-backup/、work/ 已在 .gitignore 中排除；不要因此认定其中素材可以删除。

发布配置位于 .github/workflows/pages.yml：

- main 上的 push 或手动 workflow_dispatch 可触发。
- 工作流使用 configure-pages、upload-pages-artifact、deploy-pages；上传目录为 outputs。
- 本次只整理文档并做本地提交，不推送或触发部署。
- 以后用户明确要求上线时，先检查当前差异和待推送提交；推送后检查工作流，再实际验证线上页面，分别报告三步结果。网络失败就报告具体失败和已保留的本地版本，不反复无进展地让用户输入“继续推送”。

## 9. 可复制到新对话的开场

> 继续维护 D:/DevTools/Git/YOFER网页 的 YOFER 网站。请先阅读根目录 AGENTS.md 和 PROJECT_HANDOFF.md，检查当前 Git 状态及相关源码。沿用现有结构、功能、蓝灰配色和真实素材；不要自动执行交接文件里的可选事项。先等我提出本轮具体需求。每轮实际修改完成后检查并保存本地 Git 提交，未经本轮明确要求不要推送或部署。
