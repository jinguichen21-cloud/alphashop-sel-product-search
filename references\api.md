# 商品搜索API - 参考文档

## 接口信息

- **接口名称**: `productSearchApi`
- **功能**: 通过关键词搜索 Amazon 和 TikTok 平台的商品，支持多维度筛选
- **调用方式**: HTTP POST
- **HTTP端点**: `POST https://api.alphashop.cn/ai.sel.global1688.productSearchApi/1.0`

## 核心能力

1. **关键词搜索**: 基于关键词智能匹配相关商品
2. **多平台支持**: Amazon 和 TikTok 两大主流电商平台
3. **多维度筛选**: 价格、评分、销量、上架时间等
4. **机会分析**: 提供商品机会分和新品机会分
5. **1688同款**: 自动查询1688同款商品参考价

## 认证方式

使用 JWT Token 进行认证。

### JWT Token 生成

```python
import jwt
import time

def generate_token(access_key: str, secret_key: str) -> str:
    """生成 JWT token"""
    current_time = int(time.time())
    expired_at = current_time + 1800  # 30分钟后过期
    not_before = current_time - 5

    token = jwt.encode(
        payload={
            "iss": access_key,
            "exp": expired_at,
            "nbf": not_before
        },
        key=secret_key,
        algorithm="HS256",
        headers={"alg": "HS256"}
    )

    return token
```

### 请求头

```
Authorization: Bearer {JWT_TOKEN}
Content-Type: application/json
```

## 请求参数

| 参数名 | 类型 | 必填 | 说明 | 示例值 |
|--------|------|------|------|--------|
| keyword | String | ✅ | 搜索关键词 | "wireless mouse" |
| platform | String | ✅ | 目标平台（amazon/tiktok） | "amazon" |
| region | String | ✅ | 目标国家/地区 | "US" |
| minPrice | Double | ❌ | 最低价格 | 10.0 |
| maxPrice | Double | ❌ | 最高价格 | 100.0 |
| minSales | Integer | ❌ | 最低30天销量 | 100 |
| maxSales | Integer | ❌ | 最高30天销量 | 10000 |
| minRating | Double | ❌ | 最低评分（0-5.0） | 3.5 |
| maxRating | Double | ❌ | 最高评分（0-5.0） | 5.0 |
| listingTime | String | ❌ | 商品上架时间范围（"90"/"180"/"365"，单位：天） | "90" |
| count | Integer | ❌ | 返回商品数量（默认10） | 20 |
| userId | String | ❌ | 用户ID（可选） | "123456" |

## 支持的平台和国家

### Amazon 平台
US, UK, ES, FR, DE, IT, CA, JP

### TikTok 平台
ID, VN, MY, TH, PH, US, SG, BR, MX, GB, ES, FR, DE, IT, JP

## 请求示例

### 基础搜索

```bash
curl -X POST "https://api.alphashop.cn/ai.sel.global1688.productSearchApi/1.0" \
  -H "Authorization: Bearer {JWT_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "keyword": "wireless mouse",
    "platform": "amazon",
    "region": "US",
    "userId": "123456"
  }'
```

### 带筛选条件的搜索

```bash
curl -X POST "https://api.alphashop.cn/ai.sel.global1688.productSearchApi/1.0" \
  -H "Authorization: Bearer {JWT_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "keyword": "bluetooth speaker",
    "platform": "amazon",
    "region": "US",
    "minPrice": 20.0,
    "maxPrice": 100.0,
    "minRating": 4.0,
    "listingTime": "90",
    "count": 20,
    "userId": "123456"
  }'
```

## 响应结构

### 成功响应

