+++
date = '2026-10-26T22:05:00+08:00'
draft = true
title = '学习pstack'
+++


一切都源自那个恐怖的女人8月份做了 2011 次提交

[![image.png](https://i.postimg.cc/MZs2xw2q/image.png)](https://postimg.cc/NLrP7Wjz)


## Loops You Can Trust

[Loops You Can Trust](https://x.com/poteto/status/2069824386283319343)

拥有一个高质量的验证 skill 是保证 agent 产出最重要的 skill。

Lauren 入职第二天，经理就请Lauren帮忙解决 Agents Window（内部代号 glass）的性能问题，而且几天后就要发布。Lauren不熟悉代码库，Electron 应用内存高到经常在录完 trace 前就撞上 4GB 上限崩溃；问 agents 也只得到自信但可疑的回答。

Lauren的思路来自 2025 年 4 月做过的 React Compiler 玩具 MCP：agents 应该拿到人类工程师写好代码时用的同样信号（编译器诊断、lint、静态分析等）。于是Lauren很快写了 /control-glass skill（技能）：让 agent 启动开启 Chrome DevTools Protocol 的开发版，能点击和输入、查看无障碍树、截图录屏、限速 CPU 和网络、抓 CPU profile 和 heap snapshot（堆快照）。这样 agent 能自己复现、分析、修改、再量一次，之后的每个 agent 都自动有这套能力。

loop 之前，先教会 agents 验证自己的工作是否正确、是否达标。否则跑 loop 只会让活全推给本就分身乏术的人, 垃圾产出越积越多（原文叫 slop）。验证就是软件开发的 limiting step。

Cursor 开发版共用端口、进程和本地数据，两个 agent 会互相干扰，作者于是加了 worktree（工作树）支持：每个 agent 有独立的 checkout、构建、端口、浏览器状态和 Cursor 实例。加快了速度，但并不能保证质量。

Lauren把每个反复出现的失败模式变成一条 skill，比如先复现再改代码、提出多个假设逐一排除、先写失败的测试、检查 diff 之外的影响范围（blast radius）、比较方案再选、录下改动前后的真实产品。形成了个人 skills 集 -- pstack。pstack 还带能让 agent 连跑几小时的 playbook（作业手册），并留下决策日志、测试等可事后审查的产物。作者认为，**观察 agents 在哪卡住、搭系统帮它们成功，已是工程师工作的重要部分，形式可能是 skills 和规则，也可能是重构和选对架构，让 agents 默认就做对**。

agents 写了大部分代码后维护成了噩梦，而且 agents 仍要作者亲手启动：发现问题、开 agent、跑 pstack、盯结果，熵增比收拾得快。Cursor 3 月推出 Automations（自动化）后，作者把 pstack 和 /control-glass 串成自动运行的 loop，先用在软件维护上：从 Slack 里拿到问题反馈，然后分诊、复现、修复、验证。

- Triage（分诊）：下载截图和视频附件并分析，从含糊的描述推断涉及哪个功能，查反馈人版本、查重、翻代码和近期历史；确认是 bug 就建工单，并给复现环节留交接说明。
- Repro（复现）：在云端起真实 Cursor 构建，沿反馈人的路径操作，必须两次精确复现故障状态并截图录屏，证据交给修复环节。
- 人工窗口：修复前帖子保持开放，人可以纠正或否决复现结果。
- Fix（修复）：无人反对且根因清楚时，接手已构建好的环境和所有证据，能写测试就写，做能证明的最小修复，跑前后对比，开 draft PR（草稿 PR）。

截图和视频让人很快判断 agent 修的是不是对的问题、修得对不对，审 PR 时就能专心看代码本身。自动化还能验证进行中的工作：发现已有未合入的 PR 时，跑前后对比，确认它是否真修了反馈的 bug。

如何从 agent 的 loop 从吸取经验，然后进行优化？

- 人在 loop 里时细读对话，尤其是 thinking blocks（思考过程），从中找失败模式，也借此判断某一步需不需要 agent：脚本能确定性完成的就用脚本，agent 只负责模糊部分（选假设、解读证据、判断何时要人拍板）。
- 长任务用 pstack 的 /show-me-your-work：只追加的决策日志，每行记决策、理由、证据和结果；结束时与对话记录核对，并派另一模型家族的 agent 审查流程。人只看关键决策即可。
- Build the Lever（造杠杆）：以跨语言或跨框架迁移为例，靠几百个 agent 硬推，就得验证几百份改动。正确做法是先手做第一份摸清套路，再让 agent 写 codemod（代码改写脚本）处理机械部分，外加一个能证明输出正确的检查器，审查者可以读、可以重跑。看到 agent 反复手工做某件事，就让它写出“它希望有”的工具或 skill，以后每次运行都能继承。

起步：安装 pstack，跑 /create-verification-skill。并建议把 Dr Eggbot（帮你搭高质量 bots 的机器人；随 pstack 附带）加进名单——它能教 coding bots 怎么用 pstack，也能用同样严谨度做非 coding bots。

可让 Dr Eggbot 先建一个 engineer bot，再让它跑 /create-verification-skill，并设每日例行跑 /maintain-verification-skill。

作者把 Grok Bot 与 Cursor 上在用的验证技能蒸馏成这个 meta-skill（元技能）：教你的 agent 为你自己的应用写出高质量验证技能。

技术栈选择在这里很关键。Electron 或 Web 可用 JS 生态的调试能力，例如 Chrome DevTools Protocol（CDP，Chrome 开发者工具协议），复用浏览器开发者工具那套；iOS 则可用模拟器。理想状态是能与应用交互、调试、抓 perf traces（性能追踪），以及你手工开发时会用的其他工具。若没有丰富 runtime（运行时），就让 agent 自造工具（如 lldb，或开发环境里的 sidecar 包），或先用已有手段。

作者甚至会认真建议：为拿下 agentic verification（智能体验证）的不公平优势，自己造调试工具，或干脆换更易控的技术栈。栈越难调试、越难操控，agents 就越难用出生产力；反过来，让 agents 能自验，组织里所有人都能贡献并确认改动真的有效。

pstack 有一条原则叫 Build the Lever（造杠杆）：给 skill 时优先给工具，而不只给 markdown。落到验证技能上，就是做一个小 CLI，把交互与调试收成 agent 友好的小工具——少写一次性脚本、少烧 token，也更可复现、可测。

原文举了一个面向 Electron 应用的假想 CLI，命令大致分 Inspection（探查）、Navigation（导航）、Interaction（交互）、Performance（性能）、Streaming（流式）、Health & cleanup（健康与清理）等组。有了它，所有 agents 都能快速导航与调试。同时要想清开发体验：种子数据库、鉴权与测试用户、打测试/预发 API、一致地装起开发环境——这些本就是人手写代码时要面对的，现在成了 agents 做开发的主工具，要维护好、测好。

其他示例命令包括 doctor、new-session、send、press、snapshot、screenshot、wait-settle、feature-flag（功能开关）相关等。基础就绪后，agents 在应用里导航与调试应明显顺很多。作者建议先把 CLI 做稳、少错，再做更高级事；也可让 agent 按 agent 友好 CLI 来设计。关键属性：易组合（呼应 John Ousterhout 的 deep modules / 深模块）、破坏性命令要有 --dry-run、子命令做渐进披露、错误信息要告诉 agent 下一步该做什么、丰富的 --help、机器可读输出（如 JSON）。

总结一下就还是重复的内容要提供脚本给 agent，不要让 agent 重复造轮子。

应用变复杂后，agents 需要更多引导。Feature Map（功能地图）：可检索的地图，列出应用有哪些功能、做什么、从用户视角怎么走到。

## 监督比你聪明的人

我们几乎不用先懂代码也能靠 prompt（提示）改系统。但把代码质量与用户体验压住仍然很难，尤其当我们还不是 domain expert（领域专家）、不知道该盯什么、该问什么的时候。

即便前沿模型已经很强，作者仍常看到两种失败模式：

1. 意图没说清：under/poorly specified（欠指定 / 指定很差），模型吃不准你要什么。
2. 上下文不够：不知道「正确地做」需要哪些背景。

两者其实连着：用好 agents，关键是把 high quality context（高质量上下文）灌进 context window（上下文窗口）。

前沿模型写代码已经很强。对旧模型，作者往往会写得很具体、近乎 micromanage（微观管理）；最新模型往往比你我写得更好。因此要在「说清目标」与「留给它你想不到的解法」之间找平衡。

这就是「监督比你聪明的人」的艺术：对象比你强、代码库未必是你写的、整机 mental model 人类已经塞不进脑子。

作者爱用的一招是 indirect prompt（间接提示）：不直接告诉 agent「我要什么」，而是把它从 agent 嘴里用它自己的话掏出来。

例如 Slack 里有人报问题，作者常先让 agent 读线程，用自己的话复述底层问题，再动手：

```
/poteto-mode read this slack thread. restate in your own words and in plain english what you think the underlying issue is
/poteto-mode 读一下这个 Slack 讨论串。用你自己的话、用大白话重新说一下，你觉得根本问题是什么。
```

这能办成三件事：
1. 迫使 agent 把嘈杂对话压成结构化问题陈述。
2. 立刻抓误解——若它抓住红鲱鱼（red herring），可在写任何代码前纠正。
3. 你没有先把自己的假设与假说塞进去，避免带偏，也避免限制它本来能达到的解法空间。

Building up a mental model（搭起心智模型）

让 agent「用你能懂的方式复述」——这是和比自己聪明的人协作的核心。由此催生了 /teach：帮 agent 用直观方式向你解释。作者需要确认「它在做的事对我有意义」时就会用。

底层上，/teach 会调到 /how 与 /why：
- /how：追 runtime mechanics（运行时机制）。会先评估子系统复杂度；若跨多目录或多服务，会在快、省的模型（如 Grok）上并行拉起 explorer agents（探索智能体）。例：`/how is virtualization implemented?`
- /why：追动机与意图。代码告诉你「发生了什么」，很少告诉你「当初为什么这么写」。跑 `/why` 时，pstack 会并行查多路历史证据：Git 历史与 PR review 评论、Linear tickets、Notion design docs、Slack 对话、Datadog monitors、Sentry errors、code lineage（代码谱系）、analytics warehouse events（分析仓事件）等。例：`/why are we still stuck an old version of node.js?`

想让 agent 复述以便自己理解并信任其工作时，作者会用 /teach，例如：

```
/teach me why you implemented it this way and not <other way>. what were the tradeoffs you made and why?
教教我，为什么你要这样实现，而不是用<另一种方式>？你做了哪些取舍，为什么？
```

作者认为：多数带 plan mode（计划模式）的 harness，往往 over-specify implementation details（过度指定实现细节），却 under-specify everything else（其他一概欠指定）。因此 pstack README 里有一句半开玩笑的话：「I don't believe in planning」。

对共享代码、给别人用的 packages（包）这类工作，作者信 readme driven development（README 驱动开发）：先写 README，逼自己戴上 developer experience（开发者体验）帽子——先向假想用户描述 API，再倒推实现与架构。

在做 Dune 的时候，先写 README， 但是第一版很难读：教程、how-to、架构说明、API 参考全搅在一篇里，还带着典型 AI slop（AI 注水腔）与 mannered prose（做作文风）。于是先「磨刀」——做出 `/technical-writing`。

`/technical-writing` 用 [Diátaxis](https://diataxis.fr/) 框架把文档拆成四种模式：

1. Tutorial（教程）：边做边学；带新手走完一系列步骤，做出看得见的东西。
2. How-to guide（操作指南）：给有经验用户解决具体真实问题的步骤。
3. Reference（参考）：干、全、权威的技术描述——机械结构、API、配置开关。
4. Explanation（解释）：澄清背景、设计取舍与 tradeoffs（权衡）的高层面讨论。

`/technical-writing` 用 [Diátaxis](https://diataxis.fr/) 框架把文档拆成四种模式：

1. **Tutorial（教程）**：边做边学；带新手走完一系列步骤，做出看得见的东西。
2. **How-to guide（操作指南）**：给有经验用户解决具体真实问题的步骤。
3. **Reference（参考）**：干、全、权威的技术描述——机械结构、API、配置开关。
4. **Explanation（解释）**：澄清背景、设计取舍与 tradeoffs（权衡）的高层面讨论。

它还会用到 [`/unslop`](https://github.com/cursor/plugins/blob/main/pstack/skills/unslop/SKILL.md)，让产出可读。

这样写「计划」有双重好处：给 agents 一个可自检的具体靶子；人也更容易看懂它到底要造什么。

设计阶段，许多 pstack skills 会**复利叠加**。作者给的组合示例：

```
(1) /recall my work fixing virtualization bugs and perf issues from the past 7 days. use /how and /why to understand how our current virtualization implementation works.

(2) then use /poteto-mode planning and /technical-writing to come up with a new virtualization engine that categorically eliminates flickering and jittering. let's start by writing a tutorial on how i would use this new package to virtualize a React app

(3) after you write the plan, /teach me and prove to me why this new approach is superior to our current engine

（1）/recall 我过去7天修复虚拟化bug和性能问题的工作。用 /how 和 /why 来理解我们当前的虚拟化实现是怎么运作的。

（2）然后用 /poteto-mode 规划和 /technical-writing 来设计一个新的虚拟化引擎，从根本上消除闪烁和抖动。咱们先写一篇教程，讲讲我会怎么用这个新包来虚拟化一个React应用。

（3）你写完方案之后，/teach 我并向我证明为什么这个新方案比我们现在的引擎更优越。
```

技法本质是把有趣、丰富的上下文**掏出来**，让 agents 能和你一样看问题——而不是只看见一小片：

1. 第一段召回过去与当前、虚拟化在应用里怎么实现的上下文。
2. 第二段引导 agent 用这些上下文（含它修过的 bug）设计能**彻底消除**那些问题的新方案。
3. 最后让它**证明**新包更优——这里就轮到 Part 1 讲的高质量 verification skills 出场了。

### Measure a hundred times, cut once（量一百次，再切一刀）

规划时作者最常见的两个错误：

1. **接受 agent 的第一版设计。**
2. **在没有经验证据时把计划炖过头（overcook）。**

人写代码的时代，常靠 design docs（设计文档）协作：高层架构、备选方案、tradeoffs、不寻常的实现注记；往往多轮迭代才定稿。有了 agents，可以跳过设计文档的仪式，但常见失误变成：agent 一交活就照单全收。pstack 则把「measure twice, cut once」推到极限——用**并行 agents**。

载体是 [prototyping playbook（原型 playbook）](https://github.com/cursor/plugins/blob/main/pstack/skills/poteto-mode/playbooks/prototype.md)。

在 pstack 里，**playbooks 不是 skills**，而是 `/poteto-mode` 里的参考文件；按任务类型**条件加载**（省 token）。截至 0.15.0 共有 23 份 playbook，每份对应作者做一类任务时的工作流。与 skills 不同，playbooks 会由 agent 作为 `/poteto-mode` 的一部分自动使用。例如：

```
/poteto-mode prototype a few options for the new dropdown menu
/poteto-mode fix this bug
/poteto-mode eval this skill change
```

Prototyping 是作者最爱的 playbook 之一：对同一目标给多次尝试，并帮 agent 推理最优选项。不只用于视觉原型，也用于功能、修 bug 等不同解法的原型：

```
/poteto-mode prototype a few options for <feature request>. use /control-app* and take videos/screenshots for me to review and choose from
```

\* `/control-app` 即 Part 1 里一起搭的 verification skill。

做视觉改动时，agent 会在应用或 scratch 目录里做可丢弃的 sketches（草图）；测 UI 交互时，会把两三种变体放在简单 switcher（切换器）后面，再用 `/control-app` 驱动交互、拍各变体截图，并量真实 timing / layout。

**Prototyping 就是用代码做规划。** 它给 agents 探索问题空间的自由，也给它们惊喜你一回的机会；原型让 agents 用经验证据回答自己的问题，而不必干等你输入。

