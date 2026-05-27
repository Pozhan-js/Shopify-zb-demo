# Shopify Test Data Contract

这个主题仓库对应的后台测试数据，至少需要覆盖商品、页面和页面路由三类对象。下面的合同是直接从当前主题代码提炼出来的，目的是避免在 Shopify Admin 里把 `handle`、模板后缀或页面用途建错。

## 页面与路由

需要保证以下页面存在，并且 `handle` 与模板匹配：

| 用途 | 页面 handle | 访问路由 | 模板 |
| --- | --- | --- | --- |
| 隐私政策 | `privacy-policy` | `/pages/privacy-policy` | `page.privacy-policy` |
| 联系我们 | `contact-us` | `/pages/contact-us` | 默认 `page` 模板 |
| About Us 展示页 | `terms-of-service` | `/pages/terms-of-service` | `page.terms-of-servicew` |

说明：

- `sections/footer-group.json` 里把 footer 链接写死成了 `shopify://pages/privacy-policy`、`shopify://pages/terms-of-service`、`shopify://pages/contact-us`。
- `templates/page.terms-of-servicew.json` 实际承载的是 `about-us` section，所以这个仓库当前是“`terms-of-service` 路由承载 About Us 页面内容”的状态。
- `templates/page.contact.json` 也存在，但当前主题主链路没有引用它；真正被 footer 和 `about-us` 文案引用的是 `/pages/contact-us`。

## 商品测试集

首页 `home-product-info-section` 在未指定 `selectedproduct` 时，会回退到 `collections.all.products.first`。因此后台至少要有 1 个可在线商店访问的有效商品，否则首页核心商品区会拿不到数据。

建议最小测试集为 4 个商品：

| 标题 | 建议 handle | 用途 |
| --- | --- | --- |
| `ZB Test Bottle 500ml` | `zb-test-bottle-500ml` | 首页商品区默认命中商品 |
| `ZB Test Bottle 1000ml` | `zb-test-bottle-1000ml` | 商品列表与集合页第二个样本 |
| `ZB Test Bottle Wide Mouth` | `zb-test-bottle-wide-mouth` | 商品页变体、图集、推荐位样本 |
| `ZB Test Bottle Square` | `zb-test-bottle-square` | 集合页和搜索页样本 |

每个商品建议满足：

- `status = ACTIVE`
- 已发布到 `Online Store`
- 至少 1 张商品主图
- 至少 2 个 variant，保证 `main-product` 和首页商品区的 variant/sku 选择逻辑可见
- variant title 可直接用容量或规格，如 `Single` / `2 Pack`，或者 `500ml` / `1000ml`

推荐给首个默认商品额外补齐：

- 3 个 variant
- 每个 variant 都有价格
- 至少 1 个 variant 绑定独立图片
- 商品描述非空

## 验证路径

后台创建完成后，前台至少验证以下路由：

- `/products/zb-test-bottle-500ml`
- `/collections/all`
- `/pages/privacy-policy`
- `/pages/contact-us`
- `/pages/terms-of-service`

如果本地 `shopify theme dev` 预览没有同步出后台变化，先核对四件事：

1. 当前 CLI 连接的是不是同一个店铺。
2. 商品是否为 `ACTIVE`。
3. 商品是否已上架到 `Online Store`。
4. 访问的是否是正确的 `handle` 路由。

## 代码依据

- `sections/home-product-info-section.liquid`
- `sections/footer-group.json`
- `templates/page.json`
- `templates/page.privacy-policy.json`
- `templates/page.terms-of-servicew.json`
