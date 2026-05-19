# AI PM 实习作品集路线图

**前置阅读**:`AI_PM_DIAGNOSIS.md`(诊断报告)。这份路线图是诊断结论的"反面"——每条短板都对应一条补救项。

**排序逻辑**:**ROI = 对实习简历/面试的边际收益 ÷ 投入工作量**。优先做 P0,这两项不补,投出去的简历回报率会被压制。

**适用阶段假设**:大三在校,每周可投入作品的时间约 8–15 小时;目标在 1–2 个月内把这份作品打磨到"敢主动甩链接给招聘方看"的程度。

---

## P0 —— 必补,补完再投简历

### P0-1 · AI 匹配 + 排序模块的产品规格

> ✅ **2026-05-16 已完成,定位为 v2 愿景(非 MVP)**:小程序 v1 尚未做起来,"正式接入大模型"延后。已交付 `AI_MATCHING_SPEC.md`(顶部显著标注 v2/非 MVP)+ PRD §6.3「匹配引擎口径说明」+ §8 v2 表后指引段 + **PRD §3.5「AI 辅助筛选」**(按用户同日第二次指令新增,标题带 `v2 · 本版不做` 徽章 + 开头加粗范围标记,确保不被误读为 MVP 已实现)。

**对应短板**:`AI_PM_DIAGNOSIS.md` §1.2 AI 含量、§1.5 第四个故事的空洞。

**为什么是 P0**:这是把"AI PM 候选人"四个字坐实的核心交付物。简历投递之后,招聘方第一关是关键词扫描("匹配 / 排序 / 推荐 / 模型 / 评估指标 / AB"),目前作品里这些词出现密度极低;面试官第一个问题大概率是"AI 在哪",目前最好的回答只能撑 1 分钟。

**交付物**:

1. **在 PRD 里新增 §3.5 设计决策**:"为什么 v3 引入 AI 辅助筛选(而不是替代中介)"
   - 用现有 §3 三段式格式(决策 / 取舍 / 实现位置)
   - 核心叙事:**人机协同**而非取代——AI 做 top-K 初筛(N×3 个候选 → N×1 候选池),中介方做最终 rank 决策 + 解释。这样 AI 失败成本可控、训练数据来源清晰、招聘方对 rank 的信任锚点不变。

