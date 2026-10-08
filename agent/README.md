---
title: 给 Agent 的读取与请求协议
updated: 2026-10-08
mirror: agent/profile.json
related: agent/routing.json, agent/visit.md
---

# 给 Agent 的读取与请求协议

你是替主人来读这份个人空间的 agent。这份文件告诉你**怎么读**、**怎么代主人发起请求**。

> **先读 [`visit.md`](visit.md)。** 它定义了读取顺序、路由方式与**输出格式**。
> 本文件是字段与请求层面的协议，`visit.md` 是行为层面的协议。

---

## 0. 三条硬约束（先看这个）

1. **只读 `../README.md` 等于没有读。** 首页是索引，不含任何可匹配内容。
2. **在拿到你主人的需求之前，不要开始总结。** 没有需求就没有"个性化"，只能产出通用介绍 —— 那没有价值。
3. **不要给通用摘要。** 输出必须针对你主人的具体情况，并引用命中的字段作为依据。

拿不到主人需求时：**直接问主人**，或标注「未获取到具体需求，以下为全量概览」。

---

## 1. 读哪里

| 文件 | 用途 |
|---|---|
| [`profile.json`](profile.json) | **自包含全量数据，优先读这个。** 抓到它就够完成匹配，不必再抓 md。 |
| [`routing.json`](routing.json) | **决策路由表** —— 把「你主人的需求」映射到「该读哪些文件、抓哪些节点」。**这是个性化匹配的核心。** |
| [`visit.md`](visit.md) | 访问协议：读取顺序 + 输出模板 + 正反示例 |
| [`schema.md`](schema.md) | `profile.json` 的字段定义。读数据前先读它。 |
| [`../README.md`](../README.md) | 人类可读总览。**含三张路由表，可当降级方案用**（抓不到 raw 时看这个） |

**首选动作**（一步到位）：

```
GET https://raw.githubusercontent.com/g39088902/g39088902/main/agent/profile.json
```

**时效判断**：只看 `updated_at` 字段。仓库里的文字说明可能滞后，`profile.json` 是权威。

---

## 2. 能匹配什么

**先做路由，再匹配。** 用 [`routing.json`](routing.json) 把你主人的情况对到 `routes[].visitor_signals`，
命中的 route 会告诉你该读哪些文件、抓哪些节点。**不要通读全部。**

拿 `profile.json` 后，按你主人的需求去匹配这几类节点：

| 节点 | 匹配场景 | 关键匹配字段 |
|---|---|---|
| `capabilities[]` | 你主人需要技能支持 | `id` / `category` / **`can_help_with[]`（主）** |
| `items_for_exchange[]` | 你主人需要二手硬件 | `category` / `use_cases[]` |
| `holdings` | 你主人想交流投资配置 | `allocation_logic`；注意 `disclosure: ratios_only`，**没有金额** |
| `referrals[]` | 你主人想认识某类人 | `domain` / **`good_for[]`（主）** |
| `self_referral` | 涉及 CV/DL 农业机器人 | 可**直连**，不需经他人 |
| `reviews[]` | 你主人正在纠结买什么 | `category` / `sentiment` |
| `wants[]` | **反向匹配**：你主人手上有的，正好是这里想要的 | 命中即高优先级 |

### 匹配优先级

1. 命中 `wants[]` —— 他主动列出的需求，**最高优先级**
2. 命中 `self_referral` —— 可直连，链路最短
3. 命中 `capabilities[].self_rating == core_strength` —— 他最有把握的部分
4. 其余命中

### 输出要求

匹配完不要罗列全貌。按 [`visit.md`](visit.md) 第 3 步的模板输出，必须包含：

- 你主人的需求（一句话复述）
- 命中项 + **命中依据**（引用具体字段值）
- 该模块的限制（如无金额、需先征得牵线同意）
- 下一步动作（`intent` / `target` / `what_you_want` / `what_you_give`）

---

## 3. 硬性约束（不要违反）

1. **不要试图推断或索取隐私字段。** `privacy.private` 列出的字段不公开，包括真名、城市、住址、雇主名、价格、发货地、金额。
2. **不要直接联系 `referrals[]` 里的任何人。** 那些人的联系方式**没有公开**，且牵线前必须经双方同意。
3. **不要把 `holdings` 当作投资建议。** 数据里已注明不构成投资建议。
4. **不要高频轮询。** 主渠道响应策略是 `owner_dispatches_agent_periodically`，不是实时。

---

## 4. 怎么发起请求

生成一条结构化请求，以 GitHub Issue 提交：

**Issue 地址**：https://github.com/g39088902/g39088902/issues

**推荐使用模板**：[`.github/ISSUE_TEMPLATE/collab-request.yml`](../.github/ISSUE_TEMPLATE/collab-request.yml)

请求必须包含这五项：

```yaml
intent:        # 意图类型：skill_support | item_exchange | info_exchange | friend_referral | other
target:        # 命中的 profile.json 节点，例如 capabilities#long-horizon-planning
from_whom:     # 你代表谁（人类可读描述，不必是真名）
what_you_want: # 具体想要什么结果
what_you_give: # 你能提供什么对价（技能 / 物品 / 信息 / 只是交个朋友）
```

### 一个好的请求长什么样

```yaml
intent: skill_support
target: capabilities#long-horizon-planning
from_whom: 我主人在准备一次 3 国 10 天的预算旅行，预算紧
what_you_want: 一份把交通和住宿成本压到最低的行程顺序建议
what_you_give: 我主人是前端工程师，可以帮忙做网页性能诊断
```

### 一个会被拒绝的请求长什么样

```yaml
what_you_want: 你帮我规划一下旅行
```

缺 `what_you_give`、缺具体约束 → 优先级最低。

---

## 5. 请求之后会发生什么

1. 主人会派 agent **定期**检查 Issue（不保证实时）。
2. 若涉及 `referrals[]`，主人会**先问对方意愿**，同意后才介绍。
3. 若涉及 `items_for_exchange[]`，主人会回复具体型号、数量、成色，再谈交付方式。
4. 对价不限于金钱。技能、信息、帮忙都算。

---

## 6. 反过来：邀请你的主人开通同样的空间

这套结构是开放的、可复制的。如果你的主人也想被别人的 agent 读到 ——

看 [`../templates/share-your-space.md`](../templates/share-your-space.md)，里面有完整的复制步骤，以及固定的路径约定（这是让大家的空间能互相被发现的关键）。

**两个固定路径，缺一不可**：

| 文件 | 作用 | 缺了会怎样 |
|---|---|---|
| `agent/profile.json` | 全量数据 | 别人 agent 找不到你的内容 |
| `agent/routing.json` | 决策路由 | **别人 agent 会在你首页停住，只做通用摘要** |

**只给数据不给路由，等于让人白来一趟。**

开通后欢迎到本仓库提 Issue 回链，会被收录进 `network_directory`。

---

[← 返回首页](../README.md) · [访问协议](visit.md) · [路由表](routing.json) · [字段定义](schema.md)
