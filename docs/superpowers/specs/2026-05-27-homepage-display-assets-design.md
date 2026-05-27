# 首页展示资源补齐设计

## 目标

为当前首页的现有七个展示区块准备一套可上传到 Shopify 后台的真实媒体资源，使本地主题所表达的首页结构能够复现 `https://bottlessupply.com/` 当前可见内容，同时保留主题编辑器的媒体可替换能力。

本设计采用已确认的 `B` 方案：补齐访客在首页能够看到的展示资源，不将全部图片和约 38 MB 视频绑定进主题代码仓库。

## 当前状态

当前首页模板 `templates/index.json` 已配置如下区块顺序：

1. `home-hero-section`
2. `home-product-info-section`
3. `home-materials-section`
4. `video-banner`
5. `home-endorsement-section`
6. `home-feature-cards-section`
7. `home-faq-section`

其中图片设置多为 `shopify://shop_images/...` 媒体引用，而 `video-banner` 当前视频设置为空。`downloaded-site/index.html` 并未把媒体二进制保存到本地，但保留了在线首页实际加载的 Shopify CDN 地址，可作为本次资源恢复来源。

## 交付形式

实施阶段产出独立的首页素材上传包与映射文档：

- 素材包写入仓库根目录下的 `homepage-display-assets/`，作为本地交付素材目录，并通过 `.gitignore` 排除二进制文件版本提交。
- 可提交的映射文档写入 `docs/homepage-display-assets-map.md`。
- 素材包包含从在线首页确认可用的图片和视频文件。
- 文件使用按区块分组且可识别用途的命名方式。
- 映射文档说明每个文件应在 Shopify Theme Editor 中配置到哪个区块和 block。
- 主题代码仅在存在空媒体展示缺陷且当前容错不足时做最小修正，不把 CDN 地址写死到生产模板。

素材包不放入主题 `assets/` 的原因：

- Shopify `image_picker` 和 `video` 设置最终需要当前店铺媒体库中的对象。
- 外部店铺 CDN 地址不是当前店铺稳定可管理的媒体配置。
- 视频文件体积较大，不适合作为默认主题代码资产进入版本历史。

## 素材范围

### Hero 轮播

保留在线首页当前可见的 3 张轮播展示图，命名为：

- `hero-slide-01.png`
- `hero-slide-02.png`
- `hero-slide-03.png`

映射目标为 `Home Hero` 的前三个 slide block。当前模板中多出的 slide 若未能在在线首页确认存在对应可见素材，应移除配置或留空，不使用臆造图片补位。

### 商品展示区

下载首页主商品区域实际出现的 15 张大图及 3 张容量选择对应展示图。文件名组织如下：

- `product-gallery-8oz-04.jpg`
- `product-gallery-8oz-01.jpg`
- `product-gallery-12oz-01-a.jpg`
- `product-gallery-16oz-01.jpg`
- `product-gallery-8oz-03.jpg`
- `product-gallery-16oz-03.jpg`
- `product-gallery-16oz-04.jpg`
- `product-gallery-12oz-03-c.jpg`
- `product-gallery-12oz-05-e.jpg`
- `product-gallery-16oz-05.jpg`
- `product-gallery-12oz-04-d.jpg`
- `product-gallery-8oz-06.jpg`
- `product-gallery-8oz-pack.jpg`
- `product-gallery-12oz-pack.jpg`
- `product-gallery-16oz-pack.jpg`
- `product-variant-8oz.jpg`
- `product-variant-12oz.jpg`
- `product-variant-16oz.jpg`

这些文件首先用于创建或补齐 Shopify 商品媒体与 variant 图片；首页 `Product Info Section` 继续通过已选商品或 `collections.all.products.first` 获取真实商品数据，不在 section 中复制一套静态商品图逻辑。

### 材料卖点区

下载当前三个材料卡片对应图片：

- `material-leak-proof.jpg`
- `material-use-cases.jpg`
- `material-top-choice.jpg`

映射到 `Home Materials` 的三个 material block，并保留现有标题与说明的对应关系。

### 视频展示区

下载在线首页当前展示的一个 MP4 视频，命名为：

- `homepage-product-showcase.mp4`

上传后配置到 `视频展示区` 的 `video` 设置。视频必须由 Shopify 媒体托管，不保留 CDN 外链作为模板默认值。

### 评价背书区

下载五位评价人物对应的图片：

- `endorsement-sarah-johnson.jpg`
- `endorsement-mike-chen.jpg`
- `endorsement-elizabeth.jpg`
- `endorsement-grace.jpg`
- `endorsement-victor.jpg`

映射到 `Home Endorsements` 中同名的五个 endorser block。若图片未上传，区块不得渲染失效图片 URL；当前工作区已有的空媒体容错修改应被保留并纳入验证。

### Amazon 商品卡

下载当前四张外部商品卡图片：

- `amazon-card-12pcs-8oz.png`
- `amazon-card-36pcs-4oz.jpg`
- `amazon-card-6pcs-2oz.png`
- `amazon-card-40pcs-12oz.jpg`

映射到 `Feature Cards Section` 中四个 feature card block，并维持既有 Amazon 链接设置。

## 资源来源与安全边界

- 来源以 `downloaded-site/index.html` 中记录的 CDN URL 为主，并以在线首页当前可见结果核对区块存在性。
- 只下载首页已经展示、与当前商品和品牌内容一致的媒体资源。
- 不下载站点脚本、追踪资源、第三方插件资源或与首页展示无关的页面素材。
- 不将外部 CDN 地址写入主题作为长期渲染依赖。
- `homepage-display-assets/` 仅用于交付和后台上传，不作为 Liquid 运行时文件路径。

## Shopify 配置流程

实施完成后，资源导入流程为：

1. 在 Shopify 后台将整理好的图片和视频上传到当前店铺媒体库。
2. 在主题编辑器中为 Hero、材料、视频、评价和商品卡区块选择对应媒体。
3. 在商品管理中补齐首页主商品媒体和容量 variant 图片，使商品区沿现有动态商品逻辑展示。
4. 保存首页模板设置，并在桌面端与移动端预览页面。

## 代码影响范围

本次不重排首页模块，不新建首页 section，不重构主题视觉样式。

允许的代码变更仅包括：

- 保留并验证当前工作区中 `home-materials-section` 与 `home-endorsement-section` 对空图片的容错处理。
- 如视频区在无配置状态下影响最终展示，实施时仅在资源已映射后恢复有效视频设置或记录后台配置步骤。
- 为资源包增加映射说明文档，不把媒体路径硬编码到 Liquid。

## 验收标准

- 素材包包含 Hero 3 张、商品相关可见图集和容量图、材料图 3 张、视频 1 个、评价图 5 张、Amazon 卡图 4 张。
- 商品相关资源固定覆盖 15 张图库大图与 3 张容量选择展示图。
- 素材包文件名可以无需打开文件即可判断用途和映射目标。
- `docs/homepage-display-assets-map.md` 覆盖当前首页所有需要媒体的区块，以及商品媒体与 variant 的后台配置关系。
- 下载后的资源可打开，图片为有效图片格式，视频可被识别为有效 MP4。
- Shopify 后台配置完成后：首页不出现缺图、空视频提示或失效图片 URL。
- 桌面端与移动端均检查 Hero、商品区、材料区、视频区、评价区及商品卡区展示。

## 非目标

- 不改变 FAQ、页脚或其他信息区内容。
- 不设计新的品牌视觉风格或替换页面文案。
- 不将目标站的应用脚本、营销追踪或外部集成功能复制进主题。
- 不把整个目标站离线镜像作为交付成果。
