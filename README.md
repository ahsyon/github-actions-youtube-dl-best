# Github-actions-youtube-dl-best

使用 GitHub Actions 下载 YouTube 最高画质视频，并自动发布到 Release。

> 2026-11 更新：已把停更的 `youtube-dl` 换成持续维护的 `yt-dlp`，升级所有 Actions 组件，
> 并加入 YouTube 登录 cookies 支持，解决「Sign in to confirm you're not a bot」人机验证问题。

## 注意！

- **GitHub Release 单个文件上限 2 GB，压缩包别超过 2 GB。**（[官方文档](https://docs.github.com/cn/free-pro-team@latest/github/managing-large-files/distributing-large-binaries)）
- 下载完成后尽量删除 Release 中无用的视频文件，**善待 GitHub**。
- cookies 属于敏感凭据，**不要**提交到仓库里，只能放进 GitHub Secret。

## 快速开始

1. Fork 本仓库或 Use this template，并在 Actions 里启用工作流。
2. **配置 cookies（关键步骤，见下文）**——不配置大概率会触发人机验证而失败。
3. 把要下载的视频链接填进 **playlist.txt**，每行一个，commit push。
4. 工作流自动运行，下载的 MP4 打包成 `downloads.tar.gz` 发布到 **Release**。
5. 进 Release 下载压缩包即可。

也可以随时在 Actions 页面点 **Run workflow** 手动触发。

## 配置 YouTube cookies（必做）

GitHub Actions 跑在数据中心 IP 上，YouTube 几乎一定会弹人机验证。解决办法是提供一份已登录
YouTube 的浏览器 cookies。

**第一步：导出 cookies.txt**

- 在 Chrome / Edge 安装扩展 **Get cookies.txt LOCALLY**（开源，本地导出，不上传数据）。
- 浏览器登录你的 YouTube 账号，打开 youtube.com。
- 用扩展导出当前站点的 cookies，保存为 `cookies.txt`（Netscape 格式）。

**第二步：存成 GitHub Secret**

- 仓库页面 → Settings → Secrets and variables → Actions → New repository secret。
- Name 填 `YT_COOKIES`，Value 把 `cookies.txt` 的**全部文本**粘贴进去 → Add secret。

**第三步：重新运行**

- 再次 push 或手动 Run workflow。工作流会把 Secret 写成临时 cookies.txt 供 yt-dlp 使用，
  用完即随 runner 销毁，不会进仓库、不会进 Release。

> 建议用一个专门的小号登录 YouTube 来导出 cookies，避免主账号因自动化下载被风控。
> cookies 会过期（通常几周到几个月），届时重新导出并更新 Secret 即可。

## 常见问题

- **「Sign in to confirm you're not a bot」**：cookies 没配、已过期，或账号被风控。重新导出更新 `YT_COOKIES`。
- **只有低画质**：检查 `config.txt` 的格式选择器；部分视频的高画质需要 cookies 才能取到。
- **403 / Unable to extract**：yt-dlp 每次运行都会自动升到最新版，一般是 YouTube 刚改版，等几天即可。
- **要下音频**：把 `config.txt` 的 `-f` 行改成 `-f bestaudio/best --extract-audio --audio-format mp4`。

## License

[MIT](LICENSE) © Heraldik
