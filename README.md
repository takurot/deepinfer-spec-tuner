# deepinfer-spec-tuner

[DeepInfer](https://github.com/deepseek-ai/DeepInfer) の投機デコード（nextn / DFlash の confidence head 温度 / MTP varlen）を、**実際のワークロードのプロンプトで測った受理率**をもとに調整するツール。

> **ステータス: 仕様策定段階（未実装）。** 機能仕様は [docs/SPEC.md](docs/SPEC.md) を参照。

## なぜ必要か

既存の道具は、どれも受理率を仮定で扱っている。

- `examples/benchmark.py` の decode スループットは、全トークンが受理される前提で `(1 + nextn)` を掛けている。プロンプトもランダムなトークン列である。
- `examples/sweep/plot_pareto.py` は、受理率を `--draft_acceptance` として人が入力する。

一方、エンジンは位置ごとの実測受理率を計算しており（`stats_logger._nextn_stats`）、ログにも出している。本ツールはこの値と、実際に生成されたトークン数・経過時間から実効スループットを求める。そして、nextn と温度の組み合わせのうち最も良いものを推薦する。

## 使い方（予定）

```bash
deepinfer-spec-tuner \
  --model /path/to/DeepSeek-V4.1-Flash \
  --prompts workload.jsonl --max-tokens 1024 \
  --nextn 0 2 3 5 \
  --temperature 0.8 1.0 1.2 \
  --out runs/tune-2026-11
```

出力は、構成ごとの実測受理率（位置別）、平均検証長、実効出力 tok/s/GPU、推薦する構成。

## ライセンス

[MIT](LICENSE)
