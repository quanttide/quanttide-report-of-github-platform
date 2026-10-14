# 资产仓库创建复盘：一次「遵循惯例」的考古

读这篇复盘的人只想知道三件事：学到了什么惯例、是怎么学到的、查的哪些资料有用哪些没用。2026-10-14，用户接连三句「遵循惯例创建 lab / gallery / report 仓库」，指令里都没有仓库名、没有落点、没有模板，惯例散落在四个仓库的实物里。本复盘用一句话回答这三个问题：**惯例不在任何一份文档里，它以「同轴仓库的既有实例」为载体——命名从本仓 `.gitmodules` 反推，落点与模板从同轴仓库实物对照，远端属性从 `gh repo view` 取；能读的都读出来了，读不出的只有一处，许可证，最后靠用户一句「Apache 2.0」补上。**

## 一、学到的惯例：六条

第一，命名分两类。配套资产是 `quanttide-{类型}-of-{平台英文名}`，而平台英文名要从本仓既有子模块反推，不能从中文直译——本仓有 `quanttide-context-of-github-platform`，所以报告是 `quanttide-report-of-github-platform`、案例集是 `quanttide-gallery-of-github-platform`，不是 `-of-github`。实验室是另一类，不带 `-of`，叫 `quanttide-github-lab`。

第二，落点由资产类型唯一确定：`data/context`、`data/report`、`docs/gallery`，实验室挂 `examples/quanttide-github-lab`。同轴的 `quanttide-pi`、`quanttide-feishu` 都照这张表挂，注册进父仓 `.gitmodules`。

第三，初始内容取最小模板，且按类型微调：lab 与 gallery 用 README + CHANGELOG + LICENSE；report 空仓只有 README + LICENSE，不带 CHANGELOG（对照 `quanttide-data`、`quanttide-finance` 的 `data/report`）。README 统一是「标题 + 一句话定位 + 概述：待补充 + 许可」。

第四，仓库属性：PUBLIC、默认分支 main、描述为「量潮{中文}{类型}」一句话（语境 / 报告 / 案例集 / 实验室）。

第五，云端建仓必须带一个初始文件（`gh repo create --add-readme`），否则子模块指针指向空提交，别人克隆后对应目录是空的。

第六，提交分两层：先在子仓独立提交推送，再回父仓提交指针与 CHANGELOG（`feat: 注册{类型}子模块（path → repo）`），父仓提交后推送。子模块再推进时，父仓另起一次「更新指针」提交。

另有一个读不出的例外值得记：许可证。我先照 `quanttide-feishu-lab` 的 CC BY 4.0 给 lab 发了许可证，被判「Apache 2.0」；而 gallery、report 用的是 CC BY 4.0。同一层资产的许可并不统一（`quanttide-org-lab` 也是 Apache 2.0），取材实例不够时只能问。

## 二、怎么学到的：四步

第一步，从当前仓出发：`cat .gitmodules`，既有的 `quanttide-context-of-github-platform` 直接给出平台英文名 `github-platform`，「创建 gallery / report 仓库」的命名由此确定，不用问用户。

第二步，横向对照同轴：要建 lab 就看哪个适配仓已经有 lab（`quanttide-feishu` 有），要建 gallery 就看哪个有 gallery（`quanttide-pi` 有），读它们的 `.gitmodules` 与 README 结构表，命名函数与落点一次就全。

第三步，进子模块看实体：README 写着「有 gallery」，不等于知道 gallery 里放什么。进 `pi/docs/gallery` 看它的 README / CHANGELOG / LICENSE，才拿到模板与许可；report 的瘦模板是从 `quanttide-data`、`quanttide-finance` 的实物对照出来的。

第四步，`gh repo view --json description,licenseInfo` 查远端属性（中文描述、许可、可见性），这是纯文件里查不到的；有歧义再问用户，许可就是这么定的。

## 三、查过的资料：有用四类，没用四类

有用：

- 本仓 `.gitmodules`：一条命令定平台英文名（`of-github-platform`），是全程价值最高的一份资料。
- 同轴仓库的 `.gitmodules` 与 README 结构表：给落点与命名先例。
- 同轴仓库挂载的实体（README / CHANGELOG / LICENSE）：给模板与许可。
- `gh repo view --json description,licenseInfo`：给中文名、许可、可见性，纯文档里查不到。

没用或低效：

- 项目作用域 grep「lab」：作用域只含本仓，邻居仓里现成的 lab 搜不到。
- 全工作区 grep「dev-guide」：惯例类问题按字面 grep 是碰运气，这次只确认了该文档此一份。
- `quanttide-asset-lab` 的三个候选路径：两个是空壳，真身在校区仓下，白翻两个。
- `gh repo view --json homepage`：字段名错，白跑一次。

## 四、边界与落地

边界：本篇只管「按惯例从零建一个资产子模块仓库」这一件事，不管仓库里内容怎么写（那是各类型仓库内部的写作惯例）。样本是连续三次成功创建、一次否定（许可），够用来说清这条工作线，不够用来断言「许可就该这么选」——`quanttide-org-lab` 与 `quanttide-feishu-lab` 的许可就不一致。

落地：六条惯例自本次起适用于 `adapters/quanttide-github` 的资产仓，后续给其他仓库补资产时照本篇四步走——先读本仓 `.gitmodules` 反推平台英文名，再定落点与模板。本仓已建：`quanttide-github-lab`（根提交 `e3738e3`，父仓指针 `e050c53`）、`quanttide-gallery-of-github-platform`（`487df3c`，父仓 `4fb778e`）、`quanttide-report-of-github-platform`（`1b0de4d`，父仓 `1d8aadf`）。
