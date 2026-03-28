---
name: alphashop-sel-product-search
description: >-
  商品搜索SKILL：通过关键词搜索Amazon和TikTok平台的商品。
  触发场景：用户说"搜索商品"、"查找产品"、"商品市场调研"、"商品筛选"。
  使用流程：
  1) 每次触发上述场景，都必需确认参数：keyword（搜索关键词）、platform（amazon或tiktok）、region（国家代码如US/UK/ID等）
  2) 询问是否需要可选筛选条件：价格区间、评分范围、销量范围、上架时间（90/180/365天内新品）
  3) 如用户未明确指定平台或区域，应先询问用户选择后再调用。
  提供商品详情、机会分析、新品机会分、1688同款参考价等信息。
metadata:
  author: AlphaShop Team
  version: 1.0.0
  category: e-commerce
  tags: [product-search, market-research, selection, amazon, tiktok]
  openclaw:
    primaryEnv: none
    requires:
      env: []
---

## 安全声明

本Skill的安全边界和使用限制：

**安全保障**：
- ✅ 仅通过HTTPS调用AlphaShop公开API，所有数据传输加密
- ✅ API凭证通过环境变量管理，不硬编码在代码中
- ✅ 不处理用户个人身份信息（PII）
- ✅ 不访问生产数据库，仅调用只读API接口
- ✅ 不执行任何文件系统修改操作（除了可选的结果保存到指定文件）
- ✅ 使用JWT Token认证（HS256），支持token过期机制

**使用限制**：
- ⚠️ 搜索关键词由用户输入，建议避免敏感词汇（可能被风控系统拦截）
- ⚠️ API调用频率建议控制在合理范围，避免对服务造成压力
- ⚠️ TikTok平台的价格筛选功能当前不可用
- ⚠️ 商品数据存在1-2天延迟，仅供参考

**数据处理**：
- 本Skill仅查询和展示商品数据，不进行订单操作
- 不存储用户的搜索历史或个人偏好
- 可选的结果保存功能需要用户显式指定输出路径

## 配置

### 环境变量

需要配置 AlphaShop API 凭证。在 OpenClaw config 中设置：

```json5
{
  skills: {
    entries: {
      "alphashop-sel-product-search": {
        env: {
          ALPHASHOP_ACCESS_KEY: "你的AccessKey",
          ALPHASHOP_SECRET_KEY: "你的SecretKey"
        }
      }
    }
  }
}
```

### 如何获取 API Key

#### 获取途径

本 skill 使用 AlphaShop/遨虾平台的 API 服务，需要申请以下凭证：
- `ALPHASHOP_ACCESS_KEY` - API 访问密钥
- `ALPHASHOP_SECRET_KEY` - API 密钥

#### 申请步骤

1. **联系平台方**
   - 如果您是 1688 或阿里内部用户，请联系 AlphaShop/遨虾 平台管理员
   - 平台可能需要您提供：
     - 公司信息
     - 使用场景说明
     - 预期调用量

2. **获取凭证**
   - 平台审核通过后会提供：
     - Access Key（访问密钥）
     - Secret Key（密钥）

3. **配置到环境**
   - 按照上面的配置方式设置环境变量

# 商品搜索SKILL

通过关键词搜索 Amazon 和 TikTok 平台的商品，支持多维度筛选和机会分析，帮助发现优质选品机会。

## 快速开始

### 基础用法

搜索 Amazon 平台商品：

```bash
python3 scripts/search.py \
  --keyword "wireless mouse" \
  --platform "amazon" \
  --region "US" \
  --app-key "你的AccessKey" \
  --app-secret "你的SecretKey"
```

搜索 TikTok 平台商品：

```bash
python3 scripts/search.py \
  --keyword "bluetooth speaker" \
  --platform "tiktok" \
  --region "US" \
  --app-key "你的AccessKey" \
  --app-secret "你的SecretKey"
```

## 功能说明

本 Skill 封装了商品搜索 API，提供以下核心功能：

### 1. 关键词搜索
- **多平台支持**：Amazon 和 TikTok 两大主流电商平台
- **多国家支持**：覆盖全球主要市场
- **智能匹配**：基于关键词智能匹配相关商品

### 2. 多维度筛选
- **价格筛选**：支持最低价格和最高价格区间（⚠️ 仅Amazon平台可用，TikTok平台暂不支持）
- **评分筛选**：支持按商品评分过滤（0-5.0分）
- **销量筛选**：支持按30天销量过滤
- **上架时间**：支持筛选指定时间内上架的新品（90天/180天/365天）

