# Homepage Display Assets Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a reproducible homepage media delivery workflow that downloads the confirmed Bottles Supply homepage assets, stores them in a local upload bundle, and documents exactly how to map each asset into this Shopify theme.

**Architecture:** Keep homepage media out of runtime Liquid and out of git history. A small shell script will download the approved CDN files into `homepage-display-assets/`, a checked-in manifest will define filenames and sources, and a checked-in mapping doc will tell the operator where each file belongs in Shopify Admin and Theme Editor.

**Tech Stack:** Shopify theme repo, shell script, `curl`, `file`, markdown docs, existing Liquid sections

---

### Task 1: Prepare The Asset Delivery Structure

**Files:**
- Modify: `.gitignore`
- Create: `homepage-display-assets/.gitkeep`
- Create: `homepage-display-assets/README.md`
- Create: `docs/homepage-display-assets-manifest.md`

- [ ] **Step 1: Add the bundle directory to `.gitignore` while keeping docs tracked**

Patch `.gitignore` to ignore downloaded binaries without hiding checked-in documentation:

```diff
+homepage-display-assets/*
+!homepage-display-assets/.gitkeep
+!homepage-display-assets/README.md
```

- [ ] **Step 2: Create the asset bundle placeholder**

Create an empty tracked placeholder file:

```text
homepage-display-assets/.gitkeep
```

- [ ] **Step 3: Create the local bundle README**

Write `homepage-display-assets/README.md` with this content:

```md
# Homepage Display Assets

This directory holds the local upload bundle for homepage media recovered from the confirmed Bottles Supply homepage.

Contents are intentionally gitignored except for this README and `.gitkeep`.

Use `scripts/download-homepage-assets.sh` to populate the files before uploading them into Shopify Admin.
```

- [ ] **Step 4: Create the checked-in source manifest**

Write `docs/homepage-display-assets-manifest.md` with a table covering every approved asset:

```md
# Homepage Display Assets Manifest

| Filename | Source URL Type | Homepage Area |
| --- | --- | --- |
| `hero-slide-01.png` | Shopify CDN image | Hero |
| `hero-slide-02.png` | Shopify CDN image | Hero |
| `hero-slide-03.png` | Shopify CDN image | Hero |
| `product-gallery-8oz-04.jpg` | Shopify CDN image | Product gallery |
| `product-gallery-8oz-01.jpg` | Shopify CDN image | Product gallery |
| `product-gallery-12oz-01-a.jpg` | Shopify CDN image | Product gallery |
| `product-gallery-16oz-01.jpg` | Shopify CDN image | Product gallery |
| `product-gallery-8oz-03.jpg` | Shopify CDN image | Product gallery |
| `product-gallery-16oz-03.jpg` | Shopify CDN image | Product gallery |
| `product-gallery-16oz-04.jpg` | Shopify CDN image | Product gallery |
| `product-gallery-12oz-03-c.jpg` | Shopify CDN image | Product gallery |
| `product-gallery-12oz-05-e.jpg` | Shopify CDN image | Product gallery |
| `product-gallery-16oz-05.jpg` | Shopify CDN image | Product gallery |
| `product-gallery-12oz-04-d.jpg` | Shopify CDN image | Product gallery |
| `product-gallery-8oz-06.jpg` | Shopify CDN image | Product gallery |
| `product-gallery-8oz-pack.jpg` | Shopify CDN image | Product gallery |
| `product-gallery-12oz-pack.jpg` | Shopify CDN image | Product gallery |
| `product-gallery-16oz-pack.jpg` | Shopify CDN image | Product gallery |
| `product-variant-8oz.jpg` | Shopify CDN image | Product variant selector |
| `product-variant-12oz.jpg` | Shopify CDN image | Product variant selector |
| `product-variant-16oz.jpg` | Shopify CDN image | Product variant selector |
| `material-leak-proof.jpg` | Shopify CDN image | Materials |
| `material-use-cases.jpg` | Shopify CDN image | Materials |
| `material-top-choice.jpg` | Shopify CDN image | Materials |
| `homepage-product-showcase.mp4` | Shopify CDN video | Video banner |
| `endorsement-sarah-johnson.jpg` | Shopify CDN image | Endorsements |
| `endorsement-mike-chen.jpg` | Shopify CDN image | Endorsements |
| `endorsement-elizabeth.jpg` | Shopify CDN image | Endorsements |
| `endorsement-grace.jpg` | Shopify CDN image | Endorsements |
| `endorsement-victor.jpg` | Shopify CDN image | Endorsements |
| `amazon-card-12pcs-8oz.png` | Shopify CDN image | Feature cards |
| `amazon-card-36pcs-4oz.jpg` | Shopify CDN image | Feature cards |
| `amazon-card-6pcs-2oz.png` | Shopify CDN image | Feature cards |
| `amazon-card-40pcs-12oz.jpg` | Shopify CDN image | Feature cards |
```

