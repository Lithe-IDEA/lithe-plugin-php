# PHP Support for Lithe

为 [Lithe](https://github.com/1lck/Lithe-IDEA) 提供 PHP 代码智能、运行 PHP 文件和 PHPUnit 测试。
这是一个独立发布的**社区插件**；
它由原先的官方插件 `dev.lithe.plugin.php-support` 迁移而来。

> English: PHP language intelligence through your own Intelephense, plus running PHP files and PHPUnit tests, for Lithe.

## 功能

| 功能 | 依赖的程序 |
| --- | --- |
| 补全、跳转定义、悬停提示、诊断 | 你电脑上的 [Intelephense](https://intelephense.com)（需要 Node.js） |
| 运行当前 `.php` 文件 | 你电脑上的 `php` |
| PHPUnit 测试（整个项目、当前文件、单个测试） | `php` 和项目里的 `vendor/bin/phpunit` |

插件的执行入口全部由 `plugin.json` 声明，ZIP 不包含可执行文件。安装时 Lithe 会弹窗列出以上程序，确认后才会安装。

与原官方插件的区别：官方插件把 Intelephense 打进了插件包；Intelephense 的许可只允许终端用户自行安装使用，
不允许再分发，所以社区版改为启动你自己安装的 `intelephense`。Lithe 会把它发现的 Node.js 目录加到语言服务器的
`PATH` 前面，从程序坞打开 Lithe 时也能找到 `node`。

## 安装

当前发布的 ZIP 适用于 macOS（Apple Silicon 和 Intel）。插件 ID 为
`io.github.lithe-idea.php-support`。宿主需支持该包的 `schemaVersion: 2` 与 `pluginAPIVersion: 1`。
运行和测试还需要宿主支持清单中的 `executionProviders`。

1. 安装 Node.js 和 Intelephense；要运行和测试的话再装 PHP 与 Composer：

   ```bash
   brew install node php composer
   npm install -g intelephense
   ```

2. 从 [Releases](https://github.com/Lithe-IDEA/lithe-plugin-php/releases) 下载 ZIP，
   在兼容的 Lithe 中用 **设置 → 插件 → 从磁盘安装插件** 选择 ZIP 并确认。
3. 启用插件后打开项目中的 `.php` 文件以激活语言服务器。若 Lithe 提示信任工作区，请先确认该项目可信。

当前版本未收录在 [Lithe Store](https://github.com/Lithe-IDEA/Lithe-store) 索引中。

### 禁用与卸载

在插件管理中禁用会停用语言服务器及运行、测试声明。当前桌面宿主会立即应用插件状态变更，
并在卸载前停止它管理的插件进程。Node.js、Intelephense、PHP、Composer 和项目里的
`vendor/bin/phpunit` 都由用户或项目管理，卸载插件不会删除它们。

### 从官方 PHP Support 迁移

如果仍安装了旧版官方 PHP Support（`dev.lithe.plugin.php-support`），请先在插件管理中将它卸载，
再按上面的步骤安装社区版。

## 本地打包

需要 Node.js 22+ 和 `zip`：

```bash
node scripts/package.mjs
```

输出 `dist/php-support-<版本>-darwin.zip`（同时适用 Apple Silicon 与 Intel Mac）和它的 SHA-256。

## 诊断日志

在启用结构化日志 v2 的 Lithe macOS preview 中，宿主按语言服务身份将 Intelephense 的生命周期、
`window/logMessage` 和有界 stderr 诊断送入当前会话的 `lsp/php` 通道。
保留 `--stdio` 启动方式，由宿主统一进行脱敏、截断、写入和保留；插件不创建自己的日志 writer。
PHP 程序与 PHPUnit 的输出仍属于运行/测试控制台，不复制进诊断日志。

Intelephense 由插件声明、宿主执行，插件没有自己的进程发送 `lithe/log`。宿主因此替插件把每次启动尝试
记成与 SDK 插件同一组的操作事实，写入 `plugins/io.github.lithe-idea.php-support/host` 通道，
操作名为 `languageServer/start`：`initialize` 成功记 `plugins.operation.finished`（INFO，含耗时）；
失败记 `plugins.operation.failed`（ERROR），失败码只取自启动阶段——`host.executableUnavailable`
（无法解析 Intelephense 启动命令）、`host.launchFailed`（进程拉起失败）、`host.exitedEarly`（就绪前退出）、
`host.initializeFailed`（初始化握手失败）、`host.timedOut`（初始化超时）。取消、停止和被新启动取代的
尝试不记录；事实不含 Intelephense 输出、路径或命令行。插件不需要任何日志代码，宿主实现见
https://github.com/1lck/Lithe-IDEA/pull/1720。

排障时使用 Lithe 的独立日志窗口和诊断导出，详细诊断仅临时开启。
不要把 PHP 源码、HTTP/数据库内容、环境变量、凭据或完整协议载荷写入诊断日志。
Windows 的日志实现仍为 v1；本文没有声明 Windows 已完成 v2 验收。

## 发布新版本

1. 修改 `io.github.lithe-idea.php-support/plugin.json` 的 `version`，合入 `main`。
2. 推送同名标签：

   ```bash
   git tag v0.2.0 && git push origin v0.2.0
   ```

3. `Release` 工作流会核对标签与版本一致、打包并发布 Release（说明里附 SHA-256）。
4. 发布资产可从 Releases 下载。通过 Store 校验并收录到索引的版本才会在插件市场中显示。

## 许可

[Apache-2.0](LICENSE)。Intelephense 由用户自行安装，按其自身许可使用。