### 3. 机会分析
- **机会分**：综合评估商品的市场机会，分数越高表示机会越好
- **新品机会分**：专门针对新品的机会评分
- **排名对比**：显示商品击败的同类商品百分比

### 4. 1688 同款参考
- **价格对比**：自动查询 1688 平台的同款商品参考价
- **利润空间**：帮助评估商品的利润空间

## 支持的平台和国家

### Amazon 平台
支持国家：`US`, `UK`, `ES`, `FR`, `DE`, `IT`, `CA`, `JP`

### TikTok 平台
支持国家：`ID`, `VN`, `MY`, `TH`, `PH`, `US`, `SG`, `BR`, `MX`, `GB`, `ES`, `FR`, `DE`, `IT`, `JP`

## 使用方法

### 参数说明

| 参数 | 类型 | 必填 | 说明 | 示例 |
|------|------|------|------|------|
| `--keyword` | String | ✅ | 搜索关键词 | `"wireless mouse"` |
| `--platform` | String | ✅ | 平台（`amazon` 或 `tiktok`） | `"amazon"` |
| `--region` | String | ✅ | 国家代码 | `"US"` |
| `--app-key` | String | ✅ | AlphaShop Access Key | - |
| `--app-secret` | String | ✅ | AlphaShop Secret Key | - |
| `--min-price` | Float | ❌ | 最低价格 | `10.0` |
| `--max-price` | Float | ❌ | 最高价格 | `100.0` |
| `--min-rating` | Float | ❌ | 最低评分（0-5.0） | `3.5` |
| `--max-rating` | Float | ❌ | 最高评分（0-5.0） | `5.0` |
| `--min-sales` | Integer | ❌ | 最低30天销量 | `100` |
| `--max-sales` | Integer | ❌ | 最高30天销量 | `10000` |
| `--listing-time` | Integer | ❌ | 上架时间范围（90/180/365，单位：天） | `90` |
| `--count` | Integer | ❌ | 返回商品数量（默认10） | `20` |
| `--user-id` | String | ❌ | 用户ID（可选） | `"123456"` |
| `--output` | String | ❌ | 输出文件路径 | `"result.json"` |

## 完整示例

### Amazon 平台示例

```bash
# 基础搜索
python3 scripts/search.py \
  --keyword "wireless mouse" \
  --platform "amazon" \
  --region "US" \
  --app-key "$ALPHASHOP_ACCESS_KEY" \
  --app-secret "$ALPHASHOP_SECRET_KEY"

# 带价格和评分筛选
python3 scripts/search.py \
  --keyword "bluetooth speaker" \
  --platform "amazon" \
  --region "US" \
  --min-price 20 \
  --max-price 100 \
  --min-rating 4.0 \
  --app-key "$ALPHASHOP_ACCESS_KEY" \
  --app-secret "$ALPHASHOP_SECRET_KEY"

# 搜索90天内的新品
python3 scripts/search.py \
  --keyword "gaming keyboard" \
  --platform "amazon" \
  --region "US" \
  --listing-time 90 \
  --min-rating 4.5 \
  --app-key "$ALPHASHOP_ACCESS_KEY" \
  --app-secret "$ALPHASHOP_SECRET_KEY"
```

### TikTok 平台示例

⚠️ **重要提示**：TikTok平台暂不支持价格筛选（`--min-price`/`--max-price`），使用价格参数会导致API报错。其他筛选条件（评分、销量、上架时间）正常可用。

```bash
# 搜索印尼市场商品（基础搜索）
python3 scripts/search.py \
  --keyword "phone case" \
  --platform "tiktok" \
  --region "ID" \
  --app-key "$ALPHASHOP_ACCESS_KEY" \
  --app-secret "$ALPHASHOP_SECRET_KEY"

# 搜索美国市场高评分商品（支持评分和销量筛选）
python3 scripts/search.py \
  --keyword "led lights" \
  --platform "tiktok" \
  --region "US" \
  --min-rating 4.5 \
  --min-sales 500 \
  --app-key "$ALPHASHOP_ACCESS_KEY" \
  --app-secret "$ALPHASHOP_SECRET_KEY"

# ❌ 错误示例：TikTok平台不支持价格筛选
# python3 scripts/search.py \
#   --keyword "yoga pants" \
#   --platform "tiktok" \
#   --region "ID" \
#   --min-price 10 --max-price 100  # 会报错！
```

