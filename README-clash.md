# Clash 去广告规则集

从 Shadowrocket 主模块的 2951 条规则里提取出 **2744 条**域名级 REJECT，转成 Clash / Mihomo 的 rule-provider 格式。

## 能力边界

Clash 是纯代理工具，**没有 MITM 和内容改写能力**。所以只能做最外层的一层：

| 能力 | Shadowrocket | Clash |
|---|---|---|
| `[Rule]` 域名 REJECT | 有 | **有**（本文件） |
| `[URL Rewrite]` 按路径拦截 | 有 | 无 |
| `[Map Local]` 伪造响应 | 有 | 无 |
| `[Body Rewrite]` 改 JSON | 有 | 无 |
| `[Script]` 跑 JS | 有 | 无 |
| 需要装证书 | 是 | **否** |

实际效果大概是 Shadowrocket 方案的 **四到六成**。

能去掉的：
- 独立广告域名（阿里妈妈、穿山甲接口、广点通上报、各种 SSP/DSP）
- 广告素材 CDN（`pgdt.gtimg.cn`、`adsmind.ugdtimg.com` 这类）
- 埋点与监控上报

去不掉的：
- 开屏广告（走 App 主接口，同域名）
- 信息流广告（JSON 里的字段，要改响应体）
- 用 IP 直连绕过的广告

## 用法

### 方式一：远程 rule-provider（推荐）

把本文件放在任意可访问的 URL，然后在 Clash 配置里加：

```yaml
rule-providers:
  adblock:
    type: http
    behavior: classical
    url: "https://raw.githubusercontent.com/wyq0201/shadowrocket-rules/main/clash-adblock.yaml"
    path: ./ruleset/adblock.yaml
    interval: 86400

rules:
  - RULE-SET,adblock,REJECT
  - GEOIP,CN,DIRECT
  - MATCH,PROXY
```

`RULE-SET,adblock,REJECT` 必须放在 `GEOIP,CN,DIRECT` **之前**，否则国内域名先被直连规则吃掉，广告拦不住。

### 方式二：本地文件

下载 `clash-adblock.yaml`，改成：

```yaml
rule-providers:
  adblock:
    type: file
    behavior: classical
    path: ./ruleset/adblock.yaml
```

### 方式三：直接内联

把 `payload:` 下面的条目全部展开到 `rules:` 里，每条后面加 `,REJECT`。2744 条会让配置文件变得很长，只适合临时测试。

## 客户端兼容

| 客户端 | rule-provider | 说明 |
|---|---|---|
| Clash Meta / Mihomo | 支持 | 推荐，`classical` behavior |
| Clash Verge Rev | 支持 | 同上 |
| Clash for Android | 支持 | 老版本可能不吃 `interval` |
| Surfboard | 支持 | 语法基本一致 |
| ClashX Pro | 支持 | macOS |

## 安卓上想要更强的效果

Clash 只能到这一层。要接近 Shadowrocket 的效果，安卓上有两条路：

**AdGuard for Android** — 自带本地 MITM（HTTPS 过滤），能改响应体、能过滤内容，能力接近 Shadowrocket 模块。需要装它的证书。缺点是和 Clash 抢 VPN 槽位，二选一。

**Root + LSPosed 模块** — 从客户端层面改 App，效果最彻底，但需要解锁 Bootloader + Root，门槛高，且微信、银行类 App 会检测。

## 维护

主模块更新后重新生成一次即可。本文件与 `blockAds-local.srmodule` 同源，去掉了已解封的条目（京东 `dns.jd.com`、知乎 `appcloud.zhihu.com`、友盟等）。
