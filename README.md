# chenpi — 陈皮溯源自定义词汇表（Custom Vocabulary for GS1 EPCIS 2.0）

> 本文件是项目 EPCIS JSON-LD 扩展的详细词汇规范。
本仓库定义 **`chenpi:` 命名空间**：一套用于陈皮区块链溯源系统的 EPCIS 2.0 自定义扩展词汇。
GS1 EPCIS/CBV 标准词汇无法覆盖陈皮特有的业务属性（品种、成熟度、翻晒轮次、陈化重量等），本词汇表依据 GS1 EPCIS 2.0 的用户扩展机制（user extensions），在自有命名空间下对这些属性给出统一定义。

- **项目背景**：基于 Hyperledger Fabric 2.5 的陈皮供应链溯源系统（农户 → 经销商 → 加工厂 → 分销商 → 零售商 → 消费者验证）
- **维护者**：[@hyjhyx](https://github.com/hyjhyx)
- **版本**：v1.3 文档

---

## 1. 命名空间

| 项 | 值 |
|---|---|
| 前缀 | `chenpi` |
| 命名空间 URI | `https://github.com/hyjhyx/chenpi/tree/main#` |

术语 URI = 命名空间 URI + 术语名，例如 `chenpi:variety` 展开为
`https://github.com/hyjhyx/chenpi/tree/main#variety`；远端页面是否具有对应锚点，需要发布后验证。



## 2. 在 EPCIS JSON-LD 中引用

```json
"@context": [
  "https://ref.gs1.org/standards/epcis/2.0.0/epcis-context.jsonld",
  { "chenpi": "https://github.com/hyjhyx/chenpi/tree/main#" }
]
```

采摘建批事件示例（节选）：

```json
{
  "type": "ObjectEvent",
  "eventTime": "2024-11-10T01:00:00.000Z",
  "eventTimeZoneOffset": "+08:00",
  "action": "ADD",
  "bizStep": "https://github.com/hyjhyx/chenpi/tree/main#harvesting",
  "disposition": "active",
  "quantityList": [{"epcClass": "urn:epc:class:lgtin:6901001.000001.XH2024001", "quantity": 500.0, "uom": "KGM"}],
  "bizLocation": { "id": "urn:epc:id:sgln:6901001.00001.0" },
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
| [`chenpi:seedSource`](#seedsource) | string | 种苗 / 育种来源 | 采摘建批 |
| [`chenpi:plantingDate`](#plantingdate) | date | 种植日期 | 采摘建批 |
| [`chenpi:maturity`](#maturity) | string | 采摘成熟度 | 采摘建批 |
| [`chenpi:weightKg`](#weightkg) | number | 重量（千克），鲜果 / 陈皮通用 | 采摘、收购、进货、开皮、翻晒 |
| [`chenpi:purchaseDate`](#purchasedate) | date | 收购 / 交割日期 | 经销收购、工厂进货 |
| [`chenpi:round`](#round) | integer | 翻晒轮次 | 翻晒陈化 |
| [`chenpi:fileHash`](#filehash) | string | 链下证据文件哈希（SHA-256） | 翻晒陈化 |
| [`chenpi:evidenceReportHash`](#evidencereporthash) | string | AI/人工审核报告 SHA-256 | 翻晒陈化 |
| [`chenpi:aiDecision`](#aidecision) | string | 视觉审核结论 pass/review/reject | 翻晒陈化 |
| [`chenpi:aiReviewStatus`](#aireviewstatus) | string | 自动通过或人工复核通过状态 | 翻晒陈化 |
| [`chenpi:aiModel`](#aimodel) | string | 视觉模型供应商与版本 | 翻晒陈化 |
| [`chenpi:perceptualHash`](#perceptualhash) | string | 64 位图像感知哈希 | 翻晒陈化 |
| [`chenpi:evidenceID`](#evidenceid) | string | 链下证据与公开审核报告编号 | 翻晒陈化 |
| [`chenpi:spec`](#spec) | string | 包装规格 | 出仓打包 |
| [`chenpi:unitCount`](#unitcount) | integer | 单品数量（罐） | 打包、分销、上架 |
| [`chenpi:destGln`](#destgln) | string (URI) | 目的地 GLN | 分销发货 |
| [`chenpi:inboundDate`](#inbounddate) | date | 门店入库 / 上架日期 | 零售上架 |
| [`chenpi:soldDate`](#solddate) | date | 售出日期 | 零售售出 |

## 4. 术语定义

<a id="variety"></a>
### variety — 品种
类型 string。柑橘品种名称。陈皮价值与品种强相关（如新会茶枝柑）。
示例：`"chenpi:variety": "茶枝柑"`

<a id="origin"></a>
### origin — 产地
类型 string。产地的人类可读名称。机器可读的产地以事件的 `bizLocation`（GLN）为准，本字段用于展示。
示例：`"chenpi:origin": "广东新会"`

<a id="seedsource"></a>
### seedSource — 种苗来源
类型 string。育种 / 种苗来源说明。
示例：`"chenpi:seedSource": "老陈苗圃"`

<a id="plantingdate"></a>
### plantingDate — 种植日期
类型 date（ISO 8601，`YYYY-MM-DD`）。果树种植日期，属历史信息，随采摘建批一并登记。
示例：`"chenpi:plantingDate": "2020-03-01"`

<a id="maturity"></a>
### maturity — 成熟度
类型 string。采摘时果实成熟度，决定陈皮品类等级。建议取值：`青皮` / `微红皮` / `大红皮`。
示例：`"chenpi:maturity": "大红皮"`

<a id="weightkg"></a>
### weightKg — 重量（千克）
类型 number，单位固定为千克。本事件涉及货物的重量，**鲜果与陈皮通用**：采摘时为鲜果重、收购时为成交重、开皮后为鲜皮重、翻晒时为当次实测重。陈化过程中重量随时间递减，可作为陈化真实性的辅助证据。
示例：`"chenpi:weightKg": 18.5`

<a id="purchasedate"></a>
### purchaseDate — 收购日期
类型 date。货权转移的成交日期（对应 TransactionEvent）。
示例：`"chenpi:purchaseDate": "2024-11-11"`

<a id="round"></a>
### round — 翻晒轮次
类型 integer（从 1 起）。本次翻晒为该陈化批的第几轮。记录具有业务时间和提交交易信息，但可以事后补录；时间跨度与密度可供追溯核验，不能单独证明实物年份。
示例：`"chenpi:round": 2`

<a id="filehash"></a>
### fileHash — 证据文件哈希
类型 string，格式 `sha256:<64位小写十六进制>`。链下证据文件（照片 / 视频等）的 SHA-256 摘要。文件本体存于链下，链上仅存哈希用于防篡改校验。
示例：`"chenpi:fileHash": "sha256:9f2b…c17"`

<a id="evidencereporthash"></a>
### evidenceReportHash — 审核报告哈希
类型 string，格式 `sha256:<64位小写十六进制>`。对最终结构化审核报告计算的摘要，用于核对公开报告是否与链上承诺一致。它与原图的 `fileHash` 不是同一个值。

<a id="aidecision"></a>
### aiDecision — 模型原始结论
类型 string，允许值为 `pass`、`review`、`reject`。它只是视觉模型对图片可见内容的辅助判断，不是最终业务审核结论，也不能证明拍摄时间、地点和批次真实。

<a id="aireviewstatus"></a>
### aiReviewStatus — 最终审核状态
类型 string。当前主要取值为 `auto_approved`、`human_approved`；链上事件只记录已经获准提交的证据，待处理或被拒绝状态保留在链下工作流中。

<a id="aimodel"></a>
### aiModel — 模型标识
类型 string，格式为 `供应商/模型ID`，例如 `qwen/qwen3.8-flash`。用于复现实验和区分模型版本，不表示模型对线下事实作出认证。

<a id="perceptualhash"></a>
### perceptualHash — 图像感知哈希
类型 string，当前为 16 位十六进制表示的 64 位 DCT pHash。用于发现视觉上近似的重复图片；它不是密码学哈希，不能替代 `fileHash` 的完整性校验。

<a id="evidenceid"></a>
### evidenceID — 链下证据引用
类型 string，当前使用 UUID。关联链下原图、完整模型响应、人工复核记录和公开审核报告；只有已经绑定 Fabric 交易的报告才允许公开读取。

<a id="spec"></a>
### spec — 包装规格
类型 string。单品包装规格描述。
示例：`"chenpi:spec": "500g/罐"`

<a id="unitcount"></a>
### unitCount — 单品数量
类型 integer。本事件涉及的单品（罐）数量：打包时为箱内罐数，分销 / 上架时为该批罐数。
示例：`"chenpi:unitCount": 30`

<a id="destgln"></a>
### destGln — 目的地 GLN
类型 string（EPC SGLN URI）。分销发货的目的地（零售门店）位置码。
示例：`"chenpi:destGln": "urn:epc:id:sgln:6905005.00001.0"`

<a id="inbounddate"></a>
### inboundDate — 入库日期
类型 date。零售门店收货入库 / 上架日期。
示例：`"chenpi:inboundDate": "2026-12-10"`

<a id="solddate"></a>
### soldDate — 售出日期
类型 date。项目记录的单品售出日期，对应 `action: DELETE`；v4.6 导出为 `disposition: retail_sold`，原始账本保留旧 `…:sold`。
示例：`"chenpi:soldDate": "2027-01-05"`

## 5. 自定义业务步骤（bizStep）

导出用本命名空间的 Web URI 表达下列工序。原始账本中的 `urn:chenpi:bizstep:peeling`、`urn:chenpi:bizstep:sun_turning` 由版本化适配器显式映射；URN 与 Web URI 并不会自动获得 RDF 同义关系。

<a id="harvesting"></a>
### harvesting — 采摘建批
URI：`https://github.com/hyjhyx/chenpi/tree/main#harvesting`
鲜苷果子被农户采摘

<a id="peeling"></a>
### peeling — 开皮 · 晒制
URI：`https://github.com/hyjhyx/chenpi/tree/main#peeling`
鲜柑果开皮并初步晒制为鲜皮的加工工序。用于 TransformationEvent（鲜果批次 → 陈皮批次），该事件的 `eventTime` 即**陈化起算点**（陈化年份的计算基准）。

<a id="sun_turning"></a>
### sun_turning — 翻晒
URI：`https://github.com/hyjhyx/chenpi/tree/main#sun_turning`
陈化期间对陈皮批的周期性翻晒。用于 ObjectEvent（`action: OBSERVE`），可多次发生；配合 `chenpi:round`、`chenpi:weightKg`、`chenpi:fileHash` 记录过程证据。

## 6. 设计约定

1. 本词汇仅承载 GS1 标准无法表达的领域属性；凡标准可表达者（对象码、时间、地点、单据、状态）一律使用 EPCIS / CBV 标准字段。
2. 日期统一 ISO 8601；事件时间由标准字段 `eventTime` 承载，本词汇中的日期字段仅为业务展示补充。
3. 自定义重量字段 `weightKg` 固定千克；标准 QuantityElement 已知重量则同时填 `quantity` 和 `uom: KGM`。未知投入数量只填 `epcClass`，不填数量和单位。
4. 陈化年份**不设字段**：由包装事件与开皮事件的 `eventTime` 之差在查询时计算，避免可篡改的落库值。

## 7. 许可

本词汇表文档以 CC BY 4.0 发布，欢迎引用与反馈（Issues）。

