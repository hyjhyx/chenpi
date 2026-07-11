# chenpi
chenpi-vocabulary
# Chenpi Traceability Core Vocabulary (CTCV) Specification
# 陈皮溯源核心词汇表规范

**Document Version / 文档版本:** 1.0.0
**Namespace URI / 命名空间 URI:** `https://<你的GitHub用户名>.github.io/<仓库名>/vocabulary#`
**Prefix / 推荐前缀:** `chenpi:`

---

## 1. Introduction / 概述

This document defines the custom vocabulary extensions for the Chenpi (Tangerine Peel) agricultural traceability system, strictly adhering to the GS1 EPCIS 2.0 and JSON-LD standard frameworks. The vocabulary specified herein is designed to describe domain-specific agricultural and processing attributes that are not covered by the Core Business Vocabulary (CBV).

本文档定义了陈皮农产品溯源系统的自定义词汇扩展，严格遵循 GS1 EPCIS 2.0 与 JSON-LD 标准框架。本文档规定的词汇旨在描述核心业务词汇（CBV）未能涵盖的特定农业与加工属性。

---

## 2. Property Definitions / 属性定义 (Data Properties)

The following properties are defined for use within EPCIS event extensions (e.g., `ObjectEvent`, `TransformationEvent`).

以下属性定义用于 EPCIS 事件扩展（如对象事件、转换事件）中。

### 2.1 peelMethod
* **URI:** `chenpi:peelMethod`
* **Label (en):** Peeling Method
* **Label (zh):** 开皮工艺
* **Definition (en):** The specific traditional cutting method applied to the fresh citrus fruit to produce the peel (e.g., "Symmetrical Three-Cut Method").
* **Definition (zh):** 应用于新鲜柑橘果实以制取果皮的特定传统切割工艺（例如：“正三刀法”、“对称二刀法”）。
* **Expected Type (数据类型):** `xsd:string`

### 2.2 agingYears
* **URI:** `chenpi:agingYears`
* **Label (en):** Aging Years
* **Label (zh):** 陈化年份
* **Definition (en):** The cumulative duration, measured in years, that the citrus peel has been stored under controlled conditions for the aging process.
* **Definition (zh):** 柑橘皮在受控环境下为实现陈化过程而存放的累计时长（以年为计算单位）。
* **Expected Type (数据类型):** `xsd:integer`

### 2.3 ambientTemperature
* **URI:** `chenpi:ambientTemperature`
* **Label (en):** Ambient Temperature
* **Label (zh):** 环境温度
* **Definition (en):** The recorded environmental temperature in Celsius during a specific agricultural or processing event (e.g., harvesting, sun-drying).
* **Definition (zh):** 在特定农业或加工事件（如采摘、翻晒）期间记录的环境温度（摄氏度）。
* **Expected Type (数据类型):** `xsd:decimal`

### 2.4 ambientHumidity
* **URI:** `chenpi:ambientHumidity`
* **Label (en):** Ambient Humidity
* **Label (zh):** 环境湿度
* **Definition (en):** The relative humidity percentage of the environment recorded during an event.
* **Definition (zh):** 事件记录期间环境的相对湿度百分比。
* **Expected Type (数据类型):** `xsd:decimal`

### 2.5 agriculturalPractice
* **URI:** `chenpi:agriculturalPractice`
* **Label (en):** Agricultural Practice
* **Label (zh):** 农事操作规范
* **Definition (en):** Specific agricultural or physical processing actions performed on the object, serving as a detailed supplement to the standard CBV `bizStep`. Examples include "Sun-drying" or "Pest Control".
* **Definition (zh):** 对溯源对象执行的具体农事或物理处理操作，作为对标准 CBV `bizStep` 的详细补充。例如“自然翻晒”或“病虫害防治”。
* **Expected Type (数据类型):** `xsd:string`

---

## 3. Usage Example in EPCIS 2.0 / EPCIS 2.0 使用规范示例

When integrating this vocabulary into an EPCIS JSON-LD payload, the namespace must be declared in the `@context` array.

将此词汇表集成到 EPCIS JSON-LD 载荷时，必须在 `@context` 数组中声明命名空间。

```json
{
  "@context": [
    "[https://ref.gs1.org/standards/epcis/2.0.0/epcis-context.jsonld](https://ref.gs1.org/standards/epcis/2.0.0/epcis-context.jsonld)",
    {
      "chenpi": "https://<你的GitHub用户名>.github.io/<仓库名>/vocabulary#"
    }
  ],
  "type": "ObjectEvent",
  "action": "OBSERVE",
  "bizStep": "urn:epcglobal:cbv:bizstep:inspecting",
  "epcList": ["urn:epc:class:lgtin:6901234.056789.BATCH-2023"],
  "chenpi:agriculturalPractice": "自然翻晒",
  "chenpi:ambientTemperature": 28.5,
  "chenpi:ambientHumidity": 45.0
}
