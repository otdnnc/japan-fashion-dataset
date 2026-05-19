# ファッションアイテムデータセット

日本の大手ファッションEコマースサイトから収集された商品カタログデータセットです。ブランド、カテゴリ、価格、商品画像URL、商品説明文、バリエーション（色・サイズ）など、ファッションアイテムに関するリッチなメタデータを含んでおり、商品分類、レコメンデーション、マルチモーダル学習、画像-テキスト対応学習などの研究・開発用途に利用できます。

## データセット概要

- **ファイル形式**: Apache Parquet (Snappy 圧縮)
- **言語**: 日本語 (ja)
- **ドメイン**: ファッション / アパレル / Eコマース

## 想定ユースケース

- ファッション商品の自動カテゴリ分類 (テキスト/画像)
- 商品説明文からのメタデータ抽出 (素材、色、サイズ、用途など)
- 画像-テキストペアによるマルチモーダル事前学習 (CLIP 風モデル)
- 価格予測 / ブランド予測モデル
- レコメンデーションシステムの評価用商品コーパス
- 日本語ファッションドメインの言語モデルファインチューニング
- 検索 / セマンティック検索のベンチマーク


## スキーマ詳細

| カラム名 | 型 | 説明 |
|---|---|---|
| `brand` | struct | ブランド情報。`id` (int), `is_published` (bool), `key` (str), `name` (str), `name_kana` (str) を含むネストされた構造体 |
| `brand_no` | string | ブランド独自の品番 (例: `703-06301`) |
| `category` | struct | カテゴリ情報。`id`, `keyword`, `name` (親カテゴリ) と `child_category` (子カテゴリ) を含む |
| `content` | string | 商品の詳細説明文 (日本語)。素材、デザインの意図、機能、モデル身長などを含むことが多い長文テキスト |
| `currency_code` | string | 通貨コード (ISO 4217)。例: `JPY` |
| `currency_unit` | string | 通貨記号。例: `¥` |
| `display_price` | string | 表示用の整形済み価格文字列 (例: `¥104,500`) |
| `id` | int | アイテム内部 ID |
| `images` | struct | 商品画像 URL のリスト。各要素に `id`, `url_125`, `url_215`, `url_500` (3 種類の解像度) を含む |
| `item_detail_id` | int | 商品詳細ページの ID |
| `keywords` | struct | 商品に紐づくキーワードタグのリスト。各要素は `id`, `name` を持つ (例: `無地(柄)`, `ナイロン(素材)`, `A4サイズ収納可(バッグ サイズ)`) |
| `name` | string | 商品名 (日本語)。例: `ヒート リュックサック` |
| `price` | int | 価格 (整数、税込)。`currency_code` の通貨単位 |
| `sex_name` | string | 対象性別。`MEN`, `WOMEN`, `KIDS` など |
| `variations` | struct | バリエーション情報。`colors` (色のリスト) と `sizes` (サイズのリスト) を含む |

### ネスト構造のサンプル

#### `brand`
```json
{
  "id": 1140,
  "is_published": true,
  "key": "porter",
  "name": "PORTER",
  "name_kana": "ポーター"
}
```

#### `category`
```json
{
  "id": 114,
  "keyword": "bag",
  "name": "Bags",
  "child_category": {
    "id": 2045,
    "keyword": "backpack-rucksack",
    "name": "Backpack"
  }
}
```

#### `images`
```json
{
  "items": [
    {
      "id": 79,
      "url_125": "https://c.imgz.jp/116/1060116/1060116b_8_d_125.jpg",
      "url_215": "https://c.imgz.jp/116/1060116/1060116b_8_d_215.jpg",
      "url_500": "https://c.imgz.jp/116/1060116/1060116b_8_d_500.jpg"
    }
  ]
}
```

画像 URL のサフィックス `_125`, `_215`, `_500` はそれぞれ短辺ピクセル数の目安 (サムネイル / 中サイズ / 大サイズ) です。