- [ ] **Step 5: Verify the structure exists**

Run:

```bash
test -f homepage-display-assets/.gitkeep
test -f homepage-display-assets/README.md
test -f docs/homepage-display-assets-manifest.md
rg -n "homepage-display-assets" .gitignore homepage-display-assets/README.md docs/homepage-display-assets-manifest.md
```

Expected: all three files exist and `rg` returns matches from the ignore file and docs.

- [ ] **Step 6: Commit**

Run:

```bash
git add .gitignore homepage-display-assets/.gitkeep homepage-display-assets/README.md docs/homepage-display-assets-manifest.md
git commit -m "chore: scaffold homepage asset delivery bundle"
```

### Task 2: Add A Reproducible Download Script

**Files:**
- Create: `scripts/download-homepage-assets.sh`
- Test: `docs/homepage-display-assets-manifest.md`

- [ ] **Step 1: Create the download script with an explicit asset list**

Write `scripts/download-homepage-assets.sh`:

```bash
#!/usr/bin/env bash
set -euo pipefail

ROOT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")/.." && pwd)"
OUT_DIR="$ROOT_DIR/homepage-display-assets"

mkdir -p "$OUT_DIR"

download() {
  local filename="$1"
  local url="$2"
  echo "Downloading $filename"
  curl -L --fail --silent --show-error "$url" -o "$OUT_DIR/$filename"
}

download "hero-slide-01.png" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/6716a46c60614ecf54996e9978660efd.png?v=1776748156&width=1920"
download "hero-slide-02.png" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/d64acadc9bd4d6b516da1b643e472206.png?v=1776749265&width=1920"
download "hero-slide-03.png" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/3028134ca0274b4b1ef4100609be084f.png?v=1776752828&width=1920"
download "product-variant-8oz.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/8oz_592dd354-8e31-44a3-99e4-d27c4baa0559.jpg?v=1776862322&width=800"
download "product-variant-12oz.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/12oz.jpg?v=1776862322&width=800"
download "product-variant-16oz.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/16oz.jpg?v=1776862322&width=800"
download "product-gallery-8oz-04.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/8oz_4.jpg?v=1776246704&width=800"
download "product-gallery-8oz-01.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/8oz_1.jpg?v=1776247528&width=800"
download "product-gallery-12oz-01-a.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/12oz_1_A.jpg?v=1776247528&width=800"
download "product-gallery-16oz-01.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/16oz_1.jpg?v=1776247528&width=800"
download "product-gallery-8oz-03.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/8oz_3.jpg?v=1776247528&width=800"
download "product-gallery-16oz-03.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/16oz_3.jpg?v=1776247528&width=800"
download "product-gallery-16oz-04.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/16oz_4.jpg?v=1776247528&width=800"
download "product-gallery-12oz-03-c.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/12oz_3_C.jpg?v=1776247528&width=800"
download "product-gallery-12oz-05-e.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/12oz_5_E.jpg?v=1776247528&width=800"
download "product-gallery-16oz-05.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/16oz_5.jpg?v=1776247528&width=800"
download "product-gallery-12oz-04-d.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/12oz_4_D.jpg?v=1776247528&width=800"
download "product-gallery-8oz-06.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/8oz_6.jpg?v=1776247528&width=800"
download "product-gallery-8oz-pack.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/8oz_592dd354-8e31-44a3-99e4-d27c4baa0559.jpg?v=1776862322&width=800"
download "product-gallery-12oz-pack.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/12oz.jpg?v=1776862322&width=800"
download "product-gallery-16oz-pack.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/16oz.jpg?v=1776862322&width=800"
download "material-leak-proof.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/b1251a8aa4d0f1fa065ffb1059d046f0.jpg?v=1759829546&width=600"
download "material-use-cases.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/3_e98868dd-920b-47f4-be60-6f5ac0294436.jpg?v=1759829547&width=600"
download "material-top-choice.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/1_447d2f6f-d46a-4ce9-9958-1546b7a2273a.jpg?v=1759829546&width=600"
download "homepage-product-showcase.mp4" "https://cdn.shopify.com/videos/c/vp/f54da08e2b6e4aaa8cc1b01707c8fcb6/f54da08e2b6e4aaa8cc1b01707c8fcb6.HD-1080p-7.2Mbps-59283460.mp4"
download "endorsement-sarah-johnson.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/1_46e9b6a4-e6b9-428d-84cd-9269ef453976.jpg?v=1759830076&width=600"
download "endorsement-mike-chen.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/2_39e2bdd8-eff6-49ec-b3ff-53d72a53c78c.jpg?v=1759830076&width=600"
download "endorsement-elizabeth.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/3_0d05cff7-349e-4ed2-98a4-da8b592c0968.jpg?v=1759830075&width=600"
download "endorsement-grace.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/2025.4.3-_2.jpg?v=1758093262&width=600"
download "endorsement-victor.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/5.jpg?v=1759830075&width=600"
download "amazon-card-12pcs-8oz.png" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/external-link-1_400x400.png?v=1758597993"
download "amazon-card-36pcs-4oz.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/external-link-3_png_400x400.jpg?v=1758597993"
download "amazon-card-6pcs-2oz.png" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/external-link-2_400x400.png?v=1758597993"
download "amazon-card-40pcs-12oz.jpg" "https://cdn.shopify.com/s/files/1/0679/1603/8214/files/external-link-4_png_400x400.jpg?v=1758597993"
```

