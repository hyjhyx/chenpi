# chenpi — 陈皮溯源自定义词汇表（Custom Vocabulary for GS1 EPCIS 2.0）

> 本文件是项目 EPCIS JSON-LD 扩展的详细词汇规范。仓库根 README 保留与命名空间 URI 对应的全部术语锚点，本文件给出更完整的类型、取值、边界和示例。
> 命名空间 URI 与仓库地址绑定；发布后应逐项验证根 README 的 `#术语名` 锚点可访问。本次只修改本地文件，仍需提交并推送到远端仓库。

本仓库定义 **`chenpi:` 命名空间**：一套用于陈皮区块链溯源系统的 EPCIS 2.0 自定义扩展词汇。
GS1 EPCIS/CBV 标准词汇无法覆盖陈皮特有的业务属性（品种、成熟度、翻晒轮次、陈化重量等），本词汇表依据 GS1 EPCIS 2.0 的用户扩展机制（user extensions），在自有命名空间下对这些属性给出统一定义。

- **项目背景**：基于 Hyperledger Fabric 2.5 的陈皮供应链溯源系统（农户 → 经销商 → 加工厂 → 分销商 → 零售商 → 消费者验证）
- **维护者**：[@hyjhyx](https://github.com/hyjhyx)
- **版本**：v1.3 文档；导出映射 `chenpi-epcis-1.0`（链码 v4.7，EPCIS 映射沿用 v4.6）

---

## 1. 命名空间

| 项 | 值 |
|---|---|
| 前缀 | `chenpi` |
| 命名空间 URI | `https://github.com/hyjhyx/chenpi/tree/main#` |

术语 URI = 命名空间 URI + 术语名，例如 `chenpi:variety` 展开为
`https://github.com/hyjhyx/chenpi/tree/main#variety`；远端页面是否具有对应锚点，需要发布后验证。

> URI 与默认分支 `main` 绑定，请保持分支名稳定；重命名分支会导致已发布 URI 失效。
> 如将来迁移至 GitHub Pages 等长期地址，应保留旧 URI 的访问与明确映射，并发布词汇/Context 新版本；直接改前缀会改变展开后的术语身份，不能静默替换历史文档。

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
表示项目记录的采摘建批，不额外宣称标识首次被使用。旧链内的 `urn:epcglobal:cbv:bizstep:harvesting` 不是本版导出所使用的标准词项；不自动映射为 commissioning。

<a id="peeling"></a>
### peeling — 开皮 · 晒制
URI：`https://github.com/hyjhyx/chenpi/tree/main#peeling`
鲜柑果开皮并初步晒制为鲜皮的加工工序。用于 TransformationEvent（鲜果批次 → 陈皮批次），该事件的 `eventTime` 即**陈化起算点**（陈化年份的计算基准）。

<a id="sun_turning"></a>
### sun_turning — 翻晒
URI：`https://github.com/hyjhyx/chenpi/tree/main#sun_turning`
陈化期间对陈皮批的周期性翻晒。用于 ObjectEvent（`action: OBSERVE`），可多次发生；配合 `chenpi:round`、`chenpi:weightKg`、`chenpi:fileHash` 记录过程证据。v4.7 可进一步附带图像感知哈希、视觉审核摘要和最终报告哈希；这些字段提高可审计性，但不能单独认证线下事实真实。

## 6. 设计约定

1. 本词汇仅承载 GS1 标准无法表达的领域属性；凡标准可表达者（对象码、时间、地点、单据、状态）一律使用 EPCIS / CBV 标准字段。
2. 日期统一 ISO 8601；事件时间由标准字段 `eventTime` 承载，本词汇中的日期字段仅为业务展示补充。
3. 自定义重量字段 `weightKg` 固定千克；标准 QuantityElement 已知重量则同时填 `quantity` 和 `uom: KGM`。未知投入数量只填 `epcClass`，不填数量和单位。
4. 陈化年份**不设字段**：由包装事件与开皮事件的 `eventTime` 之差在查询时计算，避免可篡改的落库值。

## 7. 导出映射与审计扩展

### v4.6 起使用的状态和来源扩展

`https://github.com/hyjhyx/chenpi/tree/main#sellable`：项目的可售业务状态，不推断商品是否处于消费者可接触区域。售出则映射为 CBV `retail_sold`。原始账本内 `sold` 等旧值仍保留。

| 属性 | 类型与含义 |
|---|---|
| `chenpi:mappingVersion` | string；文档和事件采用的导出映射版本 |
| `chenpi:sourceObjectType` / `sourceObjectID` | string；事件所属原始对象类型与内部 ID |
| `chenpi:sourceOwnerMSP` | string；对象的记录组织或翻晒 operator |
| `chenpi:sourceParentType` / `sourceParentID` | string；原始对象直接上游的类型与内部 ID，不是 EPCIS 聚合 parentID |
| `chenpi:sourceTxID` | string；原始事件内交易 ID；批量交易中多条事件可共享 |
| `chenpi:sourceEventDigest` | string；原始 GS1EventObject 经 Go encoding/json 序列化后的 SHA-256；不是区块证明或通用 JSON 规范化签名 |
| `chenpi:legacyBizStep` / `legacyDisposition` | string；发生映射时保留的旧值 |
| `chenpi:legacyTransactions` | object array；每项保存 `chenpi:documentType` 和 `chenpi:documentNumber` |
| `chenpi:inputQuantityStatus` | string；当前为 `not_recorded` |
| `chenpi:collectionCompleteness` | string；文档层固定为 `not_attested`，不证明集合完整 |
| `chenpi:sourceChannel` | string；可读取到通道名时添加；空 mock 通道不输出 |
| `chenpi:mspId` | string；Party Master Data 中的 Fabric MSP 标识 |
| `chenpi:gcp` | string；Party Master Data 中登记的 GS1 Company Prefix |

`sourceObjectID`、`sourceParentID` 是应用内部编号，例如 `LU-06`、`PKG-F5`；它们不等于 LGTIN、SGTIN、SSCC 等 GS1 标识。`sourceTxID` 是 Fabric 交易 ID，`sourceEventDigest` 是项目定义的事件摘要，两者都不能脱离账本独立充当区块包含证明。

`legacyTransactions` 中每一项包含：

| 属性 | 类型与含义 |
|---|---|
| `chenpi:documentType` | string；原账本保存的旧 CBV 单据类型 URI |
| `chenpi:documentNumber` | string；原始采购单或发票编号 |

来源属性在当前 Context 中通过前缀扩展；其值保持普通字符串/数字，不应擅自解释为带 `@id` 类型的 RDF 边。包裹的 `sourceParentID` 用于保留陈化批关联；标准 AggregationEvent 的 `parentID` 仍是 SSCC。对 AgingEvent，sourceObjectID 是 lotUnitID，不是独立唯一键，应联合 eventID、时间和原始来源定位。

事件 ID 在添加上述来源字段前计算，保持与旧导出的事件身份一致。导出摘要只校验返回内容的一致性；真实审计还需校验通道、已提交交易及授权来源。词汇发布后需检查长期可访问性；本次仅修改本地词汇文档，未发布远端命名空间页面。

后续可为柑果（鲜果）等相关对象定义独立命名空间（如 `fruit:`），以独立文档发布，与 `chenpi:` 并行引用于同一 `@context`。术语的新增 / 变更通过版本号与变更记录管理，已发布术语的 URI 与语义保持稳定。

## 8. 许可

本词汇表文档以 CC BY 4.0 发布，欢迎引用与反馈（Issues）。

