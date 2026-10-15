# 更低成本的 ReelMimic 原创角色素材方案

[English](README.md)

[ToAPIs · 对应模型价格与接入](https://go2v.toapis.com/re15gc)

**2026-10-15：3 张真实输出，图片实扣合计 $0.0394（7.88积分），后台截图已确认。**

独立API角色素材 → 原 stage-assets.mjs 本地导入；完整参考片分析与多Agent流程未验收。

## 演示

![真实输出与报价](screenshots/cn/01-models.png)

## 本次对应模型价格

| Model | Requested preset | Actual pixels | Quote / request | Actual charge |
|---|---|---|---:|---|
| [`gpt-image-2-vip`](https://go2v.toapis.com/re15v) | low / 1K | 1024×1024 | $0.0019 | $0.0019 / 0.38 credits |
| [`gemini-nano-banana-2.1`](https://x.toapis.com/re15n) | 1K | 1024×1024 | $0.0150 | $0.0150 / 3 credits |
| [`gpt-image-2.5-flare`](https://im25.toapis.com/re15f) | high / 1K | 1024×1024 | $0.0225 | $0.0225 / 4.5 credits |

报价合计: **$0.0394**. 本次图片实扣已由后台截图确认；1积分=$0.005。除 global 为用户提供价表，其余为当天实时 `/v1/pricing`。不同模型/画质不是等效质量比较。

## 复现

Python 3.11+; Node 22+ for Node/TS native-entry checks.

```bash
python -m pip install Pillow
python generate_case.py --model gpt-image-2-vip --prompt "An original product photograph, no text"
# Dry-run: no network call. Set TOAPIS_API_KEY privately before --live.
python generate_case.py --model gpt-image-2-vip --prompt "An original product photograph, no text" --run-id my-unique-case --output result.png --live
```

`TOAPIS_BASE_URL=https://toapis.com` or `https://toapis.cn`; `/v1` is appended once. `TOAPIS_STATE_DIR` stores private durable task state. `TOAPIS_MAX_IMAGE_USD` defaults to 0.10. Never commit the key or private state.

### 原生入口与范围

```bash
node upstream/stage-assets.mjs --from examples --hyperframes demo-project
```

The unchanged upstream importer was executed twice and all imported bytes were compared. See `evidence/native-import.json`. It is a local asset-import check, not the full ReelMimic analysis/agent/render workflow. Original 2D characters only.

### API mapping

`POST /v1/images/generations` → persist returned task ID → `GET /v1/images/generations/{id}` → download result without forwarding the API key to the CDN. Bearer authentication is used only for the API origin. No automatic POST retries. Uncertain submissions remain blocked until reconciled.

The adapter maps explicit pixel hints to aspect ratio and sends model-specific resolution fields. JPEG responses are preserved as original JPEG and separately converted to PNG for display; real pixel sizes are recorded. A requested 1K tier is not a guarantee of exactly 1024×1024 pixels.

## 证据与截图教程

- [Per-task records](evidence/)
- [Original outputs](examples/)
- [Model comparison](screenshots/cn/01-models.png)
- [API steps](screenshots/cn/02-api.png)
- [Reuse and limits](screenshots/cn/03-reuse.png)

Upstream: [edenfunf/reelmimic](https://github.com/edenfunf/reelmimic/tree/bf59f0d7b60cc49cc328c67329300e9d265964d1), pinned `bf59f0d7b60cc49cc328c67329300e9d265964d1`. Community work; no author endorsement or existing partnership is implied. [Attribution](NOTICE.md).

[Dashboard-confirmed itemized image charges](evidence/actual-charges.json). Video submissions without task IDs are excluded and remain unreconciled.
