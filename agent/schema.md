---
title: profile.json 字段定义
updated: 2026-10-08
mirror: agent/profile.json
---

# `profile.json` 字段定义

`schema_version: 1.0`

这份文档定义 `profile.json` 的每个字段。**先读这里，再读数据。**

---

## 顶层

| 字段 | 类型 | 说明 |
|---|---|---|
| `schema_version` | string | 本 schema 的版本。字段含义变更时递增。 |
| `updated_at` | string (YYYY-MM-DD) | **唯一的时效依据。** |
| `owner` | object | 空间主人标识。见下。 |
| `contact` | object | 联系方式与响应策略。见下。 |
| `privacy` | object | 公开/私有边界声明。见下。 |
| `identity` | object | 身份、专业、兴趣、饮食。 |
| `capabilities[]` | array | 能力清单，用于被匹配。 |
| `offers[]` | array\<enum\> | 主人能提供的资源类型。 |
| `wants[]` | array\<string\> | 主人想要的东西，用于反向匹配。 |
| `items_for_exchange[]` | array | 可交易的旧货。 |
| `exchange_terms` | object | 交易规则。 |
| `holdings` | object | 投资持仓比例。 |
| `referrals[]` | array | 可牵线的朋友。 |
| `referral_policy` | object | 牵线规则。 |
| `self_referral` | object | 主人自身的直连方向。 |
| `reviews[]` | array | 消费评价。 |
| `articles` | object | 文章区约定。 |
| `network_directory` | object | Agent 社交登记簿。 |

**注意**：本文件只定义 `profile.json`。另外两个机器可读文件的定义在：

