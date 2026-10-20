# PHP Support for Lithe

为 [Lithe](https://github.com/1lck/Lithe-IDEA) 提供 PHP 代码智能、运行 PHP 文件和 PHPUnit 测试。
这是一个通过 [Lithe Store](https://github.com/Lithe-IDEA/Lithe-store) 分发的**社区插件**；
它由原先的官方插件 `dev.lithe.plugin.php-support` 迁移而来。

> English: PHP language intelligence through your own Intelephense, plus running PHP files and PHPUnit tests, for Lithe.

## 功能

| 功能 | 依赖的程序 |
| --- | --- |
| 补全、跳转定义、悬停提示、诊断 | 你电脑上的 [Intelephense](https://intelephense.com)（需要 Node.js） |
| 运行当前 `.php` 文件 | 你电脑上的 `php` |
| PHPUnit 测试（整个项目、当前文件、单个测试） | `php` 和项目里的 `vendor/bin/phpunit` |

插件本身只有一份 `plugin.json`，不附带任何可执行文件。安装时 Lithe 会弹窗列出以上程序，确认后才会安装。

与原官方插件的区别：官方插件把 Intelephense 打进了插件包；Intelephense 的许可只允许终端用户自行安装使用，
不允许再分发，所以社区版改为启动你自己安装的 `intelephense`。Lithe 会把它发现的 Node.js 目录加到语言服务器的
`PATH` 前面，从程序坞打开 Lithe 时也能找到 `node`。

## 安装

1. 安装 Node.js 和 Intelephense；要运行和测试的话再装 PHP 与 Composer：

   ```bash
   brew install node php composer
   npm install -g intelephense
   ```

2. 在 Lithe 中打开 **设置 → 插件 → 插件市场**，搜索 “PHP Support”，安装并确认。
3. 按界面提示重启 Lithe，再打开项目中的 `.php` 文件以激活语言服务器。

Lithe 已预置 Lithe Store 社区插件源。如果你之前移除了它，先在 **⚙ → 管理插件仓库…** 中重新添加
`https://github.com/Lithe-IDEA/Lithe-store`。

### 从官方 PHP Support 迁移

之前装过官方 PHP Support（`dev.lithe.plugin.php-support`）的话，Lithe 的插件管理会把它标成“已迁移到社区插件”。
先卸载旧插件，再按上面的步骤安装本插件。两者声明的语言相同，不要同时启用。

## 本地打包

需要 Node.js 22+ 和 `zip`：

```bash
node scripts/package.mjs
```

输出 `dist/php-support-<版本>-darwin.zip`（同时适用 Apple Silicon 与 Intel Mac）和它的 SHA-256。

## 发布新版本

1. 修改 `io.github.lithe-idea.php-support/plugin.json` 的 `version`，合入 `main`。
2. 推送同名标签：

   ```bash
   git tag v0.2.0 && git push origin v0.2.0
   ```

3. `Release` 工作流会核对标签与版本一致、打包并发布 Release（说明里附 SHA-256）。
4. Lithe Store 的自动追踪会在 6 小时内发现新版本并开合并请求，合并后用户即可在 Lithe 中更新。

## 许可

[Apache-2.0](LICENSE)。Intelephense 由用户自行安装，按其自身许可使用。
