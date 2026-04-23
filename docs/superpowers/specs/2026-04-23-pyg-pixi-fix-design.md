# PyTorch Geometric + Pixi インストール失敗の修正設計

**日付**: 2026-04-23  
**対象ファイル**: `pyproject.toml.jinja`

## 問題

`use_pytorch_geometric = true` かつ `package_manager = pixi` で生成したプロジェクトで `pixi install` が失敗する。

### 根本原因

2つの問題が重なっている:

1. **プラットフォーム問題**: テンプレートが `platforms = ["linux-64", "osx-arm64"]` を生成する。pixiはロックファイル解決時に両プラットフォームを同時に処理するため、CUDAホイールが存在しない `osx-arm64` での解決が失敗する。

2. **ホイールソース問題**: `torch-scatter` 等のPyPI上の配布物はソースのみ。`setup.py` がビルド時に `torch` を直接インポートするが、pixi/uvのビルド分離環境では `torch` が未インストールのためビルドが失敗する。ビルド済みホイールはPyGのwheelサーバー (`data.pyg.org/whl/`) にのみ存在する。

## 設計

### 変更1: pixiのプラットフォームをLinux限定に

macOSは現時点でスコープ外のため、pixiセクションのプラットフォームをLinux専用にする。

```toml
platforms = ["linux-64"]
```

### 変更2: pixiセクションに [tool.uv] find-links を追加

pixiはPyPI依存解決に内部でuvを使用しており、`pyproject.toml` の `[tool.uv]` を読む。PyG有効時のみ以下を追加する:

```toml
[tool.uv]
find-links = ["https://data.pyg.org/whl/torch-{pytorch_version}+cu{cuda_nodot}.html"]
```

これにより uvリゾルバーがPyGのwheelページを参照し、`torch-scatter` 等のビルド済みホイールを発見できる。

### 変更3: UVセクションに [tool.uv.sources] を追加

既存の `[[tool.uv.index]]`（`explicit = true`）はパッケージが明示的に指定されない限り使われない。PyGパッケージをそのindexに紐付ける:

```toml
[tool.uv.sources]
torch-scatter = { index = "pytorch-geometric" }
torch-sparse = { index = "pytorch-geometric" }
torch-cluster = { index = "pytorch-geometric" }
```

## スコープ

- 変更対象: `pyproject.toml.jinja` のみ
- UV・pixi 両方のパスで torch-scatter 等がビルド済みホイールから解決されるようになる
- macOS ARM64 は現時点で対象外（将来的に対応する場合は別設計が必要）