- [ ] **Step 2: Make the script executable**

Run:

```bash
chmod +x scripts/download-homepage-assets.sh
```

Expected: the file is executable and ready to run.

- [ ] **Step 3: Verify the script lists the full bundle**

Run:

```bash
rg -c '^download ' scripts/download-homepage-assets.sh
```

Expected: output `34`, matching the approved asset count.

- [ ] **Step 4: Commit**

Run:

```bash
git add scripts/download-homepage-assets.sh
git commit -m "chore: add homepage asset download script"
```

### Task 3: Download And Validate The Homepage Asset Bundle

**Files:**
- Modify: `homepage-display-assets/*` (generated, gitignored)
- Test: `scripts/download-homepage-assets.sh`

- [ ] **Step 1: Run the download script**

Run:

```bash
./scripts/download-homepage-assets.sh
```

Expected: each asset prints `Downloading ...` and files appear in `homepage-display-assets/`.

- [ ] **Step 2: Validate file count**

Run:

```bash
find homepage-display-assets -maxdepth 1 -type f ! -name '.gitkeep' ! -name 'README.md' | wc -l
```

Expected: output `34`.

- [ ] **Step 3: Validate image and video MIME types**

Run:

```bash
file homepage-display-assets/hero-slide-01.png
file homepage-display-assets/product-gallery-8oz-04.jpg
file homepage-display-assets/homepage-product-showcase.mp4
```

Expected: PNG, JPEG image data, and MP4/ISO media output respectively.

- [ ] **Step 4: Spot-check download integrity**

Run:

```bash
test -s homepage-display-assets/homepage-product-showcase.mp4
test -s homepage-display-assets/endorsement-sarah-johnson.jpg
test -s homepage-display-assets/amazon-card-12pcs-8oz.png
```

Expected: all checks succeed with zero exit status.

- [ ] **Step 5: Commit only tracked changes if any docs changed during validation**

Run:

```bash
git status --short
```

Expected: generated binaries remain untracked because of `.gitignore`; there should be nothing new to commit from the asset bundle itself.

### Task 4: Write The Shopify Mapping Guide

