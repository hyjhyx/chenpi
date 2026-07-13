# chenpi — 陈皮溯源自定义词汇表（Custom Vocabulary for GS1 EPCIS 2.0）

本仓库定义 **`chenpi:` 命名空间**：一套用于陈皮（Citrus Peel / Chenpi）区块链溯源系统的 EPCIS 2.0 自定义扩展词汇。
GS1 EPCIS/CBV 标准词汇无法覆盖陈皮特有的业务属性（品种、成熟度、翻晒轮次、陈化重量等），本词汇表依据 GS1 EPCIS 2.0 的用户扩展机制（user extensions），在自有命名空间下对这些属性给出统一定义。

- **项目背景**：基于 Hyperledger Fabric 2.5 的陈皮供应链溯源系统（农户 → 经销商 → 加工厂 → 分销商 → 零售商 → 消费者验证），数据模型对齐 GS1 EPCIS 2.0。
- **维护者**：[@hyjhyx](https://github.com/hyjhyx)
- **版本**：v1.0 （2026-07）

---

## 1. 命名空间

| 项 | 值 |
|---|---|
| 前缀（prefix） | `chenpi` |
| 命名空间 URI | `https://github.com/hyjhyx/chenpi/tree/main#` |

术语 URI = 命名空间 URI + 术语名。例如 `chenpi:variety` 展开为：

```
https://github.com/hyjhyx/chenpi/tree/main#variety
```

该 URI 可直接在浏览器打开，定位到本页对应术语的定义（本文档为每个术语设置了精确锚点）。

> 注：URI 与默认分支 `main` 绑定，请保持分支名稳定（重命名分支将导致已发布 URI 失效）。如需长期稳定的命名空间，可后续迁移至 GitHub Pages（如 `https://hyjhyx.github.io/chenpi/#`），届时仅需在 `@context` 中整体替换前缀，术语名不变。

## 2. 在 EPCIS JSON-LD 中引用

在 `@context` 中声明本命名空间：

```json
"@context": [
  "https://ref.gs1.org/standards/epcis/2.0.0/epcis-context.jsonld",
  { "chenpi": "https://github.com/hyjhyx/chenpi/tree/main#" }
]
```

使用示例（采摘建批事件，节选）：

```json
{
  "type": "ObjectEvent",
  "eventTime": "2024-11-10T01:00:00.000Z",
  "eventTimeZoneOffset": "+08:00",
  "action": "ADD",
  "bizStep": "urn:epcglobal:cbv:bizstep:harvesting",
  "disposition": "urn:epcglobal:cbv:disp:active",
  "epcList": ["urn:epc:class:lgtin:0614141.000001.XH2024001"],
  "bizLocation": { "id": "urn:epc:id:sgln:0614141.00001.0" },
  "chenpi:variety": "茶枝柑",
  "chenpi:maturity": "大红皮",
  "chenpi:weightKg": 500.0
}
```

## 3. 术语总览

| 术语 | 类型 | 含义 | 适用环节 |
|---|---|---|---|
| [`chenpi:variety`](#variety) | string | 品种 | 采摘建批 |
| [`chenpi:origin`](#origin) | string | 产地（可读名） | 采摘建批 |
| [`chenpi:seedSource`](#seedSource) | string | 种苗/育种来源 | 采摘建批 |
| [`chenpi:plantingDate`](#plantingDate) | date (ISO 8601) | 种植日期 | 采摘建批 |
| [`chenpi:maturity`](#maturity) | string | 采摘成熟度 | 采摘建批 |
| [`chenpi:weightKg`](#weightKg) | number | 重量（千克），鲜果/陈皮通用 | 采摘、收购、开皮、翻晒 |
| [`chenpi:purchaseDate`](#purchaseDate) | date | 收购日期 | 经销收购 |
| [`chenpi:round`](#round) | integer | 翻晒轮次 | 翻晒陈化 |
| [`chenpi:fileHash`](#fileHash) | string | 链下证据文件哈希（SHA-256） | 翻晒陈化（可扩展至其他环节） |
| [`chenpi:spec`](#spec) | string | 包装规格 | 出仓打包 |
| [`chenpi:unitCount`](#unitCount) | integer | 单品数量（罐） | 打包、分销、零售 |
| [`chenpi:destGln`](#destGln) | string (URI) | 目的地 GLN | 分销发货 |
| [`chenpi:inboundDate`](#inboundDate) | date | 门店入库/上架日期 | 零售上架 |
| [`chenpi:soldDate`](#soldDate) | date | 售出日期 | 零售售出 |

自定义业务步骤（bizStep）见 [第 5 节](#5-自定义业务步骤bizstep)。

## 4. 术语定义

<a id="variety"></a>
### variety — 品种

- **URI**：`https://github.com/hyjhyx/chenpi/tree/main#variety` · **类型**：string
- 柑橘品种名称。陈皮价值与品种强相关（如新会茶枝柑）。
- 示例：`"chenpi:variety": "茶枝柑"`

<a id="origin"></a>
### origin — 产地

- **URI**：`https://github.com/hyjhyx/chenpi/tree/main#origin` · **类型**：string
- 产地的人类可读名称。机器可读的产地以事件的 `bizLocation`（GLN）为准，本字段用于展示。
- 示例：`"chenpi:origin": "广东新会"`

<a id="seedSource"></a>
### seedSource — 种苗来源

- **URI**：`https://github.com/hyjhyx/chenpi/tree/main#seedSource` · **类型**：string
- 育种/种苗来源说明。
- 示例：`"chenpi:seedSource": "老陈苗圃"`

<a id="plantingDate"></a>
### plantingDate — 种植日期

- **URI**：`https://github.com/hyjhyx/chenpi/tree/main#plantingDate` · **类型**：date（ISO 8601，`YYYY-MM-DD`）
- 果树种植日期（历史信息，随采摘建批一并登记）。
- 示例：`"chenpi:plantingDate": "2020-03-01"`

<a id="maturity"></a>
### maturity — 成熟度

- **URI**：`https://github.com/hyjhyx/chenpi/tree/main#maturity` · **类型**：string
- 采摘时果实成熟度，决定陈皮品类等级。建议取值：`青皮` / `微红皮` / `大红皮`。
- 示例：`"chenpi:maturity": "大红皮"`

<a id="weightKg"></a>
### weightKg — 重量（千克）

- **URI**：`https://github.com/hyjhyx/chenpi/tree/main#weightKg` · **类型**：number（单位固定为千克 kg）
- 本事件涉及货物的重量。**鲜果与陈皮通用**：采摘时为鲜果重、收购时为成交重、开皮后为鲜皮重、翻晒时为当次实测重。陈化过程中重量随时间递减，可作为陈化真实性的辅助证据。
- 示例：`"chenpi:weightKg": 18.5`

<a id="purchaseDate"></a>
### purchaseDate — 收购日期

- **URI**：`https://github.com/hyjhyx/chenpi/tree/main#purchaseDate` · **类型**：date
- 经销商向农户收购的日期（对应 TransactionEvent）。
- 示例：`"chenpi:purchaseDate": "2024-11-11"`

<a id="round"></a>
### round — 翻晒轮次

- **URI**：`https://github.com/hyjhyx/chenpi/tree/main#round` · **类型**：integer（从 1 起）
- 本次翻晒为该陈化批的第几轮。翻晒记录按真实时间逐笔上链，其时间跨度与密度构成陈化年份的过程证据。
- 示例：`"chenpi:round": 2`

<a id="fileHash"></a>
### fileHash — 证据文件哈希

- **URI**：`https://github.com/hyjhyx/chenpi/tree/main#fileHash` · **类型**：string（`sha256:<hex>`）
- 链下证据文件（照片/视频等）的 SHA-256 摘要。文件本体存于链下，链上仅存哈希用于防篡改校验。
- 示例：`"chenpi:fileHash": "sha256:9f2b...c17"`

<a id="spec"></a>
### spec — 包装规格

- **URI**：`https://github.com/hyjhyx/chenpi/tree/main#spec` · **类型**：string
- 单品包装规格描述。
- 示例：`"chenpi:spec": "500g/罐"`

<a id="unitCount"></a>
### unitCount — 单品数量

- **URI**：`https://github.com/hyjhyx/chenpi/tree/main#unitCount` · **类型**：integer
- 本事件涉及的单品（罐）数量：打包时为箱内罐数，分销/上架时为该批罐数。
- 示例：`"chenpi:unitCount": 30`

<a id="destGln"></a>
### destGln — 目的地 GLN

- **URI**：`https://github.com/hyjhyx/chenpi/tree/main#destGln` · **类型**：string（EPC SGLN URI）
- 分销发货的目的地（零售门店）位置码。
- 示例：`"chenpi:destGln": "urn:epc:id:sgln:0614141.00006.0"`

<a id="inboundDate"></a>
### inboundDate — 入库日期

- **URI**：`https://github.com/hyjhyx/chenpi/tree/main#inboundDate` · **类型**：date
- 零售门店收货入库/上架日期。
- 示例：`"chenpi:inboundDate": "2026-12-10"`

<a id="soldDate"></a>
### soldDate — 售出日期

- **URI**：`https://github.com/hyjhyx/chenpi/tree/main#soldDate` · **类型**：date
- 单品售出（脱离供应链，对应 `action: DELETE` + `disposition: sold`）的日期。
- 示例：`"chenpi:soldDate": "2027-01-05"`

## 5. 自定义业务步骤（bizStep）

GS1 CBV 标准词汇未覆盖陈皮特有工序，以下两个 bizStep 在本命名空间定义。EPCIS 2.0 推荐自定义词汇使用可解引用的 Web URI；如需 URN 形式，可等价使用 `urn:chenpi:bizstep:<name>`。

<a id="peeling"></a>
### peeling — 开皮·晒制

- **URI**：`https://github.com/hyjhyx/chenpi/tree/main#peeling`
- 鲜柑果开皮并初步晒制为鲜皮的加工工序。用于 TransformationEvent（鲜果批次 → 陈皮批次），该事件的 `eventTime` 即**陈化起算点**（陈化年份的计算基准）。

<a id="sun_turning"></a>
### sun_turning — 翻晒

- **URI**：`https://github.com/hyjhyx/chenpi/tree/main#sun_turning`
- 陈化期间对陈皮批的周期性翻晒。用于 ObjectEvent（`action: OBSERVE`），可多次发生；配合 `chenpi:round`、`chenpi:weightKg`、`chenpi:fileHash` 记录过程证据。

## 6. 设计约定

1. 本词汇仅承载 GS1 标准无法表达的领域属性；凡标准可表达者（对象码、时间、地点、单据、状态）一律使用 EPCIS/CBV 标准字段。
2. 日期统一 ISO 8601；事件时间由 EPCIS 标准字段 `eventTime` 承载，本词汇中的日期字段仅为业务展示补充。
3. 重量单位固定千克（kg），字段名中显式标注（`weightKg`），不另设单位字段。
4. 陈化年份**不设字段**：由包装事件与开皮事件的 `eventTime` 之差在查询时计算，避免可篡改的落库值。

## 7. 扩展计划

- 本仓库后续可为柑果（鲜果）等相关对象定义独立命名空间（如 `fruit:`），以独立文档或独立仓库发布，与 `chenpi:` 并行引用于同一 `@context`。
- 术语的新增/变更将通过版本号与变更记录管理，已发布术语的 URI 与语义保持稳定。

## 8. 许可

本词汇表文档以 CC BY 4.0 发布，欢迎引用与反馈（Issues）。
