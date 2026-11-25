# shadowrocket-rules

Shadowrocket 规则补丁集合。

## adblock-patch.sgmodule

补充 [fmz200/wool_scripts](https://github.com/fmz200/wool_scripts) 的 `blockAds.srmodule` 未覆盖的广告域名。

主模块覆盖 2969 条规则 / 964 个 MITM 域名 / 730 款 App，但以下域名漏了：

| 域名 | 作用 |
|---|---|
| `iytcdn.com` | 第三方广告 SDK，下发开屏配置 `show_time=10` |
| `win.gdt.qq.com` | 广点通竞价成功通知 |
| `h-adashx.ut.cainiao.com` | 阿里广告点击上报（菜鸟） |
| `mon.zijieapi.com` | 字节 / 穿山甲前端监控 |
| `admonitor-feedback.cn-hangzhou.log.aliyuncs.com` | 广告曝光监控 |

### 用法

与主模块叠加，两个都开：

```
主模块： https://raw.githubusercontent.com/fmz200/wool_scripts/main/Shadowrocket/module/blockAds.srmodule
补丁：   https://raw.githubusercontent.com/wyq0201/shadowrocket-rules/main/adblock-patch.sgmodule
```

`配置 → 模块 → 右上角 + → 粘贴链接 → 下载 → 打开开关`

国内访问 raw 慢的话用 jsDelivr：

```
https://cdn.jsdelivr.net/gh/wyq0201/shadowrocket-rules@main/adblock-patch.sgmodule
```

### 维护

抓到新的广告域名时，改 `adblock-patch.sgmodule` 的 `[Rule]` 段，push 后：

- 等 CDN 缓存刷新（raw 约 5 分钟，jsDelivr 更快）
- Shadowrocket `配置 → 模块 → 点模块 → 更新`

模块不会自动更新，需要手动点。
