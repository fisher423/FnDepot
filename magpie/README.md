# Magpie

统一管理 AI 模型与本地网关。给局域网里的 Claude Code、Codex、Gemini CLI 等客户端提供 **OpenAI / Anthropic / Gemini** 三种兼容接口，模型供应商、密钥、用量集中在一处配置。

打包自开源项目 [yetone/magpie](https://github.com/yetone/magpie)（MIT）。

## 端口

| 端口 | 用途 |
|---|---|
| `3430/tcp` | Web 管理页（飞牛桌面图标进入） |
| `3425/tcp` | AI 网关，对局域网提供兼容接口 |

## 首次使用

1. 从飞牛桌面打开 Magpie，输入安装时设置的**访问密钥**（6 位以上，仅英文字母和数字）。
2. 在「供应商」页添加模型服务商，填入对应 API Key。
3. 在「网关」页创建一个**网关密钥**，局域网内的客户端访问 3425 时需要携带它。

```bash
# OpenAI 兼容
export OPENAI_BASE_URL=http://<NAS-IP>:3425/v1
export OPENAI_API_KEY=<网关密钥>

# Anthropic 兼容
export ANTHROPIC_BASE_URL=http://<NAS-IP>:3425
export ANTHROPIC_API_KEY=<网关密钥>

# Gemini 兼容
export GOOGLE_GEMINI_BASE_URL=http://<NAS-IP>:3425
export GEMINI_API_KEY=<网关密钥>
```

验证：

```bash
curl http://<NAS-IP>:3425/v1/models -H "Authorization: Bearer <网关密钥>"
```

## 安装说明

- 以专用用户 `magpie_fnos` 运行，**不使用 root**，安装在存储空间。
- 安装向导中必须设置管理页访问密钥；密钥规则为英文字母 + 数字，6–128 位，建议 12 位以上。
- 应用数据、密钥、日志保存在 `/vol*/@appdata/magpie`，重装或升级不丢失。
- 包内自带 Mozilla CA 根证书，飞牛系统未装 `ca-certificates` 时插件市场与 HTTPS 请求仍可正常工作。

## 忘记访问密钥

通过 SSH 在 NAS 上执行：

```bash
sudo cat /vol*/@appdata/magpie/web.key        # 查看当前密钥

sudo /var/apps/magpie/cmd/main stop
sudo sh -c "printf '%s\n' 'NewKey123456' > /vol*/@appdata/magpie/web.key"
sudo chmod 600 /vol*/@appdata/magpie/web.key
sudo /var/apps/magpie/cmd/main start
```

## 安全提示

- 管理页可以查看和修改模型 API Key，**不要将 3430 / 3425 直接暴露到公网**。
- 远程使用请走 VPN（Tailscale / ZeroTier）或带 TLS 的反向代理。
- 应用内「检查更新」只提示新的 FPK，不会自行替换二进制；升级请通过应用中心安装新包。

## 版本说明

本包跟随上游 `yetone/magpie` 发布节奏，版本号与上游 tag 一致（去掉前缀 `v`）。只做两处飞牛适配：

1. 访问密钥最低长度由上游的 16 位降为 6 位，以适配安装向导。
2. 应用内更新检查交给飞牛应用中心，禁止程序自替换二进制。

上游业务代码未做其它修改。

## 许可与归属

- 上游项目：[yetone/magpie](https://github.com/yetone/magpie)，MIT License
- 飞牛打包：fisher423
