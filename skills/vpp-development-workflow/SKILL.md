---
name: vpp-development-workflow
description: 在此 VPP 仓库中定位、实现、评审并验证涉及 src、test、docs 和 CI 的改动。当用户要修改 VPP 核心或插件、编写或排查测试、更新构建或文档目标、或需要判断应使用哪些仓库内命令和文件时使用。
---

# VPP 开发工作流

## 目标

在本仓库内工作时使用此技能。优先使用仓库原生的构建、测试和文档入口，
不要随意改用临时命令。

## 仓库结构

- `src/`：VPP 核心实现、基础库、API 和内置插件
- `test/`：Python 测试框架和功能测试套件
- `docs/`：Sphinx 文档和开发者参考资料
- `.gitlab-ci.yml`、`dockerfile`：CI 镜像和流水线行为
- `extras/`：辅助工具、示例和周边实用组件

## 工作规则

1. 编辑前先定位受影响的子系统。
2. 改动范围保持在能解决问题的最小模块集合内。
3. 验证方式要和改动范围匹配，不要默认全量构建或全量测试。
4. 如果当前环境不支持偏 Linux 的构建或测试目标，要明确说明，并退回到静态验证。
5. 不要把 `build-root/`、`test/venv/` 或临时测试产物当作源码改动。

## 改动路由

### 核心代码与插件

- 涉及转发、接口、协议或报文处理路径时，优先检查 `src/vnet/`、
  `src/vlib/`、`src/vppinfra/`，或 `src/plugins/` 下对应插件。
- 涉及 CLI、API 绑定或 API schema 时，优先检查 `src/vpp/`、
  `src/vpp-api/`、`src/vlibapi/` 以及相关生成目标。
- 如果改动是某个特性的增量修改，新增文件前先查找是否已有对应插件、
  测试和文档页面。

### 测试

- 涉及数据面或集成行为时，优先扩展 `test/` 下已有的 `test_*.py`
  测试套件，而不是新建无关测试框架。
- 遵循 VPP 测试框架约定：`VppTestCase`、报文生成接口、按用例隔离的临时目录，
  以及带过滤条件的 `make test` 执行方式。
- 排查失败时，如果环境生成了相关产物，检查 `/tmp/vpp-unittest-*` 和
  `/tmp/vpp-failed-unittests`。

### 文档

- 用户文档和开发文档统一放在 `docs/` 下。
- 如果行为、配置或开发流程发生变化，且已有对应页面，应在同一次改动里更新文档。
- 优先修改最接近的现有文档页面，不要新增孤立文档。

### CI 与构建镜像

- 涉及流水线行为时，检查 `.gitlab-ci.yml`。
- 涉及构建镜像依赖时，检查 `dockerfile`，并确认 CI 流程仍与镜像假设一致。
- CI 与构建镜像改动要尽量小且可复现。

## 默认流程

1. 先定位子系统，以及最接近的现有实现、测试和文档。
2. 阅读控制该子系统的本地 `Makefile` 或文档目标。
3. 以尽可能小的改动面完成修改。
4. 执行最小但有意义的验证：
   - API 或构建改动：运行定向构建或生成目标
   - 可测试的功能改动：运行定向 `make test TEST=...`
   - 纯文档改动：运行 `make docs` 或相关文档目标
   - 仅在改动涉及格式规范时运行 style 目标
5. 说明哪些内容已验证、哪些未验证，以及环境限制是什么。

## 默认验证策略

### 优先命令

- `make build` or `make build-release` for repository-native builds
- `make test`
- `make test TEST=<filter>`
- `make retest`
- `make test-debug TEST=<filter> DEBUG=gdb`
- `make test-help`
- `make docs`
- `make checkstyle`
- `make test-checkstyle`

### 选择原则

- 当改动行为能映射到已有测试套件或用例时，使用 `make test TEST=<filter>`。
- 如果失败列表已经生成，失败后优先使用 `make retest`。
- 当需要决定 `TEST`、`DEBUG`、`TEST_JOBS` 等参数时，先查看 `make test-help`。
- 纯文档改动优先使用文档目标，不要跑代码构建。
- 如果存在更窄的验证目标，不要默认做全量验证。

## 输出要求

应用此技能时，需要明确给出：

- 选中的子系统和文件
- 为什么这些文件是正确的编辑点
- 实际执行的验证命令，或无法验证的原因
- 本次改动留下的测试或文档缺口

## 附加参考

- 常用路径和命令示例见 [reference.md](reference.md)
