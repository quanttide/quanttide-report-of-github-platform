# 资产子模块初始化复盘：一次「遵循惯例」的考古

读这篇复盘的人只想知道三件事：学到了什么惯例、是怎么学到的、查的哪些资料有用哪些没用。2026-10-14，两次「遵循惯例创建仓库」的指令——feishu 的手册仓，github 的 lab / gallery / report 三仓——都没给仓库名、没给落点、没给模板，惯例散落在多个仓库的实物里。本复盘用一句话回答这三个问题：**惯例不在任何一份文档里，它以「同族仓库的既有实例」为载体——命名从本仓 `.gitmodules` 反推（分代码仓与文档仓两套风格、两个名字），落点与模板从同族实物对照，远端属性从 `gh repo view` 取；能读的都读出来了，读不出的只有许可一处，靠用户补上。**

## 一、学到的惯例：七条

第一，命名分两套风格，各自内部统一，都由领域名与资产类型唯一确定。**文档仓**（context、journal、intention、report、gallery、handbook 等）取长名（英文名）`quanttide-{类型}-of-{英文名}`，如 `quanttide-handbook-of-feishu-platform`、`quanttide-report-of-github-platform`；**代码仓**（platform、toolkit、laboratory）取短名（简称）`qtcloud-{简称}`、`quanttide-{简称}-toolkit`、`quanttide-{简称}-lab`。两个名字不是同一个：长名多为两个单词或一个长词（如 `feishu-platform`），短名是一个单词或缩写（如 `feishu`）；都从本仓既有命名反推，不能从中文直译——feishu 有 `quanttide-context-of-feishu-platform`，所以手册是 `-of-feishu-platform`，不是 `-of-feishu`。

第二，落点也分两套。**文档仓**挂固定短名目录：`docs/handbook`、`docs/gallery`、`data/context`、`data/report`；**代码仓**挂 `{标准目录}/{仓库原名}`，如 `examples/quanttide-github-lab`、`apps/qtcloud-github`——直接用原名，避免独立开工作区时重名，`examples/default` 这类固定别名已废弃。都注册进父仓 `.gitmodules`。

第三，初始内容取最小模板，且按类型微调：report 与 handbook 空仓只有 README + LICENSE；lab 与 gallery 用 README + CHANGELOG + LICENSE。README 统一是「标题 + 一句话定位 + 概述：待补充 + 许可」。模板取最新一次建仓的实例——惯例本身也在演进，取最新而非最全（handbook 取 `quanttide-handbook-of-meta-engineering`，比 knowledge-work 的 piece/place/process 更瘦）。

第四，仓库属性：PUBLIC、默认分支 main、描述为「量潮{中文}{类型}」一句话（手册 / 报告 / 案例集 / 实验室）。

第五，云端建仓必须带一个初始文件（`gh repo create --add-readme`），否则子模块指针指向空提交，别人克隆后对应目录是空的。

第六，提交分两层：先在子仓独立提交推送，再回父仓提交指针与 CHANGELOG（`feat: 注册{类型}子模块（path → repo）`），父仓提交后直接推送。子模块再推进时，父仓另起一次「更新指针」提交。

第七，许可证读不出规律，只能问：同一层资产并不统一，lab 用 Apache 2.0（`quanttide-github-lab`、`quanttide-org-lab`），而手册、报告、案例集用 CC BY 4.0。取材实例之间有分歧时，惯例不再自明。

## 二、怎么学到的：从可复算的实物反推，四步

第一步，从当前仓出发：`cat .gitmodules`。feishu 已有 `of-feishu-platform` 系列、唯独缺 `docs/handbook`，「补哪个」由此确定；github 既有 `quanttide-context-of-github-platform` 直接给出长名 `github-platform`，「建 gallery / report」的命名由此确定——都不用问用户。

第二步，上溯一级：读根仓库 `AGENTS.md`，拿到四级路径约定（domains / defaults / assets / adapters）与「子模块独立维护、父仓库只追指针」的分层提交规则，明确一次操作要在两个仓库各提交一次。

第三步，横向对照同族：要建 handbook 就查 `quanttide-work` / `quanttide-org` / `quanttide-agent` 的手册，落点 `docs/handbook` 与命名先例 `of-knowledge-work` 一次就全；要建 lab / gallery 就看哪个适配仓已经有（feishu 有 lab、pi 有 gallery），读它们的 `.gitmodules` 与 README 结构表。再进子模块看实体（README / CHANGELOG / LICENSE），才拿到模板与许可——README 写着「有 gallery」不等于知道 gallery 里放什么。

第四步，用 `gh repo view --json description,licenseInfo` 查远端属性（中文描述、许可、可见性），这是纯文件里查不到的；有歧义再问用户，许可就是这么定的。

## 三、查过的资料：有用四类，没用六类

有用：

- 本仓 `.gitmodules`：一条命令定命名（`of-{英文名}` 系列或缺口）、坐标与短名，是全程价值最高的一份资料。
- 根仓库 `AGENTS.md`：给路径约定、分层提交规则、「提交即推送」规则。
- 同族仓库的 `.gitmodules` 与 README 结构表：给落点与命名先例。
- 同族仓库挂载的实体（README / CHANGELOG / LICENSE）：给模板与许可；`gh repo view --json description,licenseInfo` 给中文名、许可、可见性，纯文档里查不到。

没用或低效：

- 全仓 grep「handbook」「惯例」「lab」：命中的多是业务内容或不命中，惯例类问题按字面 grep 是碰运气，按结构定向翻实物更可靠。
- 全工作区 grep「dev-guide」：只确认了该文档此一份。
- `devops-submodule` SKILL：只是通用 git submodule 操作手册，回答「怎么做」，不回答「长什么样」。
- 遍历全部 domains 找「哪些领域缺 handbook」：跑偏一步，要的是当前仓库的手册，不是全组织的盘点。
- `quanttide-asset-lab` 的三个候选路径：两个是空壳，真身在校区仓下，白翻两个。
- `gh repo view --json homepage`：字段名错，白跑一次。

## 四、边界与落地

边界：本篇只管「按惯例从零建一个资产子模块仓库」这一件事，不管仓库里内容怎么写（那是各类型仓库内部的写作惯例）。样本是两次建仓、四次成功创建、一次否定（许可），够用来说清这条工作线，不够用来断言「许可就该这么选」或「模板永远取最新」。另有一条本属「怎么做」的边界情况未在此展开——空仓库 `git submodule add` 后处于 unborn branch，需进目录手动 commit + `git push -u origin main` 再重跑挂载——其可执行版本已归档为案例，见 `quanttide-gallery-of-github-platform` 的 `second-brain/init-submodule.md`。

落地：七条惯例自本次起适用于 `quanttide-feishu` 与 `adapters/quanttide-github` 的资产仓。已建：feishu 手册 `quanttide-handbook-of-feishu-platform`（根提交 `ccd33f8`，父仓指针 `51bdee7`）；github 三仓 `quanttide-github-lab`（`e3738e3` / `e050c53`）、`quanttide-gallery-of-github-platform`（`487df3c` / `4fb778e`）、`quanttide-report-of-github-platform`（`1b0de4d` / `1d8aadf`）。