#### `keywords`
```json
{
  "items": [
    {"id": 15,  "name": "無地(柄)"},
    {"id": 35,  "name": "ナイロン(素材)"},
    {"id": 156, "name": "ナイロン(素材)"},
    {"id": 161, "name": "A4サイズ収納可(バッグ サイズ)"}
  ]
}
```

キーワードは `名前(属性カテゴリ)` の形式が多く、`(...)` 内が属性タイプ (素材、柄、サイズなど) を表します。

#### `variations`
```json
{
  "items": {
    "colors": [{"name": "ブラック"}],
    "sizes":  [{"name": "FREE"}]
  }
}
```

## サンプルレコード

```text
brand            : {"id":1140,"is_published":true,"key":"porter","name":"PORTER","name_kana":"ポーター"}
brand_no         : 703-06301
category         : {"id":114,"keyword":"bag","name":"Bags","child_category":{"id":2045,"keyword":"backpack-rucksack","name":"Backpack"}}
content          : 耐久性と耐水性に優れた2種類の異なる素材を組み合わせ、無骨でスタイリッシュなデザインが魅力の「HEAT（ヒート）」シリーズです。...
currency_code    : JPY
currency_unit    : ¥
display_price    : ¥104,500
id               : 8
images           : {"items":[{"id":79,"url_125":"https://c.imgz.jp/...","url_215":"...","url_500":"..."}, ...]}
item_detail_id   : 102961
keywords         : {"items":[{"id":15,"name":"無地(柄)"}, {"id":35,"name":"ナイロン(素材)"}, ...]}
name             : ヒート リュックサック
price            : 104500
sex_name         : MEN
variations       : {"items":{"colors":[{"name":"ブラック"}],"sizes":[{"name":"FREE"}]}}
```

## 読み込み例

### Hugging Face `datasets` ライブラリ

```python
from datasets import load_dataset

ds = load_dataset("otdnnc/japan-fashion-items", split="train")
print(ds[0])
print(ds.features)
```

### pandas

```python
import pandas as pd

df = pd.read_parquet("data.parquet")
print(df.shape)
print(df.columns.tolist())
print(df.iloc[0])
```

### PyArrow (ストリーミング読み込み)

```python
import pyarrow.parquet as pq

table = pq.read_table("data.parquet")
print(table.schema)

# 必要なカラムだけロードしてメモリを節約
table = pq.read_table("data.parquet", columns=["id", "name", "brand", "price"])
```

### Polars

```python
import polars as pl

df = pl.read_parquet("data.parquet")
print(df.head())
```


## 前処理・利用上の注意

### 文字エンコーディング
すべての文字列は UTF-8 でエンコードされています。日本語の全角記号 (`／`, `（）` など) や半角カナが混在する可能性があるため、テキスト処理時には正規化 (NFKC) を推奨します。

### ネスト構造の取り扱い
`brand`, `category`, `images`, `keywords`, `variations` などは struct/list のネスト型です。pandas で読み込むと `dict` として展開されます。フラット化が必要な場合は以下のようにします。

```python
import pandas as pd

df = pd.read_parquet("data.parquet")
df["brand_name"] = df["brand"].apply(lambda x: x["name"] if x else None)
df["category_name"] = df["category"].apply(lambda x: x["name"] if x else None)
df["child_category_name"] = df["category"].apply(
    lambda x: x.get("child_category", {}).get("name") if x else None
)
df["image_urls"] = df["images"].apply(
    lambda x: [img["url_500"] for img in x["items"]] if x else []
)
```

### 画像のダウンロード
画像 URL は `c.imgz.jp` ドメインから配信されています。スクレイピング時はサーバ負荷を考慮し、適切なレート制限 (例: 1〜2 req/sec) を設けてください。

