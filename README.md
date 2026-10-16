# FLUX 2 Max API (`flux-2-max`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-hero-perfume.jpg" width="820" alt="Sample render via the APIMart image API">
</p>

> **from $0.056 per image** (1MP $0.056 · 2MP $0.08 · 3MP $0.104) — `https://api.apimart.ai/v1` の OpenAI 互換エンドポイント1つだけ、月額プランは不要です。*(2026-10-12 時点)*

**[APIキー](https://go.apimart.ai/k-5edcdf)** · **[料金](https://go.apimart.ai/k-45068c)** · **[モデルページ](https://go.apimart.ai/k-22c514)** · [⚡ 60秒クイックスタート](#quickstart)

**チームが APIMart 経由で FLUX 2 Max（`flux-2-max`）を使う理由**

- **キー1つでカタログ全体。**同じ `https://api.apimart.ai/v1` の base URL と `Authorization` ヘッダーで FLUX 2 Max（`flux-2-max`）と画像・動画・言語モデルなど 300+ のモデルにアクセスできます。切り替えるのは `model` フィールドだけで、クライアント側の変更は不要です。
- **最低 $1 から、従量課金。**サブスクリプションも、事前に規模を見積もるプリペイドプランもありません。$1 からチャージして、呼び出した分だけ使えます。
- **課金内容はレスポンスで返ります。**完了したタスクはすべて請求額（`cost` / `credits_cost`）を報告し、`failed` になったタスクは返金されます。
- **非同期前提の設計。**送信して `task_id` を受け取り、`GET /v1/tasks/{id}` をポーリングするだけ。バッチ処理もリトライも特別な統合ではなく、普通のキュー処理です。

<!-- /conv-kit:v1 -->

**別名:** フラックス

FLUX 2 Max is the top FLUX 2 tier, priced per megapixel of output.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Per-image | `flux-2-max` | per delivered image, by resolution | see the [model page](https://go.apimart.ai/k-22c514) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/images/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Output | List price | Effective price |
| --- | --- | --- |
| default | 0.1 | 0.08 |
| 1MP | 0.07 | 0.056 |
| 2MP | 0.1 | 0.08 |
| 3MP | 0.13 | 0.104 |
| 4MP | 0.16 | 0.128 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-45068c) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | what to generate, or what to change when editing |
| `size` | string | `auto` / `1:1` | aspect ratio, or `auto` where the route supports it |
| `resolution` | string | `1K` | output resolution tier — cost scales with it |
| `n` | integer | `1` | number of images per task, where the route allows more than one |
| `image_urls` | string[] | — | reference images as public HTTP(S) URLs or base64 data URLs |
| `nsfw_check` | boolean | `false` | runs `omni-moderation-latest` before submitting |

The table lists the fields accepted by the APIMart `images/generations` endpoint. Check the [model page](https://go.apimart.ai/k-22c514) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/images/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"flux-2-max","prompt":"A bamboo forest at sunrise, slow push-in","size":"1:1","resolution":"1K"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/images/generations", headers=HEADERS, timeout=60, json={
    "model": "flux-2-max", "prompt": "A bamboo forest at sunrise, slow push-in",
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["images"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**FLUX 2 Max のモデル id は何ですか？**

`flux-2-max` です。上のルート表を参照してください。

**課金方法は？**

**from $0.056 per image** (1MP $0.056 · 2MP $0.08 · 3MP $0.104)。完了したタスクは `cost` フィールドに正確な金額を報告し、失敗したタスクは返金されます。

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-5edcdf) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [flux-2-max-prompts-ja](https://github.com/nbkj6/flux-2-max-prompts-ja)
- **Same model, other languages** — 9 more language builds: 中文 [flux-2-max-api-cn](https://github.com/apimartg583b51/flux-2-max-api-cn) · Deutsch [flux-2-max-api-de](https://github.com/nk7pvzvbm3b8/flux-2-max-api-de) · Español [flux-2-max-api-es](https://github.com/xmob24gstm0hwf/flux-2-max-api-es) · Français [flux-2-max-api-fr](https://github.com/apimartg583b51/flux-2-max-api-fr) · Relay/gateway [flux-2-max-api-gateway](https://github.com/apimartpzjt/flux-2-max-api-gateway) · Bahasa [flux-2-max-api-id](https://github.com/rtkz3/flux-2-max-api-id) · 한국어 [flux-2-max-api-ko](https://github.com/ibmf1yp-x2df/flux-2-max-api-ko) · Português [flux-2-max-api-pt](https://github.com/j3jxmbmj/flux-2-max-api-pt) · Русский [flux-2-max-api-ru](https://github.com/d79bcrw6n9kd/flux-2-max-api-ru)
- **More models in this language** — [midjourney-api-ja](https://github.com/apimartt1q/midjourney-api-ja) · [gemini-2-5-flash-image-preview-api-ja](https://github.com/ic7skkh/gemini-2-5-flash-image-preview-api-ja) · [nano-banana-api-ja](https://github.com/e4ylndl3g-v/nano-banana-api-ja) · [gemini-3-1-flash-image-preview-api-ja](https://github.com/gv4ju/gemini-3-1-flash-image-preview-api-ja) · [nano-banana-2-api-ja](https://github.com/lpbxkrzbfel/nano-banana-2-api-ja) · [gemini-3-pro-image-preview-api-ja](https://github.com/zzsxs8r/gemini-3-pro-image-preview-api-ja)
- **Topic hubs** — every build of this model, all languages: [flux-2-max](https://github.com/topics/flux-2-max) · [flux-2-max-api](https://github.com/topics/flux-2-max-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-22c514> | `apimart.ai/ja/model/flux-2` |
| Live pricing | <https://go.apimart.ai/k-45068c> | `apimart.ai/ja/pricing` |
| Get an API key | <https://go.apimart.ai/k-5edcdf> | `apimart.ai/ja/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

FLUX 2 Max is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
