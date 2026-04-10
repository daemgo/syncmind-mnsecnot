# 输出规则

## Markdown 展示模板

```markdown
# 【数字化诊断档案】：{companyName}

> {summary}

## 0. 企业画像
[profile 模块数据：行业、规模、主营业务、产品、商业模式、标签、营收规模]

## 1. 工商信息解读
[registration 模块：解读文本 + 关键发现（≤3条）]

## 2. 组织架构与决策链
[organization 模块：组织类型、一句话决策模式、关键角色（≤4个）]

## 3. 政策与风险
[collect-risk 数据：政策命中信号 + 司法风险等级]

## 4. 时机判断
[timing 模块：所处阶段、判断依据（≤3条）、建议切入策略]

## 5. 痛点与机会
[painPoints（≤5条） + opportunities（≤3条）]

## 6. 招聘信号
[hiring.keySignals：≤3条关键信号]

## 7. 竞争格局
[competition 模块：行业排名、竞对、客户]
```

> 档案生成完毕。运行 `/sales-guide` 获取销售进攻指南。

---

## JSON 写入规则

写入路径：`docs/customer/profile.json`

**重要：写入的 JSON 必须严格遵循下面的结构示例。所有字段都在同一层，不要额外嵌套 `profile` 包裹对象。**

### 目标结构示例

```json
{
  "companyName": "示例科技有限公司",
  "shortName": "示例科技",
  "summary": "一句话企业画像",
  "industry": "软件与信息技术",
  "subIndustry": "企业服务",
  "scale": "中型企业（100-500人）",
  "mainBusiness": "主营业务描述",
  "products": ["产品A", "产品B"],
  "targetCustomers": ["制造业", "零售业"],
  "businessModel": "SaaS订阅",
  "rating": "A",
  "tags": ["高新技术", "专精特新"],
  "website": "https://example.com",
  "slogan": "",
  "isListed": false,
  "stockCode": "",
  "revenueScale": "1-5亿",
  "registration": {
    "registeredCapital": "1000万",
    "paidCapital": "500万",
    "establishedDate": "2015-03-20",
    "legalRepresentative": "张三",
    "businessStatus": "存续",
    "organizationType": "有限责任公司",
    "branchCount": 3,
    "interpretation": "解读文本",
    "keyFindings": ["发现1", "发现2"]
  },
  "organization": {
    "type": "扁平化",
    "decisionChain": "CTO→CEO 双签",
    "keyPersons": [{"name": "张三", "title": "CEO"}],
    "salesStrategy": "攻略建议"
  },
  "timing": {
    "phase": "选型期",
    "analysisBasis": ["依据1"],
    "entryStrategy": "切入策略",
    "urgency": "中等"
  },
  "hiring": {
    "keySignals": ["正在招聘Java架构师", "扩招数据团队"]
  },
  "competition": {
    "industryRank": "TOP 10",
    "marketShare": "5%",
    "mainCompetitors": ["竞对A"],
    "knownClients": ["客户A"]
  },
  "riskAndPolicy": {
    "legal": {
      "lawsuitCount": 2,
      "executedPerson": false,
      "dishonestPerson": false,
      "businessAnomalies": [],
      "equityFreeze": false
    },
    "policy": {
      "isSpecializedSME": true,
      "smeLevel": "国家级",
      "isHighTech": true,
      "digitalTransformation": false,
      "esgGreen": false
    }
  },
  "painPoints": [
    {"area": "领域", "description": "描述", "severity": "高"}
  ],
  "opportunities": [
    {"area": "领域", "description": "描述", "likelihood": "高"}
  ],
  "analysisPath": "A",
  "confidence": 75,
  "metadata": {
    "createdAt": "2026-04-09T01:46:00Z",
    "updatedAt": "2026-04-09T01:46:00Z",
    "createdBy": "AI",
    "version": "1.0",
    "confidence": 75,
    "sources": ["https://example.com"]
  }
}
```

### 字段来源映射

| collect-new 输出 | → profile.json | 说明 |
|----------|------------|------|
| profile.* | 顶层展开 | companyName, shortName, summary, industry 等 |
| registration.* | registration.* | 字段名 1:1 保持 |
| organization.* | organization.* | type, decisionChain, keyPersons[{name,title}], salesStrategy |
| timing.* | timing.* | phase, analysisBasis, entryStrategy, urgency |
| hiring.* | hiring.* | keySignals[]（纯字符串数组，不要用对象） |
| competition.* | competition.* | industryRank, marketShare, mainCompetitors[], knownClients[] |
| painPoints[] | painPoints[] | {area, description, severity}（所有值为字符串） |
| opportunities[] | opportunities[] | {area, description, likelihood}（所有值为字符串） |
| analysisPath | analysisPath | A/B+/B |
| confidence | confidence | 20-100 |

| collect-risk 输出 | → profile.json | 说明 |
|----------|------------|------|
| legal.* | riskAndPolicy.legal.* | lawsuitCount, executedPerson, dishonestPerson, businessAnomalies[], equityFreeze |
| policy.* | riskAndPolicy.policy.* | isSpecializedSME, smeLevel, isHighTech, digitalTransformation(bool), esgGreen(bool) |
| 命中的政策标签 | tags[]（追加） | 如"专精特新""高新技术" |

### metadata 字段（与 companyName 同级）

| 字段 | 规则 |
|------|------|
| createdAt | 首次生成时写入当前时间（ISO 8601） |
| updatedAt | 每次更新时写入当前时间 |
| createdBy | 固定 `"AI"` |
| version | 见 SKILL.md 版本管理规则 |
| confidence | 信息丰富度分数（20-100） |
| sources | 所有搜索中引用的有效 URL 列表 |

### 不写入的字段（其他 skill/系统负责）

| 字段 | 负责方 |
|------|--------|
| salesGuide | `/sales-guide` skill |
| requirements | `/requirements` skill |
| contacts | 平台 CRM / 用户手动录入 |
| tracking | 平台系统自动维护 |
| budget | 面访后由销售补充 |

### 写入策略

- **merge 写入**：保留 JSON 中已有的其他字段值，只更新 profile 负责的字段
- **首次写入**：如果文件不存在，创建完整结构，未填充字段使用类型默认值（空字符串/空数组/0）
- **更新写入**：读取现有文件，仅覆盖 profile 负责的字段，保留其他 skill 已写入的数据