```python
import asyncio, aiohttp, aiofiles
from pathlib import Path

async def fetch(session, url, dest):
    async with session.get(url) as r:
        async with aiofiles.open(dest, "wb") as f:
            await f.write(await r.read())

async def main(urls):
    async with aiohttp.ClientSession() as session:
        sem = asyncio.Semaphore(4)  # 同時接続数を制限
        async def bound(u):
            async with sem:
                await fetch(session, u, Path("images") / u.split("/")[-1])
        await asyncio.gather(*[bound(u) for u in urls])
```

### 欠損値
一部のレコードでは `variations`, `keywords`, `images` のリストが空のことがあります。学習前に必ず null/空チェックを行ってください。

### 価格
`price` は整数値で、`currency_code` の通貨単位で表現されています。本データセットは日本国内向けで `JPY` のみが含まれます。


## 制限事項 (Limitations)

- 本データセットは特定時点のスナップショットであり、商品在庫・価格・URL は時間とともに変化します。
- 画像本体はデータセットに含まれていません。画像 URL を介して別途取得する必要があります。配信元の都合により一部 URL が失効している可能性があります。
- ブランド/カテゴリ分布には偏りがあり、特に高額ブランドや特定カテゴリ (バッグ、シューズなど) に集中する傾向があります。学習用途では分布の確認と必要に応じたサンプリング/重み付けを推奨します。
- `sex_name` のラベルは MEN/WOMEN/KIDS など限定的で、ジェンダー多様性を完全に反映するものではありません。
- 商品説明文 (`content`) はマーケティング文書であり、客観的な事実よりも宣伝的な表現を含む場合があります。

## ライセンスおよび利用規約

- 本データセット自体は **CC BY-NC 4.0** ライセンスで公開しています (非商用利用に限る)。
- ただし、各商品の画像・ブランド名・ロゴ・商品説明文等の知的財産権は、それぞれの権利者 (各ブランド、各撮影者など) に帰属します。
- 商用利用、再配布、画像の二次利用を行う場合は、必ず各権利者に確認・許諾を得てください。
- 本データセットはあくまで研究・教育目的を想定しています。

## 引用 (Citation)

本データセットを論文や研究で利用される場合は、以下のように引用してください。

```bibtex
@misc{japan_fashion_items,
  title  = {Japan Fashion Items Dataset},
  year   = {2026},
  howpublished = {\url{https://huggingface.co/datasets/otdnnc/japan-fashion-items}}
}
```

連絡先: otdnnc@gmail.com

## 連絡先 / 問題報告

データセットに関する問題、誤り、削除依頼などは Hugging Face のディスカッション機能、もしくはリポジトリの Issue よりご連絡ください。

---

# Fashion Items Dataset

This is a product catalog dataset collected from a major Japanese fashion e-commerce site. It contains rich metadata about fashion items, including brand, category, price, product image URLs, product descriptions, and variations (color/size). It can be used for research and development purposes such as product classification, recommendation, multimodal learning, and image-text alignment learning.

## Dataset Overview

- **File format**: Apache Parquet (Snappy compression)
- **Language**: Japanese (ja)
- **Domain**: Fashion / Apparel / E-commerce

## Intended Use Cases

- Automatic category classification of fashion products (text/image)
- Metadata extraction from product descriptions (materials, colors, sizes, intended use, etc.)
- Multimodal pre-training using image-text pairs (CLIP-style models)
- Price prediction / brand prediction models
- Product corpus for evaluating recommendation systems
- Fine-tuning language models for the Japanese fashion domain
- Benchmarking for search / semantic search


## Schema Details

