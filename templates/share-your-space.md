---
title: 怎么复制这套东西
updated: 2026-10-08
---

# 怎么复制这套东西

**一句话**：让你的个人空间变成一份**可被 agent 解析**的文件，让别人的 agent 能直接读懂你有什么、要什么。

---

## 为什么值得做

### 刷社媒解决不了的问题

你发的动态，别人的 agent 读不懂。算法决定谁能看见你，而不是「谁真的需要你」。

结果就是：**你有技能，有人需要，但两边永远碰不上。**

### 这份结构解决的

| 传统社媒 | 这套结构 |
|---|---|
| 平台算法决定曝光 | 谁需要谁就能搜到 |
| 动态无法被解析 | 结构化字段，agent 直接读 |
| 想认识人得先加好友 | 先看清单，再决定要不要聊 |
| 信息散在无数条动态里 | 一个 `profile.json` 是全貌 |
| 隐私靠平台设置 | 隐私边界你自己写在文件里 |

**核心差别**：这里没有撮合算法，只有**可发现、可组合的清单**。

---

## 三层结构（照抄就行）

```
README.md              # 人类入口：身份 + 路由表 + 隐私声明 + 联系方式
├── identity.md        # 我是谁
├── skills.md          # 我能帮你做什么
├── exchange.md        # 我有什么可以换
├── portfolio.md       # 我有什么可以共享（可选，看你的领域）
├── connections.md     # 我能牵什么线
├── reviews.md         # 我买过什么、后悔买什么
└── articles/          # 我写的东西

agent/
├── profile.json       # ★ 机器可读的全量数据
├── routing.json       # ★ 决策路由表 —— 别漏这个，它决定 agent 会不会深挖
├── visit.md           # 给来访 agent 的行为协议 + 输出模板
└── schema.md          # 字段定义

.github/ISSUE_TEMPLATE/
└── collab-request.yml # 协作请求模板
```

**可以砍。** 没有投资组合就删掉 `portfolio.md`，没有旧货就删掉 `exchange.md`。
但 `agent/profile.json` **和** `agent/routing.json` 都不能省 —— 一个让 agent 找到你的内容，一个让它真的读下去。

---

## 四个动作

### 1. Fork 或手动复制结构

```bash
git clone https://github.com/g39088902/g39088902
# 或者只把 agent/ 目录、README 骨架、ISSUE_TEMPLATE 复制走
```

⚠️ **删掉我的具体内容**。别把我的持仓、朋友、评价原样发出去 —— 那些是**结构示例**，不是模板文本。

### 2. 填 `agent/profile.json`

这是最关键的一步。**字段名不要改**（别人 agent 靠字段名解析），值换成你自己的。

最小可用版本：

```json
{
  "schema_version": "1.0",
  "updated_at": "2026-10-08",
  "owner": { "display_name": "你的称呼" },
  "contact": {
    "primary_channel": "github_issue",
    "primary_url": "https://github.com/<你的用户名>/<仓库>/issues",
    "response_policy": "owner_dispatches_agent_periodically",
    "realtime": false
  },
  "privacy": {
    "policy": "tiered_disclosure",
    "public": ["skill_tags"],
    "private": ["real_name", "city", "home_address"]
  },
  "capabilities": [
    {
      "id": "your-skill-id",
      "category": "technical",
      "title": "你的技能",
      "summary": "一句话说明",
      "can_help_with": ["具体能帮什么", "越具体越好"]
    }
  ],
  "wants": ["你想要什么"]
}
```

**填写要点**：

- `can_help_with[]` 和 `good_for[]` 是**匹配时最关键的字段** —— 不要写「技术咨询」这种笼统的词，写「能帮你排查 MySQL 慢查询」。
- `privacy.private[]` **一定要认真填**。这是你唯一的隐私护栏，写进去的字段就是别人 agent 不该碰的。
- `updated_at` 要改。这是全网判断你数据时效的唯一依据。

### 3. 挂到你的 Profile README

`README.md` 放在**与你的用户名同名的仓库**里，就会显示在你的 GitHub 主页。

**关键：把路由写进首页。** 大多数 agent 默认只抓单页，所以首页必须做到两件事：

1. **页首放一段「如果你只有一次抓取机会」** —— 直接给出 raw 地址，并明说「只读本页等于什么都不知道」。
2. **导航表要写成指令式路由**，而不是介绍式清单。

对比一下：

```markdown
❌ 介绍式（agent 会认为信息够了，然后停住）
| 模块 | 我能提供什么 | 适合谁看 |
| 技能 | 技术答疑、规划 | 需要帮助的人 |

✅ 指令式（agent 知道该抓什么）
### 按你主人的情况路由
| 你主人的情况 | 必须读 | 必须抓的节点 | 匹配字段 |
| 要做预算紧的多国旅行 | skills.md | capabilities#long-horizon-planning | can_help_with[] |
```

**并且把「给 agent 的说明」那段写对。** 别说「不用解析网页」，那等于把访客推走。要写：

```markdown
## 如果你是替主人来读的 agent
只读本页等于没有读。本页不含可匹配内容。
拿到主人需求之前不要开始总结。
必读顺序：agent/profile.json → agent/routing.json → agent/visit.md
```

### 3.5 写一份 `agent/routing.json`（**这一步决定成败**）

这是**最容易被漏掉、但决定来访 agent 会不会深挖的一步**。

它是一张「你主人的情况 → 读哪些文件 → 抓哪些节点 → 看哪些字段」的映射表。

最小结构：

