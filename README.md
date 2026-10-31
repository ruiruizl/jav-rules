# JavDB 分流规则集

拆成两套：**必须日本节点** 和 **普通代理**。别混着放，DMM/MGStage 的图非日本 IP 直接 403 裂图。

## 文件

| 文件 | 用途 | 策略 |
|---|---|---|
| `JavDB-JP.list` | Surge / Loon / Shadowrocket / QuantumultX 格式 | `日本节点` |
| `JavDB-Proxy.list` | 同上 | `PROXY` |
| `JavDB-JP.yaml` | Clash rule-provider（classical） | 不写策略，由 RULE-SET 指定 |
| `JavDB-Proxy.yaml` | 同上 | 同上 |

## 域名清单

### 必须日本节点（3 条）
- `pics.dmm.co.jp` — DMM 封面拼接
- `awsimgsrc.dmm.co.jp` — DMM 封面拼接（AWS 源）
- `image.mgstage.com` — MGStage 封面临时链接，必须日本 IP

### 普通代理（9 条）
- 核心：`jdforrepam.com`、`c0.jdbstatic.com`、`tp.spfcas.com`
- 播放源：`jable.tv`、`memojav.com`、`javp.cc`、`javp.cc.cd`、`missav.ai`
- 预告片/元数据：`fourhoi.com`

## 用法

### Surge
```
[Rule]
RULE-SET, https://raw.githubusercontent.com/ruiruizl/jav-rules/main/JavDB-JP.list, 日本节点
RULE-SET, https://raw.githubusercontent.com/ruiruizl/jav-rules/main/JavDB-Proxy.list, PROXY
```

### Loon
```
[Remote Rule]
https://raw.githubusercontent.com/ruiruizl/jav-rules/main/JavDB-JP.list, policy=日本节点, tag=JavDB-JP
https://raw.githubusercontent.com/ruiruizl/jav-rules/main/JavDB-Proxy.list, policy=PROXY, tag=JavDB-Proxy
```

### Clash / Mihomo
```yaml
rule-providers:
  javdb-jp:
    type: http
    behavior: classical
    url: "https://raw.githubusercontent.com/ruiruizl/jav-rules/main/JavDB-JP.yaml"
    path: ./ruleset/javdb-jp.yaml
    interval: 86400
  javdb-proxy:
    type: http
    behavior: classical
    url: "https://raw.githubusercontent.com/ruiruizl/jav-rules/main/JavDB-Proxy.yaml"
    path: ./ruleset/javdb-proxy.yaml
    interval: 86400

rules:
  - RULE-SET,javdb-jp,日本节点
  - RULE-SET,javdb-proxy,PROXY
```

### Shadowrocket
配置 → 规则 → 添加规则集 → 填 raw 链接，策略选对应节点组。

## 换策略名
`日本节点` 这个名字按你实际策略组名改，一条命令搞定（Loon/Surge 端）：

```sh
sed 's/日本节点/你的节点组名/g' JavDB-JP.list > JavDB-JP.local.list
```

私有化：把 raw 链接的 `ruiruizl/jav-rules` 换成你自己的 fork 即可。

## 注意
- 规则顺序：`JavDB-JP` 必须排在 `JavDB-Proxy` 前面，也没有重叠域名，其实谁先谁后都行——但如果你的 `PROXY` 是「自动选择/负载均衡」组，务必确认它不会把日本图片域名分流到非日本节点。
- `javp.cc.cd` 不能被 `javp.cc` 覆盖掉，两条都保留。
- 镜像站换域名自己加一条 `DOMAIN-SUFFIX`，别改策略名。
