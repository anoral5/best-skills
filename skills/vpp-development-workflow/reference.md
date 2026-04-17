# VPP 仓库参考

## 常用入口

- `README.md`：仓库顶层概览
- `Makefile`：主要构建、运行、文档和代码风格目标
- `test/Makefile`：测试框架目标和过滤参数
- `docs/developer/tests/overview.rst`：测试框架行为说明
- `docs/developer/build-run-debug/testing_vpp.rst`：测试目标用法

## 常用命令

```bash
make help
make build
make build-release
make test-help
make test TEST=test_bfd
make TEST_JOBS=auto test
make retest
make test-debug TEST=<filter> DEBUG=gdb
make docs
make checkstyle
make test-checkstyle
```

## 快速路由提示

- 核心数据面逻辑：`src/vnet/`
- 核心运行时与基础设施：`src/vlib/`、`src/vppinfra/`
- 应用与 CLI：`src/vpp/`
- API 与绑定：`src/vpp-api/`、`src/vlibapi/`
- 内置插件：`src/plugins/`
- Python 功能测试：`test/`
- 开发者与用户文档：`docs/`
- CI 与镜像配置：`.gitlab-ci.yml`、`dockerfile`

## 测试框架说明

- 测试过滤参数使用 `TEST=<file|suffix|file.class.test>`。
- `V=[0|1|2]` 控制测试日志级别。
- `TEST_JOBS=<n|auto>` 控制并行度。
- 失败运行可能在 `/tmp/vpp-unittest-*` 和 `/tmp/vpp-failed-unittests`
  下留下产物。
