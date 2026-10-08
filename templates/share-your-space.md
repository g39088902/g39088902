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
README.md              # 人类入口：身份 + 导航 + 隐私声明 + 联系方式
├── identity.md        # 我是谁
├── skills.md          # 我能帮你做什么
├── exchange.md        # 我有什么可以换
├── portfolio.md       # 我有什么可以共享（可选，看你的领域）
├── connections.md     # 我能牵什么线
├── reviews.md         # 我买过什么、后悔买什么
└── articles/          # 我写的东西

agent/
├── profile.json       # ★ 机器可读的唯一入口
├── README.md          # 给 agent 的读取与请求协议
└── schema.md          # 字段定义

.github/ISSUE_TEMPLATE/
└── collab-request.yml # 协作请求模板
```

**可以砍。** 没有投资组合就删掉 `portfolio.md`，没有旧货就删掉 `exchange.md`。但 `agent/profile.json` 不能省 —— 那是别人 agent 找到你的唯一理由。

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

在 README 里加一段给 agent 的指路：

```markdown
## 给 agent

结构化数据：`agent/profile.json`
读取协议：`agent/README.md`
```

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
| **固定路径** | 机器可读入口**必须**在 `agent/profile.json`。别改路径，别改字段名。 |
| **Schema 版本** | 声明 `schema_version`。新增字段可以，改字段含义要递增版本。 |
| **时效字段** | 必须有 `updated_at`，格式 `YYYY-MM-DD`。 |
| **隐私声明** | 必须有 `privacy` 节点，明确公开与私有边界。 |
| **联系方式** | `contact.primary_channel` 必填。**可以声明实时或非实时**，但必须声明。 |
| **回链** | 在 `network_directory` 里保留互链，形成可发现的网络。 |

**字段名是这套约定里唯一不能自由发挥的部分。** 那就是「可被 agent 发现」的代价，也是它的全部价值。

---

## 我这套东西踩过的坑，你可以直接绕开

| 坑 | 我的处理 |
|---|---|
| 朋友信息该写多细 | 我选择**按我提供的原样公开**，并明确写了「介绍前会先问对方意愿」。**这是有风险的判断，你自己掂量。** 更保守的做法是只写领域、不写可定位细节。 |
| 邮箱被爬虫抓 | 写成 `name [at] domain [dot] com`，并且声明邮箱不作为主渠道。 |
| 旧货被拿来比价 | 不标价格、不写发货地，公开页只列品类。 |
| md 和 json 双写会漂移 | 每个 md 顶部写 `mirror: agent/profile.json#<节点>`，指明该改哪个 JSON 节点。**不引入构建脚本，保持零依赖。** |
| md 写完忘了同步 json | 把 `updated_at` 当成纪律：改内容就必须改它。 |

---

## 底线

- **别公开你不想被爬的信息。** 公开的东西假设永远可被检索。
- **别公开别人的联系方式。** 朋友的领域可以写，联系方式不行。
- **别承诺你做不到的响应速度。** 写清 `realtime: false` 比事后失联强。
- **别照抄我的私人内容。** 抄结构，别抄数据。

---

## 最后

**这件事只有一个人做是没用的。**

一份 `profile.json` 只是清单；**一群互相回链的 `profile.json` 才是一张可以替代算法推荐的网。**

你的 agent 读我的，我的 agent 读你的 —— 中间不需要平台，不需要加好友，不需要谁先开口。

**去建你的那份。然后回来提个 Issue。**

---

[← 返回首页](../README.md) · 对齐 [读取协议](../agent/README.md) · [字段定义](../agent/schema.md)
