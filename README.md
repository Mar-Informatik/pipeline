# pipeline

两套用于周度融资表处理与 AI 项目初筛的中文 Agent Skills：`excel` 和 `sourcing`。

## 技能

| 技能 | 输入 | 处理内容 | 输出 |
| --- | --- | --- | --- |
| `excel` | 统一格式的企名片融资 Excel | 筛选显示“是否已存在”为“否”的记录，保留“是”；仅对“否”剔除指定融资轮次和半导体、排序、填写一级行业与负责人公式 | 文件名末尾追加 `v1` 的 Excel，包含“修改后数据”和 `sheet1` |
| `sourcing` | 统一格式的企名片融资 Excel | 宽口径筛选 AI 相关公司，每家公司查找两条相关新闻；另汇总具身智能公司并主动检索本轮 FA | AI 新闻报告和 `具身智能 融资项目.md` 两份中文 Markdown |

两个技能分别位于 `excel/`、`sourcing/`；每个文件夹包含 `SKILL.md` 及其所需资源，可独立安装。

## 环境要求

- 支持 Agent Skills 的 AI 客户端，或能读取完整技能文件夹并按指令执行的智能体
- 文件读写与代码执行能力
- 联网检索与网页阅读能力：`sourcing` 的新闻、业务与 FA 检索需要这些能力，`excel` 分类含糊的公司也可能需要联网查询
- Python 3；Excel 读取脚本的依赖见根目录 `requirements.txt`
- `excel` 需要保留工作簿格式、公式、下拉和自动筛选的 Excel 编辑能力，以及用于检查公式结果的计算引擎

安装读取脚本依赖：

```bash
python3 -m pip install -r requirements.txt
```

这些技能不包含模型、搜索服务或 Excel 计算引擎，运行时使用所在客户端提供的能力。支持的工具、权限与调用语法以所用客户端为准。

## 安装

1. 克隆或下载本仓库
2. 将 `excel/` 和 `sourcing/` 两个完整文件夹按客户端的技能安装说明导入，保留各自的脚本、引用文件和资源文件的相对路径
3. 在客户端技能列表中确认 `excel` 和 `sourcing` 可用

对于支持项目级技能的 Claude Code，可将文件夹分别放入项目的 `.claude/skills/excel/` 和 `.claude/skills/sourcing/`；其他客户端按自己的安装路径或上传入口操作。

如果客户端支持上传技能压缩包，分别压缩两个完整技能文件夹后导入。仅粘贴 `SKILL.md` 会遗漏 Excel 映射表或读取脚本。

`agents/openai.yaml` 是用于支持它的客户端的附加显示配置，其他客户端可按自身规则处理该文件。技能的业务流程位于 `SKILL.md` 中。

## 使用

在客户端中明确选择相应技能并附上 Excel 文件，例如：

> 使用 excel 处理这份企名片融资表，保留已存在记录，按技能规则另存 v1

> 使用 sourcing 处理这份企名片融资表，同时生成 AI 新闻报告与具身智能融资项目汇编

支持 `@` 技能选择的 ChatGPT 环境可使用 `@excel`、`@sourcing`；Claude Code 可使用 `/excel`、`/sourcing`。其他工具请使用自己的技能调用方式。

### 两个技能的公司池范围

- `excel` 将“是”的记录保留在同一工作表内，只通过筛选隐藏；输出文件默认仅显示处理后的“否”记录
- `sourcing` 按其现有规则读取主表内的全部公司记录，忽略“是否已存在”字段，不将 Excel 的隐藏或筛选视图当作公司池过滤条件
- 两个技能分别执行自己的规则；如希望 sourcing 只分析新增项目，需要先准备仅包含相应记录的独立输入文件，或另行修改 sourcing 的范围规则

## 内置资源

| 路径 | 用途 |
| --- | --- |
| `excel/assets/sheet1.json` | `sheet1` 的 11 个行业选项、负责人映射、业务例子及特殊备注 |
| `excel/references/classification.md` | 一级行业的分类口径 |
| `sourcing/scripts/extract_excel.py` | 一次读取 `.xlsx`、`.xlsm`、`.xls` 融资表，并导出公司 JSON |

映射页包含当前团队的负责人姓名，使用者可按自己的分工调整。完整业务规则以各自的 `SKILL.md` 为准。

## 单独运行读取脚本

在仓库根目录运行：

```bash
python3 sourcing/scripts/extract_excel.py input.xlsx --output companies.json
```

也可以一次传入多个 Excel。该脚本只抽取数据，AI 相关性判断、新闻与 FA 检索以及最终文档生成，由使用技能的智能体完成。

仓库不包含真实融资表、生成的项目报告或账号凭据。
