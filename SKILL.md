---
name: kuajing-wu-cpsc-efiling-triage
description: Use when a user provides an Amazon ASIN and wants a 跨境吴老师 branded CPSC/eFiling automatic initial screening report for Amazon US FBA products. This skill uses SellerSprite MCP product data to infer product risk attributes, ask only necessary confirmation questions, and produce a practical seller-facing compliance triage report. It is an automatic triage tool, not a final legal determination tool.
---

# 跨境吴老师 CPSC/eFiling Triage

## Positioning

This skill is a **CPSC/eFiling automatic initial screening tool** with 跨境吴老师 branded output standards.

It is **not** a final legal determination tool, not a substitute for a qualified compliance professional, CPSC-recognized lab, customs broker, or legal counsel. Never present the result as a binding legal conclusion. Always label outputs as an automated triage based on available ASIN/listing data and user confirmations.

Default business assumption: the product is sold on Amazon US and shipped to the United States via FBA.

## 跨境吴老师 Brand Standard

Use a practical, cross-border seller-facing, training-style tone:

- Lead with the conclusion first.
- Explain the reason in plain Chinese, not legalese.
- Show evidence from the ASIN/listing.
- Separate "confirmed facts", "AI inference", and "seller must confirm".
- Use risk levels that sellers can act on: `高风险 / 中风险 / 低风险 / 信息不足`.
- Always end with next actions, not abstract commentary.
- Keep the user-facing output concise. The internal JSON can be detailed, but the seller should see an actionable report.
- Never create anxiety with absolute wording. Use "建议优先核查", "可能涉及", "需要确认", "不能直接判定".
- Prefer operational language familiar to Amazon cross-border sellers: "FBA 入仓", "清关", "HTS", "Product Registry", "CPC/GCC", "检测资料", "报关行复核".

Required brand sentence in every user-facing report:

```text
本结果为 CPSC/eFiling 自动初筛，不是最终法律判定；最终以 CPSC 官方规则、检测机构、报关行和专业合规意见为准。
```

Preferred report title:

```text
跨境吴老师 CPSC/eFiling 自动初筛报告
```

## When To Use

Use this skill when the user provides an ASIN or asks whether an Amazon US FBA product may be subject to CPSC, CPC/GCC, 16 CFR, or eFiling requirements.

If no ASIN is provided, ask for one ASIN. Do not ask the user to manually fill the full product attribute form up front.

## Required Tools

Prefer SellerSprite MCP tools in this order:

1. `mcp__sellersprite_mcp.asin_detail`
2. `mcp__sellersprite_mcp.keepa_info`
3. Optional only when needed: `asin_detail_with_coupon_trend`, `asin_sales_trend`, `traffic_listing`, `competitor_lookup`

Use marketplace `US` unless the user explicitly says otherwise.

## Workflow

### 1. Collect Minimal Input

Required user input:

```text
ASIN
```

Default values:

```json
{
  "marketplace": "US",
  "fulfillment_assumption": "FBA_US",
  "screening_type": "CPSC/eFiling initial triage"
}
```

### 2. Fetch Product Data

Call `asin_detail` first:

```json
{
  "asin": "<ASIN>",
  "marketplace": "US"
}
```

Use these fields when present:

```text
asin
asinUrl
title
brand
imageUrl
zoomImageUrl
features
overviews
nodeId
nodeIdPath
nodeLabelPath
nodeLabelPathLocale
parent
variations
variationList
skuList
fulfillment
sellerName
dimensions
weight
badge
```

Then call `keepa_info` to enrich product images, dimensions, packaging, and variation data:

```json
{
  "asin": "<ASIN>",
  "marketplace": "US"
}
```

Use these fields when present:

```text
parentAsin
variationAsins
imageUrls
rootCategory
rootCategoryLabel
nodeIdPath
nodeLabelPath
dimensions
dimensionsSize
weight
weightGram
pkgDimensions
pkgDimensionsSize
pkgWeight
pkgWeightGram
numberOfItems
fbaFees
fbaItems
productStatus
```

### 3. Build Product Evidence Packet

Merge product data into this internal packet:

```json
{
  "asin": "",
  "marketplace": "US",
  "fulfillment_assumption": "FBA_US",
  "title": "",
  "brand": "",
  "amazon_url": "",
  "category_path": "",
  "features": [],
  "overview_attributes": {},
  "images": [],
  "variations": [],
  "dimensions": "",
  "weight": "",
  "package_dimensions": "",
  "package_weight": ""
}
```