**Files:**
- Create: `docs/homepage-display-assets-map.md`
- Reference: `templates/index.json`
- Reference: `sections/home-product-info-section.liquid`

- [ ] **Step 1: Create the mapping guide**

Write `docs/homepage-display-assets-map.md`:

```md
# Homepage Display Assets Map

## Home Hero

Upload and assign:

| Block | File |
| --- | --- |
| Slide 1 | `hero-slide-01.png` |
| Slide 2 | `hero-slide-02.png` |
| Slide 3 | `hero-slide-03.png` |

Leave additional unused slide blocks empty or remove them from the editor if they are not needed.

## Product Info Section

Upload the 18 product files into the product media gallery of the homepage default product.

Variant media mapping:

| Variant label | File |
| --- | --- |
| 8oz | `product-variant-8oz.jpg` |
| 12oz | `product-variant-12oz.jpg` |
| 16oz | `product-variant-16oz.jpg` |

Gallery media should include all `product-gallery-*` files so the section can continue using the live product object.

## Home Materials

| Material block | File |
| --- | --- |
| Leak proof | `material-leak-proof.jpg` |
| Perfect for your business and homemake | `material-use-cases.jpg` |
| Top Choice | `material-top-choice.jpg` |

## 视频展示区

Assign `homepage-product-showcase.mp4` to the section `video` setting.

## Home Endorsements

| Endorser | File |
| --- | --- |
| Sarah Johnson | `endorsement-sarah-johnson.jpg` |
| Mike Chen | `endorsement-mike-chen.jpg` |
| Elizabeth | `endorsement-elizabeth.jpg` |
| Grace | `endorsement-grace.jpg` |
| Victor | `endorsement-victor.jpg` |

## Feature Cards Section

| Card title | File |
| --- | --- |
| 12pcs 8oz Plastic Juice Bottles with Caps | `amazon-card-12pcs-8oz.png` |
| 36pcs 4oz Juice Bottles, Small Plastic Bottles with Caps | `amazon-card-36pcs-4oz.jpg` |
| 6pcs 2oz Plastic Shots Bottles with Caps | `amazon-card-6pcs-2oz.png` |
| 40pcs 12oz Juice Bottles, Empty Plastic Juicing Bottles with Caps | `amazon-card-40pcs-12oz.jpg` |
```

- [ ] **Step 2: Verify the guide covers all editor-managed sections**

Run:

```bash
rg -n "Home Hero|Product Info Section|Home Materials|视频展示区|Home Endorsements|Feature Cards Section" docs/homepage-display-assets-map.md
```

Expected: one match per homepage media section.

- [ ] **Step 3: Commit**

Run:

```bash
git add docs/homepage-display-assets-map.md
git commit -m "docs: map homepage assets to shopify settings"
```

### Task 5: Verify Theme Behavior Against Empty-Media Expectations

**Files:**
- Verify: `sections/home-materials-section.liquid`
- Verify: `sections/home-endorsement-section.liquid`
- Verify: `sections/video-banner.liquid`

- [ ] **Step 1: Confirm image sections already tolerate blank media**

Run:

```bash
rg -n "if block.settings.image != blank|if first_block.settings.image != blank|if first_block and first_block.settings.image != blank" sections/home-materials-section.liquid sections/home-endorsement-section.liquid
```

Expected: matches confirm blank image guards exist in both sections.

- [ ] **Step 2: Confirm the video section fallback is editor-driven rather than broken markup**

Run:

```bash
rg -n "请在主题编辑器中上传视频或添加 YouTube / Vimeo 链接|section.settings.video" sections/video-banner.liquid
```

Expected: matches show the section falls back to an editor prompt when no video is set.

- [ ] **Step 3: Perform a final implementation review**

Run:

```bash
git status --short
```

Expected: only intentional tracked docs/scripts changes remain; generated media files stay ignored.

- [ ] **Step 4: Commit any required guardrail changes only if verification exposed a real defect**

Run:

```bash
git add sections/home-materials-section.liquid sections/home-endorsement-section.liquid sections/video-banner.liquid
git commit -m "fix: harden homepage media fallbacks"
```

Expected: skip this commit entirely if no code changes were needed.
