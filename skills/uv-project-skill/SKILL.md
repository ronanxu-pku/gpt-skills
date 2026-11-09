---
name: uv-project-skill
description: 配置或调整 Python 项目环境时，默认使用 uv project 和 uv sync，在当前代码工作树的 env 目录中维护依赖和环境。
---

使用 uv project 管理 Python 项目环境，以 pyproject.toml 声明依赖，
以 uv.lock 固定依赖版本，使用 uv sync 安装和同步环境。

## 位置与授权

默认环境布局位于当前代码工作树根目录：

- env/pyproject.toml
- env/uv.lock
- env/.venv/

“当前代码工作树”指 basic、beam 等实际存放项目代码的工作目录，
不是容纳多个工作树的项目外层目录。

创建、删除、重建环境，或增加另一套环境，必须事先获得用户批准。
批准应明确环境位置和操作；重建不能通过先删除再创建绕过审批。
普通依赖同步不得暗中删除或替换整个环境。

修改环境声明和锁文件时，遵守 file-scope-guard-skill。
工具生成缓存不需要额外批准，但缓存例外不包含 .venv。

默认使用一套环境。确有依赖冲突需要拆分时，先说明原因；
新增环境的子目录名称必须由用户指定。
已有布局不符合默认结构时，只提出迁移方案，不自动重构或合并。

## 配置与验证

先检查现有依赖、Python 版本及平台要求，再提出或执行获授权的修改。
Python 版本应在 pyproject.toml 中声明。

对于 env 仅作为依赖容器、不打包项目自身代码的布局，
使用 [tool.uv] package = false。
需要安装项目自身代码时，按实际打包方式配置。

在代码工作树根目录执行：

- uv lock --project env
- uv sync --locked --project env
- uv run --locked --project env <命令>

执行前确认声明文件、锁文件及环境创建操作均已获相应授权。
修改锁文件后审查实际依赖变化；不能只生成锁文件而不安装验证，
就声称环境已配置完成。

新增或修改依赖必须维护 pyproject.toml 和 uv.lock。
不以临时 pip 安装代替依赖声明；
不以 --frozen 绕过声明与锁文件不一致的问题。

## 远端环境

环境声明在本地修改和验证，再按 sync-file-skill 同步。
服务器使用同一份锁文件执行 uv sync --locked 重建所需依赖，
不直接复制本地 .venv，不在服务器临时修改依赖声明。

远端创建、删除或重建环境同样需要批准。
平台不兼容时，先报告原因并提出本地配置修改方案。

分别报告依赖解析、环境安装、导入检查和实际运行结果；
未执行或受硬件限制的检查应明确说明。

共享规则与项目独有规则的冲突、修改及更新方式，遵守
[skill-update-skill](../skill-update-skill/SKILL.md)。