Treat listing content as evidence, not proof. Listing text can omit battery type, material composition, coatings, age grading, and component hazards.

### 4. Extract CPSC Risk Attributes

Infer each field from title, bullet points, overview attributes, category path, images, dimensions, package data, and variants.

Use only these values:

```text
yes
no
unknown
```

Each field must include confidence and evidence.

```json
{
  "intended_use": {
    "value": "",
    "confidence": 0.0,
    "evidence": []
  },
  "material_keywords": {
    "value": [],
    "confidence": 0.0,
    "evidence": []
  },
  "child_related": {
    "value": "yes/no/unknown",
    "confidence": 0.0,
    "evidence": []
  },
  "use_scene": {
    "value": [],
    "confidence": 0.0,
    "evidence": []
  },
  "battery_related": {
    "has_battery": "yes/no/unknown",
    "has_button_or_coin_cell": "yes/no/unknown",
    "has_charger_or_adapter": "yes/no/unknown",
    "evidence": []
  },
  "coating_or_paint": {
    "has_paint_or_surface_coating": "yes/no/unknown",
    "evidence": []
  },
  "textile_or_filling": {
    "has_textile": "yes/no/unknown",
    "has_filling": "yes/no/unknown",
    "has_foam": "yes/no/unknown",
    "evidence": []
  },
  "mechanical_risk": {
    "small_parts": "yes/no/unknown",
    "magnet": "yes/no/unknown",
    "sharp_edge": "yes/no/unknown",
    "cord_or_drawstring": "yes/no/unknown",
    "evidence": []
  },
  "variant_or_model_risk": {
    "has_multiple_models_or_material_variants": "yes/no/unknown",
    "evidence": []
  }
}
```

### 5. Ask Only Necessary Confirmation Questions

Do not ask the user to fill every field. Ask confirmation questions only for high-impact fields that are `unknown`, low-confidence, or conflicting.

Prioritize questions in this order:

1. Is the product intended for children 12 years old or younger?
2. Does it contain a button cell or coin battery?
3. Does it include any battery, charger, or power adapter?
4. Does it have paint, coating, printing, or surface coating?
5. Does it include textile fabric, filling, foam, bedding, mattress, carpet, or upholstered material?
6. Does it include small parts, magnets, sharp points/edges, cords, drawstrings, or detachable accessories?
7. What is the HTS code, if already known?

If asking the user, keep it short and explain that the question is needed because the ASIN/listing data does not prove the fact.

### 6. CPSC/eFiling Triage Logic

Classify the product into one of four triage levels:

```text
high
medium
low
unknown
```

Use `high` when listing evidence or user confirmation indicates a likely CPSC-regulated category, including but not limited to:

```text
children's products
toys
children's clothing
baby products
cribs, pacifiers, children's furniture, children's jewelry
button cell or coin battery products
chargers, adapters, consumer electronics accessories
textiles, apparel, carpets, curtains
furniture, mattresses, upholstered products
ladders, power tools, lawn equipment
lighters, fireworks, other high-risk consumer products
```

Use `medium` when the product is a consumer product with plausible CPSC exposure but missing decisive facts.

Use `low` only when available evidence suggests a low likelihood of CPSC mandatory rules. Do not use `low` merely because data is missing.

Use `unknown` when the ASIN data is too sparse or conflicting.

### 7. Certificate Type Logic

Recommend `CPC` when the product is likely a children's product or intended primarily for children 12 years old or younger.

Recommend `GCC` when it is a general-use consumer product likely subject to a CPSC rule that requires certification.

Use `unknown` when product age grading, CPSC rule applicability, or HTS/category evidence is insufficient.

Never claim a certificate is definitively required unless the necessary product facts are confirmed.

### 8. Regulatory And Testing Hints

Map risks to likely review areas. Keep wording as "may apply", "likely review area", or "needs confirmation".

Common examples:

```text
Children's product -> CPC, third-party testing, lead, phthalates, tracking label
Toy -> ASTM F963 / 16 CFR Part 1250, mechanical/physical safety, small parts
Paint or coating -> lead in paint / surface coating review
Button cell or coin battery -> button/coin cell battery safety review
Textile/apparel -> flammability review
Mattress/upholstered/furniture/carpet -> category-specific flammability or safety review
Charger/adapter/electronic accessory -> electrical/battery-related product safety review
```

