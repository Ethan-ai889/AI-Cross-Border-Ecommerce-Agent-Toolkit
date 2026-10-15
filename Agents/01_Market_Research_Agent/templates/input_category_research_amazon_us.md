# 输入模板 · 类目调研（Amazon 美国站）

> **改编自** `input_category_research.md`（Shopee 巴西站版）。三处关键改动：
> 1. 数据源：虾多拉 → Helium 10 / Jungle Scout 导出，或手工采集 Amazon 关键词下 Top 50–100 listing；
>    `scripts/shaduola_parser.py` 是虾多拉格式专用，新数据源需改写解析脚本，`market_metrics.py` 指标计算通用、可直接用。
> 2. 平台参数：佣金 0.14 → 0.15（Amazon 大部分类目推荐佣金 15%，以实际类目为准），货币 R$ → $，认证示例换成美国站常见项。
> 3. 竞品结构新增 `fulfillment`（FBA/FBM）与 Amazon 自营/跟卖信息。
>
> **前置步骤**：先跑脚本产出 `metrics_report` 再填表。脚本跑不通 / 数据进不来时，**不要手工估算指标**。

---

## 0. 任务配置

```yaml
platform*: amazon
target_market*: 美国
category*: ""                  # 类目名（Amazon 前台类目路径，如 Home & Kitchen > ...）
sample_size: 0                 # 样本商品数（从 metrics_report 读）
data_date: ""                  # 数据日期 YYYY-MM-DD
data_source: ""                # Helium 10 / Jungle Scout / 手工采集
keyword*: ""                   # 采集所用的核心关键词
```

---

## 1. 指标报告 ★ 阻塞项

> 由 `market_metrics.py` 产出。**直接把 `.md` 内容粘贴过来，或给 `.json` 路径。**

```yaml
metrics_report_source: ""      # 文件路径，或标注"见下方粘贴内容"
metrics_json_path: ""
```

```text
（在此粘贴 metrics.md 的完整内容，或说明 JSON 路径）
```

### 可选：多期数据（用于判断趋势）

单期快照无法判断增长/下滑，需要 2 期以上：

| 期次 | 数据日期 | 月总销量 | 月总销售额 | 备注 |
|---|---|---|---|---|
| 1 | | | | |
| 2 | | | | |

---

## 2. 公司资源能力 ★ 阻塞项（前置判断必需）

> 缺这一块，前置判断无法执行 —— Agent 不允许替公司假设能力。

```yaml
company_capabilities:
  has_stock_or_incoming: false        # 是否有现货或即将到货
  has_stable_supplier: false          # 是否有稳定供应商
  has_cost_advantage: false          # 成本是否有优势
  fits_target_market: false          # 是否适合美国市场
  fits_warehouse_logistics: false    # 体积重量是否适合当前物流（FBM 小包 / FBA）
  can_handle_after_sales: false      # 是否有能力处理售后/退换货
  after_sales_notes: ""              # 补充说明（如"只能换货，不做维修"）
```

### 产品画像（用于风险判定）

```yaml
product_profile:
  weight_kg: 0
  volume_cm: ""                       # 如 "30 × 20 × 15"
  is_fragile: false                   # 是否易碎
  needs_certification: []             # 如 ["FCC", "UL", "FDA"]，按类目填写
  contains_battery: false
  is_liquid_or_magnet: false
  is_oversize_for_fbm: false          # 是否超出 FBM 小包经济尺寸
  high_virtual_spec_risk: false       # 是否容易参数虚标
  ip_risk: ""                         # 知识产权/品牌侵权风险描述
```

---

## 3. 成本结构（价格带判断"有没有利润"必需）

```yaml
cost_structure:
  unit_cost: 0.00                      # 出厂/采购单价（$）
  shipping_to_us: 0.00                 # 头程到美国单件分摊（$）
  fulfillment: "FBM"                   # FBM / FBA
  fba_fee_per_unit: 0.00               # 若走 FBA，预估配送费（$）
  referral_fee_rate: 0.15              # Amazon 推荐佣金，大部分类目 15%
  return_rate: 0.02
  ad_cost_per_unit: 0.00               # 预估单件广告花费（$）
  after_sales_cost_per_unit: 0.00     # 高风险类目需填
target_margin_rate: 0.30              # 小件目标 25–30%，大件 30–35%
```

---

## 4. 竞品数据（取 2–3 个直接竞品）

> 筛选标准：价格区间接近 / 配置接近 / 目标用户接近 / 销量较高 / 评价较多 / 近期仍有销量 / **与我方计划做的产品存在直接竞争关系**。

```yaml
competitors:
  - label: "A"                        # ⚠️ 用代号，不要填真实店铺名/ASIN
    price: 0.00
    monthly_sales: 0                  # Helium 10 / Jungle Scout 预估
    monthly_revenue: 0.00
    sales_growth_rate: 0.00
    listing_age_days: 0
    rating: 0.0
    review_count: 0
    review_with_image_count: 0
    fulfillment: ""                   # FBA / FBM
    sold_by_amazon: false             # 是否 Amazon 自营在售
    hijacker_count: 0                 # 跟卖卖家数量（0 为独家销售）
    buy_box_winner: ""                # 当时 Buy Box 归属：品牌方/第三方/自营
    core_config: ""                   # 核心配置
    title: ""
    bullet_points_notes: ""           # 五点描述特点
    main_image_points: []             # 主图打出的卖点
    in_ad_slot: false                 # 是否出现在广告位
  - label: "B"
    # ... 同结构
```

### 竞品评论（可选，浅层即可）

```yaml
competitor_reviews:
  - competitor: "A"
    text: ""                          # 保留英文原文
    rating: 0
```

> **深度评论归因交给 07 Review Analysis。** 这里只填"竞品靠什么卖"够用的量级。

---

## 5. 配置维度（用于脚本抽取）

```yaml
config_dimensions: []
# 例：["容量", "颜色", "套装数量", "材质"]
# 已在脚本里用 --config-fields 指定过，这里仅作记录
```

---

## 填写自检

- [ ] `metrics_report` 来自脚本，不是手工估算
- [ ] `company_capabilities` 六项都填了（含 `after_sales_notes`）
- [ ] `product_profile` 填了体积重量与认证需求
- [ ] 高风险类目填了 `after_sales_cost_per_unit`
- [ ] 竞品用代号（A/B/C），未填真实店铺名/ASIN
- [ ] 评论保留英文原文，未翻译
- [ ] `referral_fee_rate` 已按实际类目核对（非 15% 类目需修改）
- [ ] 若只有单期数据，已准备接受"无法判断趋势"的结论
- [ ] 我理解：**最终选品决策由我做**，Agent 只输出分析与依据
