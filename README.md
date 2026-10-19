# 译幕 · 安装包与更新清单

这个仓库只放**安装包和自动更新清单**，不含源码（源码在私有仓库）。

## 安装

1. 打开 [Releases](https://github.com/kulebbb/CC-4-No-CC-releases/releases/latest)，下载最新的 `.dmg`
2. 打开 dmg，把 `Yimu`（Finder 里显示为「译幕」）拖进「应用程序」
3. 首次启动会被 Gatekeeper 拦住（构建未做 Apple 公证）：右键 App → 打开，或执行
   ```bash
   xattr -dr com.apple.quarantine "/Applications/译幕.app"
   ```

## 更新

装好一次之后，后续版本在 App 内更新：启动时自动检查，托盘菜单里也有「检查更新…」。点横幅里的「下载并安装」会自动下载、替换、重启。

## 发版（维护者）

1. 在私有源码仓库 `kulebbb/CC-4-No-CC` 改版本号（`package.json`、`src-tauri/tauri.conf.json`、`src-tauri/Cargo.toml` 三处必须一致），提交并打 tag：
   ```bash
   git tag v0.2.x && git push origin v0.2.x
   ```
2. 到本仓库 Actions → **Release** → Run workflow，`tag` 填同一个 `v0.2.x`
3. 跑完会在本仓库生成 Release（含 dmg、`.app.tar.gz`、`.sig`、`latest.json`），客户端随即能检测到更新

需要两个仓库密钥：

| 位置 | 名称 | 用途 |
|---|---|---|
| 本仓库 | `SOURCE_REPO_TOKEN` | 只读拉取私有源码（细粒度 PAT，private 仓库的 Contents: Read） |
| 本仓库 | `TAURI_SIGNING_PRIVATE_KEY` / `..._PASSWORD` | 签名更新包，私钥只存在维护者本机与这里 |

`latest.json` 的地址被写死在 App 里（`src-tauri/tauri.conf.json` 的 `plugins.updater.endpoints`）；本仓库改名的话要同步改源码里的这个 URL。