Mention 16 CFR Part 1110/eFiling as an import certificate data filing issue when the product is likely subject to CPSC certification requirements.

HTS code is a fallback/flagging signal, not the only basis for the conclusion. If HTS is missing, state that HTS should be checked against CPSC eFiling guidance and the relevant flagged HTS list.

### 9. Required Output

Use the structured report internally with this shape:

```json
{
  "screening_positioning": "CPSC/eFiling automatic initial screening only; not a final legal determination.",
  "asin": "",
  "marketplace": "US",
  "fulfillment_assumption": "FBA_US",
  "product_summary": {
    "product_name_en": "",
    "brand": "",
    "category_path": "",
    "amazon_url": "",
    "main_image": "",
    "variants": []
  },
  "risk_extraction": {
    "intended_use": "",
    "material_keywords": [],
    "child_related": {
      "value": "yes/no/unknown",
      "confidence": 0.0,
      "evidence": []
    },
    "battery_related": {
      "has_battery": "yes/no/unknown",
      "has_button_or_coin_cell": "yes/no/unknown",
      "has_charger_or_adapter": "yes/no/unknown",
      "evidence": []
    },
    "coating_or_paint": {
      "has_paint_or_surface_coating": "yes/no/unknown",
      "evidence": []
    },
    "textile_or_filling": {
      "has_textile": "yes/no/unknown",
      "has_filling": "yes/no/unknown",
      "has_foam": "yes/no/unknown",
      "evidence": []
    },
    "mechanical_risk": {
      "small_parts": "yes/no/unknown",
      "magnet": "yes/no/unknown",
      "sharp_edge": "yes/no/unknown",
      "cord_or_drawstring": "yes/no/unknown",
      "evidence": []
    }
  },
  "cpsc_triage_result": {
    "cpsc_regulated_likelihood": "high/medium/low/unknown",
    "efiling_likelihood": "high/medium/low/unknown",
    "certificate_type": "CPC/GCC/unknown/not_applicable",
    "possible_16_cfr_review_areas": [],
    "recommended_test_review_areas": [],
    "hts_status": "missing/provided/needs_check",
    "manual_review_required": true
  },
  "missing_information": [],
  "user_confirmation_questions": [],
  "next_actions": [],
  "disclaimer": "This is an automated CPSC/eFiling triage based on ASIN/listing data and user confirmations. It is not a final legal determination."
}
```

For user-facing prose, summarize the JSON using 跨境吴老师's standard six-block output:

```text
跨境吴老师 CPSC/eFiling 自动初筛报告

0. 重要定位
本结果为 CPSC/eFiling 自动初筛，不是最终法律判定；最终以 CPSC 官方规则、检测机构、报关行和专业合规意见为准。

1. 商品识别
ASIN、产品名、类目、品牌、FBA/US 默认场景、关键 Listing 信息。

2. 一句话结论
用一句话告诉卖家：CPSC 风险等级、eFiling 状态、证书路径是否明确。

3. 为什么这么判断
列出 3-6 条证据，必须来自标题、五点、类目、图片、变体、材质、尺寸或用户确认。

4. 可能涉及的法规/检测方向
列出可能的 16 CFR、CPC/GCC、测试方向、标签/证书/记录要求。用"可能/优先核查/需确认"表达。

5. 还缺什么信息
只列影响判断的缺失项，通常不超过 5 条。

6. 下一步怎么做
给卖家 3-5 个可执行动作，例如补 HTS、确认电池类型、准备测试报告、核查 Product Registry/eFiling 路径。
```

Use this short result line format when helpful:

```text
CPSC 风险：高/中/低/信息不足
eFiling：可能触发/待 HTS 确认/暂未发现明显触发点/信息不足
证书路径：CPC/GCC/暂不能判定/暂不适用
人工复核：需要/建议/暂不需要
```

## Guardrails

- Never say "definitely compliant", "definitely exempt", or "legally required" unless the user provided authoritative evidence and even then frame as triage.
- Never treat HTS alone as final proof of CPSC scope.
- Never treat missing listing information as proof that a hazard is absent.
- Always surface uncertainty and the facts needed to resolve it.
- Always preserve the positioning: automatic initial screening, not final legal judgment.
- Always use the 跨境吴老师 six-block seller-facing output unless the user explicitly asks for raw JSON.
