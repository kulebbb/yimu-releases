# 发版与维护

面向维护者。安装与使用说明见 [README.md](README.md)。

## 这个仓库是什么

公开产物仓库：只放安装包与自动更新清单（`latest.json`），不含源码。源码在私有仓库 `kulebbb/CC-4-No-CC`。

公开的原因：App 内更新要匿名拉 `releases/latest/download/latest.json`，私有仓库的 release 资源匿名访问会 404。

## 发版流程

1. 在私有源码仓库改版本号（`package.json`、`src-tauri/tauri.conf.json`、`src-tauri/Cargo.toml` 三处必须一致），提交后打 tag 并推送：

   ```bash
   git tag v0.2.x && git push origin v0.2.x
   ```

2. 到本仓库 Actions → **Release** → Run workflow，`tag` 填同一个 `v0.2.x`。工作流只读检出源码仓库该 tag、编译、签名，发布 dmg / `.app.tar.gz` / `.sig` / `latest.json`。

3. 发布成功后把 release 正文写成用户向的更新日志（中文在前、英文在后，模板见私有仓库的 `CLAUDE.md`）。

## 需要的仓库密钥

| 名称 | 用途 |
|---|---|
| `SOURCE_REPO_TOKEN` | 只读拉取私有源码（细粒度 PAT，私有仓库的 Contents: Read-only） |
| `TAURI_SIGNING_PRIVATE_KEY` / `..._PASSWORD` | 签名更新包，私钥只存在于维护者本机与这里 |
| `APPLE_CERT_P12_BASE64` / `APPLE_CERT_PASSWORD` | 固定自签名证书，签名身份跨版本不变，否则每次更新后屏幕录制授权失效 |

## 注意

- `latest.json` 的地址被写死在 App 里（源码仓库 `src-tauri/tauri.conf.json` 的 `plugins.updater.endpoints`），本仓库改名要同步改源码，以及官网 `site/src/i18n/dict.ts` 里的仓库地址。
- 必须是正式发布（非草稿），否则 `latest.json` 会 404。
- 运行器必须是 `macos-26`，`screencapturekit → apple-metal` 需要 macOS 26 SDK。
- 产物名必须是 ASCII（`Yimu`），中文名靠 `Info.plist` 的 `CFBundleDisplayName` 与 `bundle.macOS.bundleName`。