### 使用环境变量

为了避免每次都输入 API 密钥，可以设置环境变量：

```bash
# 设置环境变量
export ALPHASHOP_ACCESS_KEY="你的AccessKey"
export ALPHASHOP_SECRET_KEY="你的SecretKey"

# 直接使用
python3 scripts/search.py \
  --keyword "wireless earbuds" \
  --platform "amazon" \
  --region "US" \
  --app-key "$ALPHASHOP_ACCESS_KEY" \
  --app-secret "$ALPHASHOP_SECRET_KEY"
```

### 保存结果到文件

```bash
python3 scripts/search.py \
  --keyword "smart watch" \
  --platform "amazon" \
  --region "US" \
  --output "results/smartwatch-us.json" \
  --app-key "$ALPHASHOP_ACCESS_KEY" \
  --app-secret "$ALPHASHOP_SECRET_KEY"
```

## 输出说明

### 商品信息字段

每个商品包含以下信息：
- **ID**: 商品在平台上的唯一标识
- **标题**: 商品标题
- **价格**: 商品价格
- **评分**: 商品评分和评价数量
- **30天销量**: 近30天的销售数量
- **30天销售额**: 近30天的销售金额（如有）
- **上架时间**: 商品上架日期
- **机会分**: 综合机会评分和排名百分比
- **新品机会分**: 新品专项机会评分和排名百分比
- **1688同款参考价**: 1688平台的同款商品参考价格

### 机会分说明

- **机会分**: 0-100分，综合评估商品的市场机会
  - 分数越高表示机会越好
  - 包含括号显示击败的同类商品百分比
  - 例如：`71.14 (击败 57.5% 商品)`

- **新品机会分**: 0-100分，专门针对新品的机会评分
  - 评估新品在市场中的竞争力
  - 同样包含排名百分比
  - 例如：`73.30 (击败 96.8% 商品)`

## API 参考文档

完整的 API 接口和数据结构文档请参阅 [references/api.md](references/api.md)。

## 注意事项

1. **API 凭证安全**
   - 请妥善保管 API 凭证，不要泄露给他人
   - 不要将凭证硬编码在代码中
   - 建议使用环境变量或配置文件管理

2. **请求频率**
   - 请合理控制请求频率，避免频繁调用
   - 建议添加适当的延迟和重试逻辑

3. **筛选条件**
   - 筛选条件过于严格可能导致返回结果较少
   - 建议先使用基础条件，再逐步添加筛选
   - 价格区间、评分区间要合理设置
   - ⚠️ **TikTok平台限制**：暂不支持价格筛选（`--min-price`/`--max-price`），使用会导致API报错（`FAIL_SERVER_INTERNAL_ERROR`）

4. **数据时效性**
   - 商品数据会定期更新，可能存在1-2天延迟
   - 销量和评分数据为近期统计，仅供参考

5. **平台差异**
   - 不同平台和国家的商品数据结构可能略有差异
   - 建议针对具体平台进行测试验证

## 已知问题

### TikTok平台价格筛选不可用

**问题描述**：
- TikTok平台使用价格筛选参数（`--min-price`/`--max-price`）时会返回 `FAIL_SERVER_INTERNAL_ERROR` 错误
- 该问题在所有TikTok支持的国家/地区都存在
- Amazon平台的价格筛选功能正常

**测试结果**：
```bash
# ❌ 失败：TikTok印尼市场 + 价格筛选
python3 scripts/search.py --keyword "yoga pants" --platform tiktok --region ID --min-price 10 --max-price 100
# 错误：ResultCode: FAIL_SERVER_INTERNAL_ERROR

# ✅ 成功：Amazon美国市场 + 价格筛选
python3 scripts/search.py --keyword "phone" --platform amazon --region US --min-price 50 --max-price 200
# 正常返回结果

# ✅ 成功：TikTok印尼市场 + 其他筛选条件
python3 scripts/search.py --keyword "yoga pants" --platform tiktok --region ID --min-rating 4.0 --min-sales 100
# 正常返回结果
```

**影响范围**：
- 仅影响TikTok平台的价格筛选功能
- 其他筛选条件（评分、销量、上架时间）不受影响
- Amazon平台功能完全正常

**临时方案**：
- TikTok平台暂时不使用价格筛选，使用其他筛选条件
- 或切换到Amazon平台使用完整的价格筛选功能