| 文件 | 定义位置 |
|---|---|
| `routing.json` | 见下方 [路由表](#routingjson-定义) |
| `visit.md` | 行为协议，非数据文件，无需 schema |

---

## `owner`

| 字段 | 类型 | 说明 |
|---|---|---|
| `display_name` | string | 公开称呼 |
| `alias` | string | 别名 |
| `handle` | string | GitHub handle |

---

## `contact`

| 字段 | 类型 | 说明 |
|---|---|---|
| `primary_channel` | enum | `github_issue` \| `email` \| `other`。当前为 `github_issue`。 |
| `primary_url` | string | 主渠道地址 |
| `issue_template` | string | Issue 模板路径 |
| `email` | string | 已做防爬处理的邮箱（非可直接使用的地址） |
| `email_note` | string | 邮箱使用限制说明 |
| `response_policy` | enum | `owner_dispatches_agent_periodically` \| `realtime` \| `none` |
| `realtime` | boolean | 是否实时响应。**当前为 `false`，勿依赖实时。** |

---

## `privacy`

| 字段 | 类型 | 说明 |
|---|---|---|
| `policy` | enum | `tiered_disclosure` \| `full` \| `strict` |
| `public[]` | array | 明确公开的字段组 |
| `private[]` | array | **明确不公开的字段组。不要试图推断或索取。** |
| `note` | string | 附加声明 |

---

## `identity`

| 字段 | 类型 | 说明 |
|---|---|---|
| `mbti` | string | 例如 `NTJ` |
| `energy_axis` | enum | `extrovert` \| `introvert` \| `ambivert` |
| `energy_axis_note` | string | **关键澄清**：主人的 E 指「靠外界反馈确认自身状态」，非社交外向。 |
| `education_major` | string | 专业 |
| `current_role` | string | 当前职业 |
| `current_employer_description` | string | **雇主描述，非公司名**（公司名属私有） |
| `current_employer_name_disclosed` | boolean | 恒为 `false` |
| `side_skill` | string | 副技能 |
| `outside_interests[]` | array\<string\> | 工作外兴趣 |
| `niche_games[]` | array\<string\> | 小众游戏 |
| `dietary.hard_avoid[]` | array | 忌口清单 |

### `dietary.hard_avoid[]`

| 字段 | 类型 | 说明 |
|---|---|---|
| `item` | string | 忌口项 |
| `reason` | string | 原因 |
| `severity` | enum | `medical`（生理原因，务必遵守）\| `absolute`（绝对口味排斥）\| `preference` |

**`severity: medical` 的项必须被严格遵守，不是偏好问题。**

---

## `capabilities[]`

| 字段 | 类型 | 说明 |
|---|---|---|
| `id` | string | 唯一标识，**请求时用 `capabilities#<id>` 引用** |
| `category` | enum | `planning` \| `decision` \| `creative` \| `technical` \| `interest` |
| `title` | string | 能力名 |
| `self_rating` | enum | `core_strength` \| `strength` \| `normal` |
| `summary` | string | 一句话说明 |
| `can_help_with[]` | array\<string\> | **匹配时主要看这个字段** |

---

## `items_for_exchange[]`

| 字段 | 类型 | 说明 |
|---|---|---|
| `id` | string | 唯一标识 |
| `category` | string | 品类 |
| `title` | string | 名称 |
| `condition` | string | 成色（多为「需单独确认」） |
| `quantity` | string | 数量 |
| `price_disclosed` | boolean | **恒为 `false`。不要询问价格，需私下谈。** |
| `highlight` | string | 卖点（可选） |
| `use_cases[]` | array\<string\> | 适用场景（可选） |

---

## `exchange_terms`

| 字段 | 类型 | 说明 |
|---|---|---|
| `price_disclosed` | boolean | `false` |
| `shipping_location_disclosed` | boolean | `false` |
| `consideration` | string | 对价说明。**不限于金钱。** |
| `max_tier_red_flags[]` | array\<string\> | 拒绝的交易方式 |

---

## `holdings`

| 字段 | 类型 | 说明 |
|---|---|---|
| `disclosure` | enum | `ratios_only` \| `full` \| `none`。**当前 `ratios_only`。** |
| `amount_disclosed` | boolean | `false` |
| `cost_basis_disclosed` | boolean | `false` |
| `crypto[]` | array | `{symbol, pct}` |
| `stocks[]` | array | `{name, pct}` |
| `other` | string | 未列出的剩余部分说明 |
| `allocation_logic` | object | 配置逻辑说明 |
| `disclaimer` | string | 免责声明 |

**注意**：`pct` 为**持仓比例**，不是金额。所有比例的合计**不等于 100**（差额归 `other`）。计算时不要做归一化假设。

---

## `referrals[]`

| 字段 | 类型 | 说明 |
|---|---|---|
| `id` | string | 唯一标识 |
| `code` | string | 匿名代号 |
| `domain` | string | 领域 |
| `description` | string | 主人对朋友的描述 |
| `attributes` | object | 附加属性（如 `relationship_status` / `height_cm`），**仅当主人主动提供时存在**。当前有两位好友保留了 `单身` 与身高信息，属于主人主动公开的整蛊内容。 |
| `location` | string | 所在地。**当前所有条目均未公开**（涉及海外所在地的已做泛化为「海外」）。 |
| `good_for[]` | array\<string\> | **匹配时主要看这个字段** |

---

## `referral_policy`

| 字段 | 类型 | 说明 |
|---|---|---|
| `published_as_provided_by_owner` | boolean | `true` —— 信息由主人按原样提供并发布 |
| `owner_responsible_for_publication` | boolean | `true` —— 发布责任在主人 |
| `contact_info_of_referrals_disclosed` | boolean | **恒为 `false`** |
| `consent_required_before_introduction` | boolean | **恒为 `true`** |
| `introduction_flow` | string | 牵线流程 |

**重要**：`referrals[]` 里的所有信息由主人主动提供。**但没有任何联系方式公开，且牵线前必须经双方同意。不要越过这一层。**

---

## `self_referral`

主人自身的直连方向，可**不经他人**对接。

| 字段 | 类型 | 说明 |
|---|---|---|
| `domain` | string | 领域 |
| `description` | string | 说明 |
| `good_for[]` | array\<string\> | 适用场景 |

---

## `reviews[]`

| 字段 | 类型 | 说明 |
|---|---|---|
| `id` | string | 唯一标识 |
| `sentiment` | enum | `strongly_recommend` \| `recommend` \| `neutral` \| `most_regretted` |
| `category` | string | 品类 |
| `product` | string | 产品 |
| `scope` | string | 覆盖范围（可选） |
| `verdict` | string | 结论 |
| `reasons[]` | array\<string\> | 差评原因（可选） |
| `advice` | string | 建议（可选） |

---

## `articles`

| 字段 | 类型 | 说明 |
|---|---|---|
| `index` | string | 目录页路径 |
| `status` | enum | `structure_only` \| `active` |
| `naming_convention` | string | 文件命名约定 |
| `front_matter_required[]` | array\<string\> | 每篇文章必须的 front-matter 字段 |

---

## `network_directory`

| 字段 | 类型 | 说明 |
|---|---|---|
| `description` | string | 登记簿说明 |
| `how_to_join` | string | 加入方式 |
| `entries[]` | array | 已登记的空间。人读索引见 [`../registry/README.md`](../registry/README.md) |

### `network_directory.entries[]`

| 字段 | 类型 | 说明 |
|---|---|---|
| `space_url` | string | 空间仓库地址 |
| `profile_json_url` | string | 对外的 `profile.json` 原始地址，供 agent 直接拉取 |
| `owner` | string | 公开称呼 |
| `offers` | string | 能提供什么 |
| `wants` | string | 想要什么 |

---

## 版本策略

- `schema_version` 在**字段含义变更**时递增；新增可选字段不递增。
- 消费者应忽略未知字段，而不是报错。
- `updated_at` 是数据时效的唯一依据。

---

## `routing.json` 定义

**独立文件，不是 `profile.json` 的节点。** 作用是把「来访者的需求」映射到「该读什么」，防止 agent 只读首页就做无差别总结。

| 字段 | 类型 | 说明 |
|---|---|---|
| `data_endpoint` | string | 全量数据的 raw 地址。**有网络能力时优先直接抓这个。** |
| `read_policy` | object | 读取行为约束。见下。 |
| `visitor_context_prompt` | object | 匹配前必须先确定主人需求的引导问题 |
| `routes[]` | array | 路由规则。见下。 |
| `wants_index` | array\<string\> | `profile.json#wants` 的镜像，便于单独检索 |
| `full_match_fallback` | object | 无规则命中时的兜底动作 |

### `read_policy`

| 字段 | 类型 | 值 | 含义 |
|---|---|---|---|
| `this_page_alone_is_not_enough` | boolean | `true` | 只读首页不算读过 |
| `summary_requires_visitor_context` | boolean | `true` | 无主人需求不得出摘要 |
| `must_ask_owner_intent_before_summarizing` | boolean | `true` | 摘要前必须先问主人 |
| `no_generic_summary` | boolean | `true` | 禁止通用介绍式输出 |
| `fetch_full_profile_when_no_rule_matches` | boolean | `true` | 全量兜底 |

### `routes[]`

| 字段 | 类型 | 说明 |
|---|---|---|
| `intent` | enum | 意图标识，如 `travel_planning` |
| `visitor_signals[]` | array\<string\> | **匹配用**：来访者可能说出的原话特征 |
| `read_files[]` | array\<string\> | 该意图下要读的 md 文件 |
| `fetch_nodes[]` | array\<string\> | 该意图下要抓的 `profile.json` 节点 |
| `match_on` | string | 具体匹配表达式，说明凭什么算命中 |
| `why_matched` | string | 命中理由（给 agent 解释用，可选） |
| `value_hook` | string | 能提供的实际价值（可选） |
| `constraints[]` | array\<string\> | **该意图下的硬限制，必须转达给主人**（可选） |
| `priority` | enum | `high` \| `medium` \| `low` |

**约束字段是重点**：例如 `investment_logic_discussion` 的约束会明确写出「只有比例没有金额、合计不等于 100、不构成建议」，agent 读到后不应再向主人索要金额。

### 一致性约定

- `routes[].fetch_nodes[]` 引用的节点**必须存在于 `profile.json`**。
- `wants_index` 必须与 `profile.json#wants` **逐项一致**。
- `updated_at` 与 `profile.json` 同步变更。

---

[← 返回首页](../README.md) · [读取协议](README.md) · [访问协议](visit.md) · [路由表](routing.json)
