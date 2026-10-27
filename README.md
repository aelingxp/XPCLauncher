# XPCL（精简开源版）

XPCL 是基于 [Plain Craft Launcher（PCL）](https://github.com/Meloong-Git/PCL) 二次开发的 Minecraft 启动器。
本仓库是**只包含启动器本体**的精简开源版本，可编译出**不含联机模块、百宝箱与任何内置凭据**的 XPCL。

## 仓库结构

| 目录 / 文件 | 说明 |
| --- | --- |
| `XPCL` | 启动器本体（VB.NET + WPF），本仓库要开源的部分 |
| `PCLCS` | C# 辅助库（下载、启动参数、资源解析等），编译必需 |
| `MeloongCore` | 基础库，Git 子模块（Meloong-Git/MeloongCore），编译必需 |
| `XPCL.sln` | Visual Studio 解决方案 |

## 这个版本砍掉了什么

- **联机模块**：只保留「联机」页面本身与说明弹窗；不包含房间服务、组网组件（EasyTier）、中继与路径诊断等任何实现，
  也不提供对接接口。每次进入联机页面都会弹窗说明：为保护 XPCL 用户的隐私与网络安全，暂不开放联机模块的开源部分。
- **百宝箱**：页面、相关代码、设置项与自定义事件一并移除（内存优化因为启动流程还在用，已挪到 `Modules\Base\ModMemory.vb`）。
- **内置凭据**：微软正版登录的 Client ID、CurseForge 的 API Key 不再内置，需要自己申请（见下）。
- **更新检查 / 赞助等级 / 土豆码 / 匿名上报**：相关入口已移除或为空实现。

## 自行申请凭据

编辑 `XPCL\Modules\ModSecret.vb`（文件开头有完整步骤），或用环境变量覆盖（优先级更高）：

| 功能 | 环境变量 | 申请方式 |
| --- | --- | --- |
| 正版登录 | `PCL_MS_CLIENT_ID` | https://entra.microsoft.com → 应用注册 → 新注册 → 平台选「移动和桌面应用程序」→ 复制「应用程序(客户端) ID」 |
| CurseForge | `PCL_CURSEFORGE_API_KEY` | https://console.curseforge.com/ → API Keys → 创建 Key |

不填也能编译、也能正常运行：

- 点「正版登录」会弹窗说明如何申请，并自动退回离线登录；离线登录完全不受影响。
- 走官方 CurseForge 接口的功能（搜索、指纹识别、更新检查）会失败并提示需要自行申请，
  可在「设置 → 下载 → Mod 下载源」改用镜像源继续使用。

## 编译

环境要求：Windows 10/11、.NET Framework 4.8、Visual Studio 2022（勾选「.NET 桌面开发」）或对应版本的 MSBuild。

```powershell
# 1. 拉取源码（MeloongCore 是子模块，必须带 --recursive）
git clone --recursive https://github.com/<你的账号>/<仓库名>.git
cd <仓库名>

# 2. 还原 C# 库的 NuGet 包（首次编译前需要）
msbuild PCLCS\PCLCS.csproj /t:Restore

# 3. 编译启动器
msbuild XPCL\XPCL.vbproj /p:Configuration=Release
```

产物：`XPCL\bin\XPCL.exe`。依赖库与资源（PCLCS、MeloongCore、Newtonsoft.Json、NAudio 等）都已内嵌在 exe 里，
单独一个 exe 就能运行，首次运行会在 exe 同级生成 `PCL` 数据文件夹。

> 建议同时把 `XPCL\App.config` 复制到 `XPCL\bin\` 并改名为 `XPCL.exe.config`：它开启了
> `useUnsafeHeaderParsing` 并包含程序集绑定重定向，个别下载源出现「协议冲突」或依赖版本不一致时需要它。

## 开源协议

XPCL 基于 PCL 二次开发，不是 PCL 官方版本；原项目版权与许可归原作者所有，详见 [LICENCE](LICENCE)。
`MeloongCore` 以 Git 子模块形式引入，遵循其自身许可。