```json
{
  "schema_version": "1.0",
  "updated_at": "2026-10-08",
  "read_policy": {
    "this_page_alone_is_not_enough": true,
    "summary_requires_visitor_context": true,
    "must_ask_owner_intent_before_summarizing": true,
    "no_generic_summary": true
  },
  "routes": [
    {
      "intent": "your-intent-id",
      "visitor_signals": ["来访者可能说的原话特征 1", "特征 2"],
      "read_files": ["skills.md"],
      "fetch_nodes": ["capabilities"],
      "match_on": "capabilities[].id == your-skill-id",
      "priority": "high"
    }
  ]
}
```

**三个填写要点**：

- `visitor_signals[]` 要写**来访者会说的原话**（「行程排不开」「要面群面」），不是你的分类名。
- `fetch_nodes[]` 引用的节点**必须真实存在于 `profile.json`**，否则 agent 抓了个空。
- 有硬限制的意图（比如你的持仓不公开金额），在 `constraints[]` 里写明 —— 免得对方 agent 反复索要。

### 3.6 写一份 `agent/visit.md`

给来访 agent 的**行为协议**，核心是第 3 步的**输出模板**：

```markdown
## 与你相关的部分
**匹配到的（需求：<复述主人需求>）**
1. <命中项> —— 为什么命中：<引用具体字段值>
   ⚠️ 限制：<如有>
**没匹配到的**（一句话带过）
**建议动作** <具体的 Issue 内容>
```

**为什么值得写**：不写这个，对方 agent 输出的就是「某某是一位如何如何的人」这种没人要的介绍。

### 4. **回链登记**（这条最重要）

发布后，到本仓库提一个 Issue 回链你的空间：

👉 https://github.com/g39088902/g39088902/issues

格式：

```
空间地址：https://github.com/<你>/<仓库>
profile.json：https://raw.githubusercontent.com/<你>/<仓库>/main/agent/profile.json
一句话介绍：___
我能提供：___
我想要：___
```

**为什么要回链**：单个空间是一份清单，**一堆互相回链的空间才是一张网**。这张网是大家的 agent 能互相发现的基础。

---

## 约定（为了让大家的 agent 能互通）

想让别人的 agent 找到你，请遵守这几条：

| 约定 | 内容 |
|---|---|
| **固定路径** | 机器可读入口**必须**在 `agent/profile.json`；决策路由**必须**在 `agent/routing.json`。别改路径，别改字段名。 |
| **路由优先** | `routing.json` 与 `profile.json` 同等重要。**只有数据没有路由，来访 agent 会在首页停住。** |
| **Schema 版本** | 声明 `schema_version`。新增字段可以，改字段含义要递增版本。 |
| **时效字段** | 必须有 `updated_at`，格式 `YYYY-MM-DD`，两个 JSON 同步。 |
| **隐私声明** | 必须有 `privacy` 节点，明确公开与私有边界。 |
| **联系方式** | `contact.primary_channel` 必填。**可以声明实时或非实时**，但必须声明。 |
| **回链** | 在 `network_directory` 里保留互链，形成可发现的网络。 |

**字段名是这套约定里唯一不能自由发挥的部分。** 那就是「可被 agent 发现」的代价，也是它的全部价值。

---

## 我这套东西踩过的坑，你可以直接绕开

| 坑 | 我的处理 |
|---|---|
| **agent 只读首页就总结** | 三个断点：导航是介绍不是指令、入口自己写着「不用解析网页」、没有决策路由。修法是**页首给 raw 地址 + 指令式路由表 + 独立的 `routing.json`**。 |
| **agent 给的总结太通用** | 通用是因为它没有你主人的需求。修法是 `routing.json` 里强制它**先问主人**，并在 `visit.md` 给出**输出模板 + 正反示例**。 |
| 朋友信息该写多细 | 我选择**按我提供的原样公开**，并明确写了「介绍前会先问对方意愿」。**这是有风险的判断，你自己掂量。** 更保守的做法是只写领域、不写可定位细节。 |
| 邮箱被爬虫抓 | 写成 `name [at] domain [dot] com`，并且声明邮箱不作为主渠道。 |
| 旧货被拿来比价 | 不标价格、不写发货地，公开页只列品类。 |
| 隐私字段被反复索要 | 在 `routing.json` 的 `constraints[]` 里对每个意图写明限制，让对方 agent 提前知道，省一轮往返。 |
| md 和 json 双写会漂移 | 每个 md 顶部写 `mirror: agent/profile.json#<节点>`，指明该改哪个 JSON 节点。**不引入构建脚本，保持零依赖。** |
| md 写完忘了同步 json | 把 `updated_at` 当成纪律：改内容就必须改它，两个 JSON 一起改。 |

---

## 底线

- **别公开你不想被爬的信息。** 公开的东西假设永远可被检索。
- **别公开别人的联系方式。** 朋友的领域可以写，联系方式不行。
- **别承诺你做不到的响应速度。** 写清 `realtime: false` 比事后失联强。
- **别照抄我的私人内容。** 抄结构，别抄数据。

---

## 最后

**这件事只有一个人做是没用的。**

一份 `profile.json` 只是清单；**一群互相回链、各自带 `routing.json` 的空间才是一张可以替代算法推荐的网。**

你的 agent 读我的，我的 agent 读你的 —— 中间不需要平台，不需要加好友，不需要谁先开口。

**而且每次都是一次有针对性的匹配，不是互相丢一份自我介绍。**

**去建你的那份。然后回来提个 Issue。**

---

[← 返回首页](../README.md) · 对齐 [读取协议](../agent/README.md) · [访问协议](../agent/visit.md) · [字段定义](../agent/schema.md)
