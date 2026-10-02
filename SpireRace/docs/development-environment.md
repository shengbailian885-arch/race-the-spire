# 开发环境

目标：Slay the Spire 2 public-beta v0.111.0（41cef1ea），Windows x64。

## 固定工具版本

| 工具 | 版本 | 安装位置 |
|---|---|---|
| .NET SDK | 9.0.318 | `.tools/dotnet` |
| MegaDot C# 编辑器 | 4.5.1-m.14 | `.tools/megadot` |
| ILSpy CLI | 9.1.0.7988 | `.tools/bin` |
| Godot C# 构建 SDK | 4.5.1 | `.local/nuget/packages` |

游戏程序集目标为 .NET 9，引用 GodotSharp 4.5.1 和 Harmony 2.4.2。
SDK 的补丁版本不需要与游戏自带运行时 9.0.7 完全一致，编译目标固定为 net9.0。
MegaDot 是 Mega Crit 的 Godot 定制编辑器；选用 4.5.1 系列官方发布。
ILSpy 9 原始目标为 .NET 8，包装脚本仅在运行 ILSpy 时允许使用项目内的 .NET 9 运行时。

## 使用

在项目根目录打开 PowerShell 7（安装脚本使用 PowerShell 7 的分段下载功能）：

```powershell
. .\scripts\Enter-DevEnvironment.ps1
dotnet --version
& $env:GODOT --headless --version
.\scripts\Invoke-ILSpy.ps1 --version
```

安装或重建工具环境：

```powershell
.\scripts\Install-DevTools.ps1
```

验证工具环境：

```powershell
.\scripts\Verify-DevTools.ps1
```

验证项目和导出文件仅位于 `.local/toolchain-smoke`，不复制到游戏 mods 目录。
验证包括 SDK/编辑器启动、引用本机游戏程序集编译、资源导入及 PCK 导出、游戏接口反编译。
验证成功代表工具链可用，不代表竞速或观战已经实现或通过游戏内测试。

`.tools` 和 `.local` 已从 Git 排除；前者保存工具与下载包，后者保存缓存和临时验证文件。
脚本只修改当前 PowerShell 进程环境，不修改系统 PATH。
MegaDot 通过 `_sc_` 标记启用便携模式，编辑器配置和缓存保存在 `.tools/megadot/editor_data`。
Git 已安装，无需重复安装。命令行已足以开发，IDE 暂不额外安装。

## 本机依赖

- 游戏：`D:\steam\steamapps\common\Slay the Spire 2`
- 编译引用：游戏 `data_sts2_windows_x86_64` 中的 `sts2.dll`、`GodotSharp.dll`、`0Harmony.dll`。
- BaseLib v3.4.7：`D:\steam\steamapps\workshop\content\2868840\3737335127\BaseLib`。
- RitsuLib 0.5.20：作为兼容对象；尚未确定竞速模组需要依赖它。
- 不复制或发布游戏程序集。

## 来源

- [BaseLib 作者的开发环境指南](https://github.com/Alchyr/ModTemplate-StS2/wiki/Setup)
- [Microsoft .NET 9 下载](https://dotnet.microsoft.com/en-us/download/dotnet/9.0)
- [Microsoft 发布元数据及 SDK SHA-512](https://builds.dotnet.microsoft.com/dotnet/release-metadata/9.0/releases.json)
- [Mega Crit 官方 MegaDot](https://megadot.megacrit.com/#4.5.1-m.14)
- [ILSpy 项目](https://github.com/icsharpcode/ILSpy)

SDK 下载使用官方 SHA-512 校验；MegaDot 下载使用官方发布的 SHA-256 校验。

## 验证记录（2026-09-14）

- `.NET SDK 9.0.318` 启动成功。
- MegaDot 返回 `4.5.1.m.14.mono.custom_build`。
- ILSpy CLI 返回 `9.1.0.7988`，成功反编译本机 `INetGameService` 接口。
- 引用本机游戏、GodotSharp、Harmony 的 net9.0 验证项目编译成功，0 警告、0 错误。
- `Godot.NET.Sdk/4.5.1` 的 C# Node 验证项目编译成功，0 警告、0 错误。
- MegaDot 无界面资源导入和 PCK 导出成功，最终日志没有 `ERROR:`。
- 完整脚本输出：`PASS: SDK, engine, game reference compilation, resource packaging, and game decompilation.`
- 以上是工具准备阶段的记录：当时未修改游戏文件、未部署模组、未启动实际对局，玩法尚未实现。后续原型进展见 `prototype-testing.md`。
