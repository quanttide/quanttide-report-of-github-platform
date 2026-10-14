# 手册仓库创建复盘：一次「遵循惯例」的考古

读这篇复盘的人只想知道三件事：学到了什么惯例、是怎么学到的、查的哪些资料有用哪些没用。2026-10-14，用户一句「遵循惯例创建 handbook 仓库」，指令里没有仓库名、没有落点、没有模板，惯例散落在四个仓库的实物里。本复盘用一句话回答这三个问题：**惯例不在任何一份文档里，它以「同族仓库的既有实例」为载体——命名惯例从本仓 `.gitmodules` 反推，结构惯例从同类仓库实物对照，模板惯例取最新一次建仓；grep 全仓找「惯例」二字反而一无所获。**

## 一、学到的惯例：五条

第一，命名是 `quanttide-handbook-of-{领域英文名}`，而领域英文名要从本仓既有子模块反推，不能从中文直译。本仓（quanttide-feishu）的子模块叫 `quanttide-context-of-feishu-platform`、`quanttide-gallery-of-feishu-platform`，所以手册是 `quanttide-handbook-of-feishu-platform`，不是 `quanttide-handbook-of-feishu`。

第二，落点是父仓库 `docs/handbook`，注册进 `.gitmodules`。同族仓库里 `quanttide-work`、`quanttide-org`、`quanttide-agent` 的手册都挂在 `docs/handbook`，URL 形如 `https://github.com/quanttide/quanttide-handbook-of-{名}.git`。

第三，初始内容取最小模板：README（标题 + 一句话定位 + 「概述：待补充」+ 「许可：CC BY 4.0」）加 LICENSE（CC BY 4.0 全文），不预铺 index、CHANGELOG、目录结构。模板来源是最近一次建仓的 `quanttide-handbook-of-meta-engineering`，比老的手册仓库（如 knowledge-work，有 piece/place/process 三目录）更瘦——惯例本身也在演进，取最新实例。

第四，仓库属性：PUBLIC、默认分支 main、描述为「量潮飞书平台手册」这种一句话中文定位。

第五，提交分两层：先在子仓库独立提交推送（根提交只含 README 与 LICENSE），再回父仓库提交指针与 CHANGELOG——CHANGELOG 在 Unreleased/Added 下补一条「挂载 `docs/handbook` 子模块」，提交后按项目规则直接推送。

另有一个边界情况值得记：GitHub 上先建的空仓库，`git submodule add` 克隆后处于 unborn branch，检出会失败（fatal: 您位于一个尚未初始化的分支）。解法是进目录手动 commit + `git push -u origin main`，再回到父仓库重跑 `git submodule add`，git 会吸收本地已有仓库并补注册 `.gitmodules`。

## 二、怎么学到的：从可复算的实物反推，四步

第一步，从当前仓出发：`ls` 父目录确认自己在 `adapters/quanttide-feishu`，`cat .gitmodules` 发现已有 `of-feishu-platform` 系列子模块，唯独缺 `docs/handbook`——「创建 handbook 仓库」的落点由此确定，不用问用户。

第二步，上溯一级：读根仓库 AGENTS.md，拿到四级路径约定（domains / defaults / assets / adapters）与「子模块独立维护、父仓库只追指针」的分层提交规则，明确这次操作要在两个仓库各提交一次。

第三步，横向对照同族：查 `quanttide-work/.gitmodules` 确认 `docs/handbook` 挂载惯例和 `of-knowledge-work` 命名先例；对 `quanttide-work`、`quanttide-meta`、`quanttide-agent` 三个手册仓库做 `ls` 实物对照，发现结构不同、meta 最新最瘦，选定模板。

第四步，用 `gh repo view` 查已存手册仓库的 visibility 与 description，验证「PUBLIC + 一句话中文描述」的属性惯例，然后才动手建仓。

## 三、查过的资料：有用四类，没用三类

有用：

- 本仓 `.gitmodules`：一条命令定命名（`of-feishu-platform` 系列）与缺口（缺 handbook），是全程价值最高的一份资料。
- 根仓库 `AGENTS.md`：给路径约定、分层提交规则、「提交即推送」规则。
- `quanttide-work/.gitmodules` 与三个手册仓库的目录实物：给落点 `docs/handbook`、命名先例 `of-knowledge-work`、模板演进（老仓繁、meta 瘦）。
- `gh repo view`（元工程/知识工作两个手册仓）：给可见性与描述风格，这是纯文档里查不到的远端属性。

没用或低效：

- 全仓 `grep "handbook|惯例"`：命中的三处全是业务内容（发送邀请邮件的 yaml 注释、qtrecurit 的模板引用），与建仓惯例无关。惯例类问题 grep 整仓是碰运气，按结构定向翻实物更可靠。
- `devops-submodule` SKILL：只是通用 git submodule 操作手册，例子恰好用 `docs/handbook` 但不含命名与模板惯例——它回答「怎么做」，不回答「长什么样」。
- 遍历全部 domains 找「哪些领域缺 handbook」：跑偏了一步，用户要的是当前仓库的手册，不是全组织的盘点；盘点结果对本任务零贡献。

## 四、边界与落地

边界：本篇只管「按惯例从零建一个 handbook 子模块仓库」这一件事，不管手册内容怎么写（那是手册仓库内部的写作惯例，knowledge-work 有 piece/place/process 一套，meta 又是另一套，本轮未涉及）。样本是一次成功创建，无否定样本；「惯例取最新实例」这条在模板出现分歧时才生效，若两个新仓模板也分歧，需要再回元工程层裁定。

落地：五条惯例与空仓库解法自本次起适用于 `quanttide-feishu` 的手册仓库（`quanttide-handbook-of-feishu-platform`，根提交 `ccd33f8`，父仓指针提交 `51bdee7`）；后续给其他仓库补 handbook 时，流程照本篇四步走——先读本仓 `.gitmodules` 反推领域英文名，再定落点与模板。