```json
{
  "api": "ai.sel.global1688.productSearchApi",
  "version": "1.0",
  "resultCode": "SUCCESS",
  "requestId": "213e04de17739297932113058e10c3",
  "result": {
    "products": [
      {
        "productId": "B0GLGFYN93",
        "title": "Wireless Mouse, 2.4GHz Computer Mice...",
        "productUrl": "https://www.amazon.com/dp/B0GLGFYN93",
        "platform": "amazon",
        "region": "US",
        "price": "US$7.99",
        "priceValue": 7.99,
        "currency": "USD",
        "rating": 4.3,
        "reviewCount": 9,
        "soldCount30d": 0,
        "salesAmount30d": 0.0,
        "onShelfDate": "2025-12-30",
        "onShelfDays": 80,
        "categoryPath": "Electronics > Computers & Accessories",
        "opportunityScore": 56.84,
        "opportunityRank": 9.1,
        "newProductScore": 67.51,
        "newProductRank": 92.2,
        "referencePrice1688": "US$2.26",
        "imageUrl": "https://m.media-amazon.com/images/I/xxx.jpg"
      }
    ],
    "totalCount": 10,
    "returnCount": 10
  }
}
```

### 字段说明

#### 商品信息 (product)

| 字段 | 类型 | 说明 |
|------|------|------|
| productId | String | 商品ID |
| title | String | 商品标题 |
| productUrl | String | 商品链接 |
| platform | String | 平台（amazon/tiktok） |
| region | String | 国家/地区代码 |
| price | String | 价格（带货币符号） |
| priceValue | Double | 价格数值 |
| currency | String | 货币代码 |
| rating | Double | 评分（0-5.0） |
| reviewCount | Integer | 评价数量 |
| soldCount30d | Integer | 近30天销量 |
| salesAmount30d | Double | 近30天销售额 |
| onShelfDate | String | 上架日期 |
| onShelfDays | Integer | 上架天数 |
| categoryPath | String | 类目路径 |
| opportunityScore | Double | 机会分（0-100） |
| opportunityRank | Double | 机会排名百分比 |
| newProductScore | Double | 新品机会分（0-100） |
| newProductRank | Double | 新品机会排名百分比 |
| referencePrice1688 | String | 1688同款参考价 |
| imageUrl | String | 商品主图链接 |

### 错误响应

```json
{
  "api": "ai.sel.global1688.productSearchApi",
  "version": "1.0",
  "resultCode": "INVALID_PARAMETER",
  "requestId": "213e04de17739297932113058e10c3",
  "result": null,
  "errorMessage": "参数错误: 关键词不能为空"
}
```

## 错误码说明

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| INVALID_PARAMETER | 参数错误 | 检查必填参数是否完整 |
| INVALID_PLATFORM | 平台不合法 | 只能是 amazon 或 tiktok |
| INVALID_REGION | 国家不合法 | 检查国家代码是否在支持列表 |
| INVALID_PRICE_RANGE | 价格区间错误 | 检查最低价格是否小于最高价格 |
| INVALID_RATING_RANGE | 评分区间错误 | 评分必须在 0-5.0 之间 |
| INVALID_SALES_RANGE | 销量区间错误 | 检查最低销量是否小于最高销量 |
| INVALID_LISTING_TIME | 上架时间参数错误 | 只能是 "90"、"180" 或 "365" |
| AUTHENTICATION_FAILED | 认证失败 | 检查 JWT token 是否正确 |
| RATE_LIMIT_EXCEEDED | 请求频率超限 | 降低请求频率 |
| INTERNAL_ERROR | 内部错误 | 联系技术支持 |

## 使用注意事项

1. **关键词建议**:
   - 使用英文关键词效果更好
   - 关键词应该清晰明确，避免过于宽泛
   - 可以包含品牌名、产品类型、特性等

2. **筛选条件建议**:
   - 首次搜索建议只使用关键词和平台/国家
   - 如果结果太多，再逐步添加筛选条件
   - 价格区间要合理，避免过于严格
   - 评分筛选建议设置 minRating >= 3.5

3. **上架时间说明**:
   - `listingTime` 用于筛选新品
   - "90" 表示90天内上架的商品
   - "180" 表示180天内上架的商品
   - "365" 表示365天内上架的商品

4. **机会分说明**:
   - **opportunityScore**: 综合机会分，考虑价格、销量、评分等多个维度
   - **opportunityRank**: 该商品在同类商品中的排名百分比，数值越大表示排名越高
   - **newProductScore**: 新品专项机会分，专门针对新品的评估
   - **newProductRank**: 新品在同类新品中的排名百分比

5. **1688同款参考价**:
   - 自动查询1688平台的同款商品
   - 用于评估利润空间
   - 可能存在查询失败的情况（显示为空或 N/A）

