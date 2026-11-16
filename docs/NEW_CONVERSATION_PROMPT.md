# 新对话提示词模板

把下面的提示词粘贴到一个**新对话**的开头，替换尖括号里的内容即可。这个对话就会按仓库约定，只写自己项目目录的文档。

## 用法

1. 先想好项目简称，英文小写加连字符：`power-rail-test`、`uart-bringup`、`ddr-margin-test`。
2. 把临时（试错）要用的分支名也想好（可选）：`topic/power-rail-test`。
3. 粘贴提示词。
4. 对话结束后，它会给出仓库文件链接。

## 提示词（完整版，推荐）

```text
这是一个新的独立任务，需要把进展整理成文档，提交到 GitHub 记忆仓库：
https://github.com/yang1a/yxy_ai_memory.git

请先做两件事，再开始写内容：
1. 克隆仓库到当前工作区（例如 E:\Users\24242\Documents\deepseek-harness\default-workspace\yxy_ai_memory），若已存在则先 git pull --rebase。
2. 完整阅读仓库根目录的 AGENTS.md 和 README.md，按其中的约定执行。

本次项目信息：
- 项目简称（英文小写加连字符）：<项目简称，例如 power-rail-test>
- 中文项目名：<一句话说明这个项目是什么>
- 是否用分支：<直接提交 main / 使用分支 topic/<项目简称>>
- 需要人工复核步骤吗：<需要，写 MANUAL_CHECK.md / 不需要>

你只能写 projects/<项目简称>/ 下的文件，以及在根 README.md 索引表里加一行。
绝对不要修改 projects/ 下其他项目的任何文件，也不要改动别人的 README 行。

PROGRESS.md 必须包含：
1. 状态头：日期（Asia/Shanghai）、项目当前是否已结束、是否有待继续的操作、用户是否已要求停止。
2. 已完成的事实，逐条注明证据范围：哪些经过严格验证，哪些只是部分确认、单次观察或视觉确认。
3. 证据边界单独一节：未验证、未测试、原因未明的事项，不要写成结论。
4. 续接方式：本机文件绝对路径、SHA-256、关键参数、复算命令、已经踩过的坑。

写作要求：
- 用中文，保留真实数据和参数，不要美化成"全部通过"。
- 不把预期结果写成实测结果，不编造校验值、时间或截图内容。
- 不提交原始采样 BIN、日志、bit、固件、ATKDL、ZIP、API Key、Token、私钥。
- 需要图片时只放关键真实截图到 projects/<项目简称>/assets/，并在文中用相对路径引用。

完成后：
- git pull --rebase 后提交，提交信息格式 docs: <项目简称> <本次内容>。
- 推送到 GitHub。若提示没有权限，先告诉我具体报错，不要新建账号级密钥、不要 force push。
- 最后给我 GitHub 上的文件链接，并说明哪些结论是严格验证、哪些还需确认。
```

## 提示词（精简版）

适合任务简单、只要一份进展记录的情况：

```text
新任务，需要把进展写成文档并推送到
https://github.com/yang1a/yxy_ai_memory.git

先克隆仓库并阅读 AGENTS.md。项目简称：<项目简称>，中文说明：<一句话>。
只写 projects/<项目简称>/PROGRESS.md（需要时加 MANUAL_CHECK.md 和 assets/），
并在根 README.md 索引表加一行，不要动其他项目目录。

PROGRESS.md 写清：日期与是否已结束、已完成事实及证据范围、未验证/原因未明的事项、
本机文件路径与 SHA-256、复算或续接方法。中文写作，不美化、不编造数据。
不提交原始数据、日志、固件、ZIP 或任何密钥。

git pull --rebase 后提交（docs: <项目简称> 内容）并推送，最后给我文件链接。
```

## 常见情况

**已经在用分支**：告诉对话分支名，并让它合并前先 `git switch main && git pull --ff-only`。

**上一个对话还在写同一项目**：让新对话先 `git pull --rebase`，或者直接用同一个 `projects/<项目简称>/` 目录续写，不要新建重复目录。

**推送被拒绝（fetch first）**：说明远端有新提交，让它 `git pull --rebase` 后重推，不要 force push。