| Column Name | Type | Description |
|---|---|---|
| `brand` | struct | Brand information. A nested struct containing `id` (int), `is_published` (bool), `key` (str), `name` (str), `name_kana` (str) |
| `brand_no` | string | Brand-specific product number (e.g., `703-06301`) |
| `category` | struct | Category information. Contains `id`, `keyword`, `name` (parent category), and `child_category` (child category) |
| `content` | string | Detailed product description (Japanese). Long-form text often including materials, design intent, features, model height, etc. |
| `currency_code` | string | Currency code (ISO 4217). E.g., `JPY` |
| `currency_unit` | string | Currency symbol. E.g., `¥` |
| `display_price` | string | Formatted price string for display (e.g., `¥104,500`) |
| `id` | int | Internal item ID |
| `images` | struct | List of product image URLs. Each element contains `id`, `url_125`, `url_215`, `url_500` (three resolutions) |
| `item_detail_id` | int | ID of the product detail page |
| `keywords` | struct | List of keyword tags associated with the product. Each element has `id`, `name` (e.g., `無地(柄)` / Solid (Pattern), `ナイロン(素材)` / Nylon (Material), `A4サイズ収納可(バッグ サイズ)` / A4 size storage capable (Bag size)) |
| `name` | string | Product name (Japanese). E.g., `ヒート リュックサック` (Heat Backpack) |
| `price` | int | Price (integer, tax included). In the currency unit of `currency_code` |
| `sex_name` | string | Target gender. `MEN`, `WOMEN`, `KIDS`, etc. |
| `variations` | struct | Variation information. Contains `colors` (list of colors) and `sizes` (list of sizes) |

### Nested Structure Samples

#### `brand`
```json
{
  "id": 1140,
  "is_published": true,
  "key": "porter",
  "name": "PORTER",
  "name_kana": "ポーター"
}
```

#### `category`
```json
{
  "id": 114,
  "keyword": "bag",
  "name": "Bags",
  "child_category": {
    "id": 2045,
    "keyword": "backpack-rucksack",
    "name": "Backpack"
  }
}
```

#### `images`
```json
{
  "items": [
    {
      "id": 79,
      "url_125": "https://c.imgz.jp/116/1060116/1060116b_8_d_125.jpg",
      "url_215": "https://c.imgz.jp/116/1060116/1060116b_8_d_215.jpg",
      "url_500": "https://c.imgz.jp/116/1060116/1060116b_8_d_500.jpg"
    }
  ]
}
```

The image URL suffixes `_125`, `_215`, `_500` indicate approximate short-edge pixel counts (thumbnail / medium / large), respectively.

#### `keywords`
```json
{
  "items": [
    {"id": 15,  "name": "無地(柄)"},
    {"id": 35,  "name": "ナイロン(素材)"},
    {"id": 156, "name": "ナイロン(素材)"},
    {"id": 161, "name": "A4サイズ収納可(バッグ サイズ)"}
  ]
}
```

Keywords are often in the format `name(attribute category)`, where the part inside `(...)` represents the attribute type (material, pattern, size, etc.).

#### `variations`
```json
{
  "items": {
    "colors": [{"name": "ブラック"}],
    "sizes":  [{"name": "FREE"}]
  }
}
```

## Sample Record

```text
brand            : {"id":1140,"is_published":true,"key":"porter","name":"PORTER","name_kana":"ポーター"}
brand_no         : 703-06301
category         : {"id":114,"keyword":"bag","name":"Bags","child_category":{"id":2045,"keyword":"backpack-rucksack","name":"Backpack"}}
content          : The "HEAT" series combines two different materials with excellent durability and water resistance, featuring a rugged yet stylish design...
currency_code    : JPY
currency_unit    : ¥
display_price    : ¥104,500
id               : 8
images           : {"items":[{"id":79,"url_125":"https://c.imgz.jp/...","url_215":"...","url_500":"..."}, ...]}
item_detail_id   : 102961
keywords         : {"items":[{"id":15,"name":"無地(柄)"}, {"id":35,"name":"ナイロン(素材)"}, ...]}
name             : ヒート リュックサック
price            : 104500
sex_name         : MEN
variations       : {"items":{"colors":[{"name":"ブラック"}],"sizes":[{"name":"FREE"}]}}
```

## Loading Examples

### Hugging Face `datasets` library

```python
from datasets import load_dataset

ds = load_dataset("otdnnc/japan-fashion-items", split="train")
print(ds[0])
print(ds.features)
```

### pandas