6. **性能建议**:
   - 建议 `count` 设置在 10-20 之间
   - 请求超时时间建议设置为 30-60 秒
   - 避免频繁请求，建议添加请求间隔

7. **数据时效性**:
   - 商品数据会定期更新
   - 价格、销量、评分等数据可能存在 1-2 天延迟
   - 上架时间基于平台数据，可能略有偏差

## Python 示例代码

```python
#!/usr/bin/env python3
import requests
import jwt
import time
import json

def generate_jwt_token(access_key: str, secret_key: str) -> str:
    """生成 JWT token"""
    current_time = int(time.time())
    expired_at = current_time + 1800  # 30分钟后过期
    not_before = current_time - 5

    token = jwt.encode(
        payload={
            "iss": access_key,
            "exp": expired_at,
            "nbf": not_before
        },
        key=secret_key,
        algorithm="HS256",
        headers={"alg": "HS256"}
    )

    return token

def search_products(
    keyword: str,
    platform: str,
    region: str,
    access_key: str,
    secret_key: str,
    **kwargs
):
    """搜索商品"""
    # 生成 JWT token
    token = generate_jwt_token(access_key, secret_key)

    # 构建请求参数
    payload = {
        "keyword": keyword,
        "platform": platform,
        "region": region,
        "userId": kwargs.get("user_id", "123456")
    }

    # 添加可选参数
    if "min_price" in kwargs:
        payload["minPrice"] = kwargs["min_price"]
    if "max_price" in kwargs:
        payload["maxPrice"] = kwargs["max_price"]
    if "min_rating" in kwargs:
        payload["minRating"] = kwargs["min_rating"]
    if "max_rating" in kwargs:
        payload["maxRating"] = kwargs["max_rating"]
    if "min_sales" in kwargs:
        payload["minSales"] = kwargs["min_sales"]
    if "max_sales" in kwargs:
        payload["maxSales"] = kwargs["max_sales"]
    if "listing_time" in kwargs:
        payload["listingTime"] = str(kwargs["listing_time"])
    if "count" in kwargs:
        payload["count"] = kwargs["count"]

    # 发送请求
    response = requests.post(
        "https://api.alphashop.cn/ai.sel.global1688.productSearchApi/1.0",
        json=payload,
        headers={
            "Content-Type": "application/json",
            "Authorization": f"Bearer {token}"
        },
        timeout=60
    )

    response.raise_for_status()
    return response.json()

# 使用示例
if __name__ == "__main__":
    ACCESS_KEY = "your_access_key"
    SECRET_KEY = "your_secret_key"

    result = search_products(
        keyword="wireless mouse",
        platform="amazon",
        region="US",
        access_key=ACCESS_KEY,
        secret_key=SECRET_KEY,
        min_price=10.0,
        max_price=50.0,
        min_rating=4.0,
        listing_time=90,
        count=20
    )

    print(json.dumps(result, indent=2, ensure_ascii=False))
```

## 常见问题

### Q1: 搜索结果为空怎么办？
A: 可能的原因：
- 关键词过于生僻或拼写错误
- 筛选条件过于严格
- 平台该国家确实没有相关商品

建议：
- 简化关键词，使用更通用的词汇
- 放宽筛选条件或去掉部分筛选
- 尝试其他国家或平台

### Q2: 如何提高搜索精准度？
A: 建议：
- 使用更具体的关键词，包含品牌、型号等
- 合理使用价格区间筛选
- 结合评分和销量筛选高质量商品
- 关注机会分高的商品

### Q3: 1688同款参考价为什么有时候显示不出来？
A: 可能的原因：
- 1688平台没有完全相同的商品
- 图像识别匹配失败
- API 暂时不可用

这个字段仅供参考，不影响主要搜索功能。

### Q4: 机会分是如何计算的？
A: 机会分综合考虑多个维度：
- 价格竞争力
- 销量表现
- 评分和评价数量
- 上架时间
- 市场竞争度

分数越高表示商品的市场机会越好。

### Q5: JWT token 过期了怎么办？
A: JWT token 有效期为 30 分钟。如果过期，需要重新生成 token。建议在每次请求前都生成新的 token，或者实现 token 缓存和自动刷新机制。
