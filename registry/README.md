---
title: Agent 社交登记簿
updated: 2026-10-08
mirror: agent/profile.json#network_directory
---

# Agent 社交登记簿

**这里收录其他采用同构结构的个人空间。**

单个 `agent/profile.json` 是一份清单；**互相回链的一堆 `profile.json` 才是一张可以替代算法推荐的网。**

---

## 怎么加入

1. 按 [怎么复制这套东西](../templates/share-your-space.md) 建好你自己的空间。
2. 确认机器可读入口在 **`agent/profile.json`**（路径不要改，字段名不要改 —— 这是能被别人 agent 发现的唯一前提）。
3. 到本仓库 [提一个 Issue](https://github.com/g39088902/g39088902/issues) 回链。

**回链格式**：

```
空间地址：https://github.com/<你>/<仓库>
profile.json：https://raw.githubusercontent.com/<你>/<仓库>/main/agent/profile.json
一句话介绍：___
我能提供：___
我想要：___
```

4. 我会把条目加进下面这张表，并同步写入 [`agent/profile.json#network_directory.entries`](../agent/profile.json)。

---

## 登记簿

| 空间 | 主人 | 能提供 | 想要 | profile.json |
|---|---|---|---|---|
| [g39088902](https://github.com/g39088902/g39088902) | 小凡 / Empathy | 技能支持、旧货、投资逻辑交流、朋友牵线 | 配置逻辑讨论、行业信息、新朋友 | [链接](https://raw.githubusercontent.com/g39088902/g39088902/main/agent/profile.json) |

---

## 收录规则

| 规则 | 说明 |
|---|---|
| 机器可读入口必须在 `agent/profile.json` | 路径自由发挥就失去了互通的意义 |
| 必须有 `updated_at` | 否则无法判断数据时效 |
| 必须有 `privacy` 节点 | 明确公开与私有边界，是对所有参与者的保护 |
| 必须有 `contact.primary_channel` | 得让人知道怎么联系你 |
| 不接受纯名单、不接受代管他人信息 | 登记的是**你自己**的空间 |

**我保留不收录的权利**：内容涉及对他人隐私的无授权披露、或纯粹用于批量收集他人信息的，不收。

---

## 为什么这件事需要你来

如果你已经在读这一段，说明你大概率也是个体面人 —— **别只做消费者**。

建你自己的那份，回来提个 Issue。这张网每多一个节点，对所有人的价值都涨一点。

---

[← 返回首页](../README.md) · [读取协议](../agent/README.md) · [字段定义](../agent/schema.md) · [复制模板](../templates/share-your-space.md)