2. **新建独立文档 `AI_MATCHING_SPEC.md`**:正经的 AI 模块产品规格,至少包含:
   - **介入点**:在主流程哪个消息节点之前/之后跑(建议:#15–#17 之间,中介方打开报名池时,AI 先把池子按相关性排序 + 给出 top-K 推荐)
   - **输入特征**:应聘方画像(学历、技能 tag、历史成单类别 / 评分 / 准时率)、招聘需求(类别、时段、地点、预算、必要技能)、上下文(报名时间、距截止时间、招聘方历史偏好)
   - **输出形态**:rank score(0–1) + 置信度 bucket(高/中/低) + 推荐理由(2–3 条可读 tag,如"历史同类兼职 4 单全好评")
   - **置信度处理 + 兜底策略**:高置信直接置顶 + 高亮;低置信沉到底部但保留;系统失败时降级为按报名时间排序
   - **冷启动方案**:从规则到模型的演进——v3.0 用规则打分(类别完全匹配 +50 / 时段重叠 +30 / 历史好评 +20),v3.1 在累积 1 万条筛选样本后切 LR/GBDT,v3.2 引入轻量向量召回。**这一段是面试 talking point 的金矿**——展现"会按数据成熟度选模型,不上来就 LLM"。
   - **评估指标**:中介方 top-3 接受率(AI 推的 top 3 里被中介保留几个)、招聘方对 AI-辅助 rank 的确认通过率、应聘方应答率(对比无 AI 基线)
   - **AB 实验设计**:对照组(纯人工)/ 实验组(AI 排序后人工复核),分流单元为招募令,样本量目标 200 单/组,主指标"中介筛选耗时"&"招聘方一次性确认率"
   - **可解释性 / 失败模式**:列出 3 类已知风险(冷启动期数据稀疏、长尾类别覆盖不足、招聘方个人偏好难学到),每类给"AI PM 怎么应对"的产品策略

3. **同步更新**:把 §6.2 表格里"匹配引擎"那两条(无匹配兼职 / 临近截止 24h)展开成 1 个小节,引用 `AI_MATCHING_SPEC.md`。

**投入估计**:8–12 小时(主要在 `AI_MATCHING_SPEC.md`)。

**面试转化**:讲完后能撑住至少 15 分钟深入追问;招聘方简历筛选阶段命中关键词显著提升。

---

### P0-2 · 指标体系 / 北极星指标

> ✅ **2026-05-17 已完成**:交付 `METRICS.md`——**指标设计/验证假设体系**(非数据报告,产品未上线、零实测数据,阈值全部标注"假设·待验证")。北极星 = **双确认成单率**(发布→已成单且 7 日内未取消的占比,抗刷量);§3.1–§3.4 每条决策配 1–2 个验证指标 + 假设阈值 + 数据来源;含护栏指标(应聘方 NPS / 客服工单率 / 中介 #17 耗时)与 §4「证伪逻辑」(什么结果出现就推翻该设计、下一步怎么改)。状态名/编号严格对齐 PRD §3/§5/§7;埋点字段级规格留待 P1-3。

**对应短板**:`AI_PM_DIAGNOSIS.md` §1.1 产品深度的"弱项 / 无数据思考"。

**为什么是 P0**:AI PM 面试和普通 PM 面试最大的差异——AI PM 必须**显式回答"哪个数能验证我的设计是对的"**。目前 §3.1–§3.4 四条设计决策都没有配套指标,等于面试时讲完故事接不下来。

**交付物**:

新建 `METRICS.md`,**关键不是面面俱到,而是每条设计决策配 1–2 个验证指标**:

| 设计决策 | 验证指标(主) | 健康阈值假设 | 数据来源 |
|---|---|---|---|
| §3.1 双重确认闭环 | 招聘方→应聘方双重确认通过率 | ≥ 75% | T2 + T3 埋点 |
| §3.1 双重确认闭环 | 拉群后 24h 内退群率 | ≤ 5%(对比 v2 应该显著下降) | 群聊事件 |
| §3.2 自动递补 | 平均成单时长(发布 → 已成单) | ≤ 24h | 状态迁移日志 |
| §3.2 自动递补 | 递补激活成功率(备选未耗尽前成单) | ≥ 60% | REPLACING 转移日志 |
| §3.3 统一取消 | 招聘方主动取消频次(P10–P90 分位) | < 10% | CANCELLED 事件 |
| §3.4 三段超时 T1 | T1 超时频次 / 中介总响应数 | ≤ 15% | T1 触发日志 |
| §3.4 三段超时 T2 | T2 超时后异议重筛成功率 | ≥ 50% | DISPUTED 转移日志 |
| §3.4 三段超时 T3 | T3 超时频次 / 应聘方应答总数 | ≤ 20% | T3 触发日志 |

最上层定义 **1 个北极星指标**:**"双确认成单率"**(从发布到已成单且 7 日内未取消的招募令占比)——这个指标同时反映了 §3.1 + §3.2 + §3.4 三条决策的健康状况,且不被刷量游戏(因为要求 7 日不取消)。

附:**反指标 / 护栏指标**:① 应聘方端 NPS 不能跌(防止系统替用户决策伤了体验);② 客服工单率不能涨(防止 §3.3 统一取消滥用);③ 中介方 #17 提交平均耗时不能涨(防止 §3.2 让中介负担过重)。

**投入估计**:4–6 小时。

**面试转化**:能稳住"你的北极星指标是什么 / 你怎么验证这个设计是对的"这两个高频问题。

---

## P1 —— 值得补,做完显著加分

### P1-1 · 竞品分析(2–3 个,聚焦差异点)

**对应短板**:`AI_PM_DIAGNOSIS.md` §1.4 商业 sense 的零提及。

**交付物**:新建 `COMPETITIVE_ANALYSIS.md`,选 **青团社 + 兼职猫**(直接竞品,双方撮合)+ **BOSS 直聘兼职板块**(单边导流模式)。**不要做大而全的功能矩阵对比**(那是浪费时间),只回答三个问题:

1. **撮合模式差异**:他们是"应聘方 ↔ 招聘方"双方撮合,本产品是"应聘方 ↔ 中介 ↔ 招聘方"四方协作——多出来的中介方解决了什么、增加了什么成本?
2. **筛选效率差异**:他们靠应聘方海投 + 招聘方海筛,本产品让中介方做集中筛选——单位招募令的筛选成本谁更低?信任度谁更高?
3. **AI 介入空间差异**:他们已经在 AI 推荐方向投入很久(成熟竞品的护城河),本产品的 AI 介入空间在哪里**反而更大**?(回答:中介方筛选场景天然产出"高质量人工标注样本",这是双方撮合产品拿不到的训练数据资产)

**投入估计**:6–8 小时(关键是把核心差异点想透,不是把竞品截图截完)。

**面试转化**:"你看了哪些竞品 / 你的产品的护城河是什么"——直接答得上。

---

### P1-2 · 补关键路径原型(应聘方端 #19a + 招聘方端 #18)

> ✅ **2026-05-18 已扩展**:在原 #19a/#18 两屏(`dual-confirm-prototype.html`)之外,按用户指令新建 `part_time_job_wechat_mini_program_prototype/full-flow-prototype.html`——**三端关键决策节点端到端一条流**(应聘:#11 推送→#14 报名→#19a 确认→成单/完工;招聘:#9 发布→#18 确认名单→T11 完工确认;中介:报名池→#17 提交 rank)。复用 cream 外壳/VC token byte-identical;#18/#19a 两屏原样复用。AI 仅在 3 个自然落点(应聘 #11 推送、招聘 #9 字段、中介 #17 报名池)显式标记 **「v2 · 本版不做」** + 页眉全局 AI 说明,绝不呈现成 MVP 已实现。

**对应短板**:`AI_PM_DIAGNOSIS.md` §1.3 设计完整度的"只覆盖 1/4 视角"。

**交付物**:

复用 `model_selection_business/cream-dossier.html` 已经搭好的 `IOSDevice` + iOS 外壳骨架(不必新搭手机壳),按相同的 V{A,B,C} 命名风格新增:

1. **应聘方 #19a 确认接受屏**:这是 v3 双重确认闭环的**点睛动作**(诊断报告 §1.5 第一卖点的最后一笔)。需要画清楚:24h 倒计时怎么呈现、"接受 / 放弃"两个按钮的视觉权重、放弃后会发生什么的提示文案、谁会被通知。
2. **招聘方 #18 确认名单屏**:中介方提交筛选结果后,招聘方看到的是什么——rank 列表、AI 推荐理由(承接 P0-1)、"全部接受 / 退回重选 / 单条剔除"的交互。这一屏同时还是 P0-1 AI 模块的**视觉落点**(让 AI 推荐理由可见)。

**注意**(技术约束,来自 `CLAUDE.md`):

- 不要用 JSX,跟现有原型一样用编译后的 `React.createElement`
- 末尾 IIFE 里挂 `InteractivePhone`,通过 `Object.assign(window, ...)` 暴露组件
- 全中文,不要英文 placeholder

**投入估计**:6–10 小时(因为骨架可以复用)。

**面试转化**:被问"你的核心动作的交互长什么样"时能直接打开看。

---

### P1-3 · 埋点设计(配合 P0-2)

**对应短板**:`AI_PM_DIAGNOSIS.md` §1.1 产品深度的"无数据思考"。

**交付物**:在 PRD 里新增 §9 或独立 `TRACKING_SPEC.md`,把 P0-2 `METRICS.md` 里每条指标反推到事件埋点:

- 事件名(规范:`{domain}_{action}`,如 `recruitment_state_changed`)
- 事件 schema(字段、类型、是否必填)
- 触发时机(对齐到 §4 主流程消息编号 + §5 状态迁移)
- 关联指标(回链到 `METRICS.md`)

**投入估计**:4–6 小时。

**面试转化**:体现"产品验证设计假设,要把假设拆到事件粒度"的工程化能力——这是 AI PM 与"只会画图的 PM"的关键区分。

---

## P2 —— 有余力再做

下列三项**不做也能投简历**,做了能让作品集更厚——但应该在 P0+P1 完成之后再考虑。

- **用户访谈记录**(2–3 份):学生兼职用户 + 中介从业者(可以是简化版,1 页/份)
- **商业模式画布**:Take rate / 单价假设 / GTM 渠道 / LTV-CAC 估算
- **v2 详细方案**:分级补偿 / 多维匹配引擎升级路径——可以呼应 P0-1 的"模型演进路径"

---

## 路线图总览

```
✅ 已完成  P0-1 AI 规格(重定位为 v2 愿景:AI_MATCHING_SPEC.md + PRD §6.3/§8)
✅ 已完成  P1-2 #19a/#18 关键路径原型(dual-confirm-prototype.html)
✅ 已完成  P0-2 METRICS.md(指标设计/验证假设体系,北极星=双确认成单率)
✅ 已扩展  P1-2 三端端到端流(part_time_job_wechat_mini_program_prototype/full-flow-prototype.html)
✅ 已完成  门面+诚实止血(README 重写 + 诊断/路线图去过期·修死链,2026-05-18)
          ─── P0 两项 + P1-2 完成:技术/产品面可投;但⚠ ───
⚠ 投前补  P1-1 竞品分析 + INTERVIEW_NOTES.md(未建)——商业 sense 仍 ☆,
          "竞品怎么看 / 北极星怎么讲"是面试翻车点,正式海投前补这两项再投
下一步     P1-1 竞品分析 → INTERVIEW_NOTES.md → P1-3 埋点设计 → P2 任选
```

**重要提醒**:每完成一项,**回头把 talking points 加到一份"面试自答稿"里**(可以是 `INTERVIEW_NOTES.md`)。作品本身打磨得再好,临场讲不出来也是浪费——把"会讲"和"做出来"同等对待。

---

## 更新记录

- **2026-05-19**:**三方底部 tabBar 图标 21→24px(两文件:`full-flow-prototype.html` + `full-flow-prototype_simple_version.html`)**——用户觉得图标偏小要"放大点";三选一(24/26/28px)用户拍板 **24px**(约 +14%,微调)。只改两处尺寸:`tabIcon` 的 `e('svg',{width:21,height:21,...})`→`width:24,height:24`(`viewBox '0 0 24 24'` 不变,设 24×24 后与 viewBox 1:1 最清晰、路径自动按比例);`TabBar` 图标容器 `div` `width:21,height:21`→`24,24`(其余样式不动)。两处必须同改(容器负责居中限位)。标签字号 9px / 栏 `padding:9px 0 22px` / `gap:4` / 颜色 token / 选中(实心咖啡)未选(描边灰)逻辑 / `tabIcon` 路径数据 / `IOSDevice` 全不碰(延续上轮评审"不镀金"纪律)。已核实仅这 2 文件含该 tabBar 且目标两行字节级一致(`feedback_chrome_polish_is_intentional` + `project_full_flow_two_files_lockstep`),同两处 Edit 逐字相同 = 4 次 Edit。自检 PASS:无 JSX、仅两个 `21→24`、未选/选中逻辑零变更、`casting-prototype-standalone.html`/`__om-edit-overrides`/PRD 编号未碰、零 AI;`git diff --stat` 仅 3 文件(2 原型 + 本路线图)、两原型本次改动 byte-identical。属外壳保真延续。**未改 P0/P1 排序,下一步仍为 P1-1 竞品分析**。
- **2026-05-19**:**三方底部 tabBar 选中态图标填实(两文件:`full-flow-prototype.html` + `full-flow-prototype_simple_version.html`)**——用户指定选中态要更明确。三选一(红色高亮+实心 / 亮色药丸底 / 保留咖啡色+图标填实)用户拍板 **保留咖啡色 + 图标填实**:选中仍 `t.coffee`、未选仍 `t.muted`、标签色不变,**唯一变化=选中态图标由描边→实心**;不引入社论红/药丸底/反白/动画(均被用户选项排除或属镀金)。`tabIcon(k,c)`→`tabIcon(k,c,filled)`:`filled` 假分支与原描边逐字一致(`filled &&` 前置短路,未选中态像素级零变更);真分支为 6 个图标**手画干净闭合实心字形**(公文包/日历/剪贴板=实心 rect + 纸色 `t.bg` 挖空腰线/表头/勾;喇叭锥体本就 `z` 闭合;房子重画为实心轮廓 `M12 3.6L20.5 11H18.5V20H5.5V11H3.5Z`+纸色门;人像实心头+胸)——避开"naive 给开放路径设 fill 会自动闭合显丑"(用户已接受该 caveat,但工程上规避之,零额外风险)。`TabBar` 调用 `tabIcon(tb.k,col,on)`,其余结构/间距/`background:t.bg`/`borderTop`/`TABS` 全不动。已核实仅这 2 文件含 `tabIcon`/`TABS` 且该区域字节级一致(`feedback_chrome_polish_is_intentional` + `project_full_flow_two_files_lockstep`),同两处 Edit 逐字相同。自检 PASS:无 JSX、未选中态零变更、颜色 token/主题/`IOSDevice`/`casting-prototype-standalone.html`/`__om-edit-overrides`/PRD 编号未碰、零 AI;`git diff --stat` 仅 3 文件(2 原型 + 本路线图)、两原型新代码 byte-identical。属外壳保真延续,微抬 `AI_PM_DIAGNOSIS.md` §1.3。**未改 P0/P1 排序,下一步仍为 P1-1 竞品分析**。
- **2026-05-19**:**招聘方招募令「详情 + 修改后重新提交审核」闭环(两文件:`full-flow-prototype.html` + `full-flow-prototype_simple_version.html`)**——用户指定:招聘方「我发布的招募令」列表点卡片进详情、可改内容并重新提交审核。先核实 simple_version 是 full-flow 的 `cp`,`EmployerRecruitList`/`window` 导出/`InteractivePhone` 路由三处区域**字节级一致**,故同四处 Edit 对两文件逐字相同(diff 交叉校验:新代码两文件 byte-identical,各 +141/-30 对称)。改动:① `L`→IIFE 级 `RLIST`(4 条补 `cat/time/place/payFull/tags/desc`;**`RLIST[0]` 与 #9 `EmployerPublish`/#19a `ApplicantConfirm`/#18 `EmployerRoster`/T11 `EmployerComplete` 同一笔 ZARA 招募令字段对齐**,防跨屏字段对不上),`EmployerRecruitList(props)` 卡片可点 + `详情/修改 ›`;② 新增 `EmployerRecruitDetail`(`view→edit→reviewing→view` 状态机,镜像 `EmployerPublish` draft→reviewing→published;复用既有 `Title/Field/Primary/Ghost/paper/page` helper 零新搭骨架)+ 新增 `DecisionNote`;③ 路由:`erecruit` 传 `onOpen` push `recruitdetail`、新增分支、`hideTab` 加 `recruitdetail`(钻取子页去 tabBar、‹ 返回走 pop 回列表)、`window` 导出补 `EmployerRecruitDetail`。**两处用户取舍(AskUserQuestion 拍板)**:(a) 弃「右下角悬浮 FAB」改**底部全宽 `✎ 修改招募令` Primary**——FAB 是 Material 语言、与 cream 卷宗违和且挂 `overflow:auto` 容器会随正文滚动遮挡描述,改后与 `EmployerPublish`/`ApplicantJobDetail` 全原型一致(连带消掉 `position:relative`/占位补丁);(b) 修改屏加一条 **§3 风格 `DecisionNote`**「已有 N 人报名时改报酬/时段/类别等实质字段对已报名者契约影响、本版简化、留待 v2、参 PRD §5」——把 CRUD 升级为面试可讲的边界感素材(本仓 §3/附录 B 一贯手法),工程量仅一段静态文案。编辑禁区/铁律自检 PASS:无 JSX(纯 `React.createElement`)、`window` 桥完整、全中文无英文 placeholder(受控 `value`)、`casting-prototype-standalone.html`/`IOSDevice` 5 文件/`__om-edit-overrides` 未碰、PRD 编号未涉、`git diff --stat` 仅这 2 个文件变更;**`DecisionNote` 不复用 `AiBanner`、零 AI 文案**(此为人工+平台重审闭环,遵 memory `feedback_ai_is_future_vision`),延续 `feedback_match_cream_design`。把招聘方一侧从「几个孤立单步」补成「列表→详情→修改→重审→生效」可钻取有状态回路,继续抬升 `AI_PM_DIAGNOSIS.md` §1.3「设计完整度」(静态原型 caveat 仍诚实保留)。**未改 P0/P1 排序,下一步仍为 P1-1 竞品分析**。
- **2026-05-19**:**新增 `full-flow-prototype_simple_version.html`(三方首页单屏对比版 + 横竖排切换)**——用户指定:`cp` `full-flow-prototype.html` 后,三方 `Section` 的 `steps` 各压成 1 条、指向该角色 TabBar 首页 view(应聘 `browse`/职位、招聘 `erecruit`/招聘、平台 `list`/首页,依 `TABS` 行 2938–2940 核实),右上角加 `position:fixed` cream 风格「竖排/横排」切换器(`React.useState` 控 `flexDirection`,默认竖排同原版),`Section` 外层 `width:'100%'→'auto'` 防横排溢出。复用既有组件零新写,**原 `full-flow-prototype.html` 未碰**;作品集展示用(一眼看全三端入口形态),非新功能。**未改 P0/P1 排序,下一步仍为 P1-1 竞品分析**。
- **2026-05-19**:**同步应聘方履约闭环(月历日程 + 到岗打卡屏)**——用户在桌面 `prototype.html` 上迭代了新版,要求把仓库 `full-flow-prototype.html` 缺的补上(范围=仅此一文件)。先 `git diff --no-index` 确认两份是同一原型的两个迭代、仓库版**无任何**桌面版缺失的独立编辑,故"补缺"边界清晰。补入 5 块:① **Tweaks 系统 script 块**(`window.__TWEAKS`/`setTweaks`/`useTweaks`/`TweaksPanel`,`TweaksPanel` 返回 `null` 不显示)——**必需**,新版 `ApplicantSchedule` 调 `window.useTweaks()`,缺它整屏报错;② **`ApplicantSchedule` 重写**:静态 3 条列表 → 完整月历(`buildGrid` 6×7 网格 / 月份切换 / 有班打点 / 选日看当天班次卡片 / 浮动月历下拉);③ **新增 `ApplicantCheckIn`**:到岗打卡屏,状态机 `未到岗→已到岗→已完工` + 履约时间线(结算口径对齐 PRD T11 7 天窗口);④ **`EntryRow` 新增 `accent` 高亮**,`ApplicantMine`/`EmployerMine` 两快捷入口改高亮并删其上 `ListHint('快捷入口')`;⑤ **路由接线**:`window` 导出加 `ApplicantCheckIn`、`schedule` 传 `onOpen`、新增 `checkin` 分支、`hideTab` 加 `checkin`、`App` 末尾渲染 `TweaksPanel`。**按用户明确决定不补**末尾 `<style id="__om-edit-overrides">`(宿主可视化编辑器残留:12 条互相覆盖、绑脆弱 nth-child 的 padding/font `!important`,CLAUDE.md 标"宿主生成的,不要动")。实现用确定性单步(取桌面版 1–3138 行恰停在 `</html>` 回写,而非在 392 字符长行近压缩 HTML 上做 5 处脆弱 Edit);两文件均 UTF-8/LF/无 BOM。双向 diff 自检通过:vs 桌面版仅差被省略的 override 块、vs HEAD 恰为上述 5 块(228 ins/30 del = 243−15),无 PRD 编号破坏、`casting-prototype-standalone.html` 未碰、无 JSX 泄漏、`window` 桥完整,延续 memory `feedback_match_cream_design`/`feedback_ai_is_future_vision`(履约屏 MVP 全人工无 AI)。把应聘方一侧从"能报名"延伸到"能履约打卡结算",继续抬升 `AI_PM_DIAGNOSIS.md` §1.3「设计完整度」(静态原型 caveat 仍诚实保留)。**未改 P0/P1 排序,下一步仍为 P1-1 竞品分析**。
- **2026-05-18**:**门面 + 诚实止血(零代码改动)**——按用户指令再次审查仓库找问题。三路只读审查:**编辑禁区 / AI 铁律全 PASS**(IOSDevice 5 文件字节级一致、`casting-prototype-standalone.html`/`__om-edit-overrides` 未碰、无 JSX 泄漏、`window` 桥完整、PRD 编号未破、SVG 双份同步;全仓 AI 提及均带 `v2·本版不做`,零违规——近期大量微信壳改动技术上干净)。真正问题在作品集层、且不在原诊断/路线图内,本轮修三处:① **重写 `README.md`**(原仅 2 行)为对外作品集落地页(谁/这是什么/AI 在哪 v2/怎么看,仅导向对外资产,不暴露诊断·路线图);② **`AI_PM_DIAGNOSIS.md` 去过期+修死链**(§1.3 设计完整度 ★★→★★★ 封顶并保留"静态原型/平台后台仍薄"诚实 caveat;§二总结表刷新;§三被动应对死链修正:`AI_MATCHING_SPEC.md`/`METRICS.md`/原型由"待写/待画"改为已交付,`COMPETITIVE_ANALYSIS.md` 诚实保留"待补";§1.5 空洞→半空洞、§一.1 弱项、§一.2 哑火按 P0 已完成据实更新);③ **本路线图总览**把无条件"可以开始投简历"改为带条件诚实表述、显式标 `INTERVIEW_NOTES.md` 未建。按用户明确决定:chrome 打磨有原因继续,**不**记为新短板、**不**建议冻结。**未改 P0/P1 排序,下一步仍为 P1-1 竞品分析**。
- **2026-05-18**:**full-flow 加按角色微信底部 tabBar + 6 个净新 tab 屏**(用户指定,深度=功能简洁版、范围=仅 `full-flow-prototype.html`)。把"像真微信小程序"主线补上最后一块标志结构:`IOSDevice` 加可选 `tabBar` 形参 + home 指示条前 gated 渲染(z55,5 文件字节级一致,其余 4 文件不传→无变化,延续 memory `feedback_match_cream_design` 外壳不变量);`App`/`Section`/`Flow`/`InteractivePhone` 透传 `role`;新增 `TabBar`+`tabIcon`+`TABS`(应聘 职位/日程/我的 · 招聘 招聘/我的 · 平台 待办/我的;tab key=view,点按 `setStack` 切屏,当前屏为 tab 根时 t.coffee 高亮);职位复用 `ApplicantBrowse`,其余 6 个净新屏(`ApplicantSchedule/ApplicantMine/EmployerRecruitList/EmployerMine/PlatformTodo/PlatformMine`)复用现有屏 IIFE helper(`Title/page/paper/Avatar/Field/Stats/OrderRow`),对齐 `AgentPool` 信息密度、假数据、全中文、**MVP 全人工无 AI**(无 AI 落点不加 AI 文案,贯彻 memory `feedback_ai_is_future_vision`);`PlatformTodo` 按截止时间升序。不重构既有 journey(PRD #编号承载性,CLAUDE.md 编辑禁区);`casting-prototype-standalone.html` / `__om-edit-overrides` 未碰。继续抬升 `AI_PM_DIAGNOSIS.md` §1.3「设计完整度」。**未改 P0/P1 排序,下一步仍为 P1-1 竞品分析**。 *导航微调(同日,用户精确指定):平台 `TABS` 最左加「首页」tab(复用 `list` 钻取链视图,新增房子 `tabIcon`),中介方三 tab = [首页|待办|我的];#19a/#20-22/#18/T11/#9 五个 journey 任务屏去掉 tabBar、改加左上「‹」返回——confirm/deal→「我的」(cmine)、roster/complete→「我的」(emine)、publish→「招聘」(erecruit);仅 `InteractivePhone` 一处 `backTo`/`noTab` 门控,其余 view 维持常驻 tabBar。随后用户追加:报名者列表/模特档案/报名池#17(`applicants`/`detail`/`pool`)也去 tabBar(消除其自带底部操作栏被遮挡),三者经 push 进入故返回仍走 `pop`(`hideTab = noTab || applicants || detail || pool`)。再追加:用户指出应聘方缺职位详情页(原 职位列表→直接报名表单),新增 `ApplicantJobDetail`(简洁版:岗位/招聘方/时段/地点/报酬/人数/要求标签/职位描述 + 报名按钮,复用现有 IIFE helper、假数据、全中文、无 AI),流程改 `browse→jobdetail→apply`,`jobdetail`(及随后 `apply` 报名表单)并入 `hideTab`(钻取子页无 tabBar、‹ 返回走 pop)。再追加:招聘方「招聘」页(`erecruit`)顶部最左侧(与返回键同位 `left:14,top:58`;erecruit 为 tab 根栈深 1 无返回键,不冲突)加「+」新建招募令——`IOSDevice` 加可选形参 `onAdd`(同 `onBack`/`tabBar` gated 模式,5 文件字节级一致,其余 4 文件不传→零变化),`Phone` 透传,仅 `v==='erecruit'` 传 `onAdd=()=>push({view:'publish'})`,复用既有 #9 EmployerPublish 与 `publish` 的 `noTab` 返回逻辑(无新建屏、不改 backTo/hideTab)。IOSDevice / VC 字节级复用组件未碰(IOSDevice 改动按字节级同步 5 文件)。再追加(用户:所有返回逻辑都要有进入入口):confirm/deal/roster/complete 四屏有返回键但无入口,在其返回目标页 ApplicantMine(→confirm/deal)/EmployerMine(→roster/complete)新增「快捷入口」行(新 `EntryRow` helper,InteractivePhone 给 cmine/emine 传 `onOpen=push`),导航成 我的→该屏→‹回我的 闭环;publish 入口已由招聘页「+」满足;纯增量,未改 backTo/hideTab/IOSDevice/字节级复用组件。*
- **2026-05-18**:**微信小程序导航带保真(状态栏主题色 + 胶囊固定带 + 正文滚动遮盖)**——承接同日「胶囊补丁」用户实测反馈:① 状态栏区原透明、露出设备根灰 `#F2F2F7`,改为**与页面主题同色的不透明带**;② 胶囊原 `top:56` 与正文重叠,改为小程序顶部固定导航带、正文整体下移到带下方;③ 占位条原在 `overflow:auto` 内随内容滚走、上滑时正文从透明状态栏透出,改为**不透明 z10 固定带遮盖滚动内容**。实现:`IOSDevice` 加 `bg` 形参(`bg || (dark?…)` 回退,字节级一致);状态栏绝对容器加 `height:100`(56 状态栏 + 44 微信导航区)+ 不透明主题背景;`WeChatCapsule` `top:56→62`(导航区内垂直居中);各 `Phone` 调用点传 `bg` 主题 hex(cream/dual-confirm/full-flow=`#f0e8db` VC_PAPER、vogue=`#fafaf8` VA、editorial=`#ffffff` VB)且占位 `56→100`。未增删 `IOSDevice` 根子节点 → `cream-dossier`/`dual-confirm` 的宿主 `__om-edit-overrides` nth-child 不受影响;`casting-prototype-standalone.html` 仍按编辑禁区排除。继续抬升 `AI_PM_DIAGNOSIS.md` §1.3「设计完整度」。微调:胶囊与状态栏图标视觉间距按用户指定从 ~14px 缩到 10px(`WeChatCapsule` `top:62→58`,图标底 ≈y48 → 间距 10px;5 文件字节级一处,导航带高/底距不变)。**返回键归位**(用户指定,全部 4 个有返回的文件):返回控件从滚动正文(full-flow 的 `BackBar` 条、cream/editorial/vogue 各屏内嵌「← 返 回/Back/BACK」)移入导航带左上,改为微信极简「‹」(细 chevron,丢 tag),`left:14` 镜像右上胶囊 `right:14`、同 `top:58`、`zIndex:40`;`IOSDevice` 加 `onBack` 形参经 `Phone`/`InteractivePhone` 透传,`stack.length>1`(非 root)才显示;原屏内按钮替换为空 `<span>`(保留 space-between 行、右侧 issueNo/卷宗标签不动);full-flow 的 `BackBar` 仅停用不删;dual-confirm 无导航不受影响,`IOSDevice` 仍字节级一致。继续抬升 §1.3「设计完整度」。**未改 P0/P1 排序,下一步仍为 P1-1 竞品分析**。
- **2026-05-18**:**微信小程序外壳保真补丁**——按用户指令,给 5 个可点击原型(`model_selection_business/cream-dossier.html`、`editorial-grid.html`、`vogue-fashion-magazine.html`、`dual-confirm-prototype.html`、`part_time_job_wechat_mini_program_prototype/full-flow-prototype.html`)的 `IOSDevice` 外壳统一新增 **`WeChatCapsule` 微信小程序胶囊按钮**(状态栏正下方右上角:`•••` + 分隔线 + 同心圆,磨砂半透明,随既有 `dark` 标志在黑/白间适配,`pointerEvents:'none'` 不挡原型交互;作为设备级 chrome 出现一次、浮于所有屏之上)。三处插入(组件定义 / `IOSDevice` 内渲染 / `Object.assign` 导出)在 5 文件中**字节级相同**(memory `feedback_match_cream_design`),`casting-prototype-standalone.html` 按 `CLAUDE.md` 编辑禁区排除。直接消除"这其实是通用 iOS 壳、不是真微信小程序"的可被当场戳穿的破绽,抬升 `AI_PM_DIAGNOSIS.md` §1.3「设计完整度」。**未改 P0/P1 排序,下一步仍为 P1-1 竞品分析**。
- **2026-05-18**:**扩展 P1-2**——按用户指令新建 `part_time_job_wechat_mini_program_prototype/full-flow-prototype.html`,把原型从"#18/#19a 两屏"扩为**应聘 / 招聘 / 中介三端关键决策节点的一条可点击流**(单文件、按角色分段、段内左→右):应聘方 #11 收到匹配推送→#14 报名→#19a 确认接受→成单/完工;招聘方 #9 发布招募令→#18 确认名单(复用)→T11 完工确认;中介方 报名池→#17 提交有序 rank。直接抬升 `AI_PM_DIAGNOSIS.md` §1.3「只覆盖 1/4 视角」短板。实现上 `cp` dual-confirm 后只改页眉 + 增 6 屏 + 重写装配,**iOS frame / VC token 字节级复用**(memory `feedback_match_cream_design`);#18/#19a 两屏原样保留。AI 严格按铁律(memory `feedback_ai_is_future_vision`):仅在应聘 #11 推送、招聘 #9 字段说明、中介 #17 报名池三个自然落点出现,每处带醒目「v2 · 本版不做」+ 页眉全局 AI 说明,MVP 全人工。**未改 P0/P1 排序,下一步仍为 P1-1 竞品分析**。
- **2026-05-17**:完成 **P0-2**——新建 `METRICS.md`,定位为**指标设计/验证假设体系**(非数据报告:产品未上线、零实测数据,所有阈值标注"假设·待验证",正面回应"没数据能不能做指标"——能,AI PM 考的是"会不会用数据思考"而非"有没有数据")。北极星 = **双确认成单率**(发布→已成单且 7 日内未取消,"7 日尾巴"抗刷量,同时覆盖 §3.1+§3.2+§3.4);§3.1–§3.4 逐条配验证指标 + 假设阈值 + 数据来源;含三条护栏指标与 §4「证伪逻辑」(给出"什么结果出现→推翻哪条假设→下一步怎么改",直接对应 `AI_PM_DIAGNOSIS.md` §1.1 短板与面试高频题)。同步更新本路线图总览(P0 两项 + P1-2 已完成,**可以开始投简历**);埋点字段级规格仍归 P1-3 `TRACKING_SPEC.md`。另:用户问"做微信小程序最合适的语言"——结论是**不切真·小程序栈**,继续用浏览器可开 React 原型(可甩链接、ROI 高),真小程序对 AI PM 边际增益≈0,详见会话计划。
- **2026-05-16**:P0-1 定位为 **v2 愿景(非 MVP)**——v1 小程序未起步,大模型接入延后。交付 `AI_MATCHING_SPEC.md`(顶标 v2/非 MVP)、PRD 新增 §6.3「匹配引擎口径说明」、§8 v2 表后指引段。**先**砍掉 §3.5,**后按用户同日第二次明确指令重新新增 PRD §3.5**「AI 辅助筛选」——三段式 callout,标题带 `v2 · 本版不做` 徽章 + 开头加粗范围标记,放在最被精读的 §3 以最大化 AI 含量可见性,同时确保不被误读为 MVP 已实现。同日提前完成 **P1-2**:新建 `model_selection_business/dual-confirm-prototype.html`,含 #19a 应聘方确认接受、#18 招聘方确认名单两屏(后者把 §3.4「系统不会替你默认通过」做进 UI;候选卡标注"v2 此处将由 AI 给理由",顺带可视化 AI 愿景但不假装 MVP 有 AI)。