```python
import pandas as pd

df = pd.read_parquet("data.parquet")
print(df.shape)
print(df.columns.tolist())
print(df.iloc[0])
```

### PyArrow (streaming load)

```python
import pyarrow.parquet as pq

table = pq.read_table("data.parquet")
print(table.schema)

# Load only the necessary columns to save memory
table = pq.read_table("data.parquet", columns=["id", "name", "brand", "price"])
```

### Polars

```python
import polars as pl

df = pl.read_parquet("data.parquet")
print(df.head())
```


## Preprocessing and Usage Notes

### Character Encoding
All strings are encoded in UTF-8. Since full-width symbols (`／`, `（）`, etc.) and half-width kana may be mixed in Japanese text, normalization (NFKC) is recommended during text processing.

### Handling Nested Structures
Fields such as `brand`, `category`, `images`, `keywords`, `variations` are struct/list nested types. When loaded with pandas, they are expanded as `dict`. If flattening is required, you can do as follows:

```python
import pandas as pd

df = pd.read_parquet("data.parquet")
df["brand_name"] = df["brand"].apply(lambda x: x["name"] if x else None)
df["category_name"] = df["category"].apply(lambda x: x["name"] if x else None)
df["child_category_name"] = df["category"].apply(
    lambda x: x.get("child_category", {}).get("name") if x else None
)
df["image_urls"] = df["images"].apply(
    lambda x: [img["url_500"] for img in x["items"]] if x else []
)
```

### Downloading Images
Image URLs are served from the `c.imgz.jp` domain. When scraping, please consider server load and apply appropriate rate limiting (e.g., 1-2 req/sec).

```python
import asyncio, aiohttp, aiofiles
from pathlib import Path

async def fetch(session, url, dest):
    async with session.get(url) as r:
        async with aiofiles.open(dest, "wb") as f:
            await f.write(await r.read())

async def main(urls):
    async with aiohttp.ClientSession() as session:
        sem = asyncio.Semaphore(4)  # Limit concurrent connections
        async def bound(u):
            async with sem:
                await fetch(session, u, Path("images") / u.split("/")[-1])
        await asyncio.gather(*[bound(u) for u in urls])
```

### Missing Values
In some records, the lists for `variations`, `keywords`, and `images` may be empty. Always perform null/empty checks before training.

### Price
`price` is an integer value expressed in the currency unit of `currency_code`. This dataset is for the Japanese domestic market and contains only `JPY`.


## Limitations

- This dataset is a snapshot at a specific point in time, and product inventory, prices, and URLs change over time.
- The image files themselves are not included in the dataset. They must be obtained separately via the image URLs. Some URLs may have expired due to circumstances of the source.
- There is bias in the brand/category distribution, with a particular concentration in high-priced brands and certain categories (bags, shoes, etc.). For training use, checking the distribution and applying sampling/weighting as needed is recommended.
- The `sex_name` labels are limited to MEN/WOMEN/KIDS and do not fully reflect gender diversity.
- Product descriptions (`content`) are marketing documents and may contain promotional expressions rather than objective facts.

## License and Terms of Use

- This dataset itself is released under the **CC BY-NC 4.0** license (non-commercial use only).
- However, the intellectual property rights of each product's images, brand names, logos, product descriptions, etc. belong to their respective rights holders (each brand, each photographer, etc.).
- For commercial use, redistribution, or secondary use of images, please be sure to obtain confirmation and permission from each rights holder.
- This dataset is intended solely for research and educational purposes.

## Citation

If you use this dataset in a paper or research, please cite it as follows.

```bibtex
@misc{japan_fashion_items,
  title  = {Japan Fashion Items Dataset},
  year   = {2026},
  howpublished = {\url{https://huggingface.co/datasets/otdnnc/japan-fashion-items}}
}
```

Contact: otdnnc@gmail.com

## Contact / Issue Reporting

For issues, errors, deletion requests, etc. regarding the dataset, please contact us via the Hugging Face discussion feature or through the repository's Issues.
