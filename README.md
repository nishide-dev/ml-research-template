# ML Research Template

**PyTorch Lightning + Hydra + W&B を使った機械学習研究プロジェクトの Copier テンプレート**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![uv](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/astral-sh/uv/main/assets/badge/v0.json)](https://github.com/astral-sh/uv)

## 特徴

### 🔥 最新のMLスタック

- **PyTorch Lightning 2.x**: 高レベル訓練フレームワーク
- **Hydra 1.3+**: 強力な設定管理システム
- **W&B/TensorBoard/MLflow**: 実験トラッキング
- **PyTorch Geometric**: グラフニューラルネットワーク（オプション）

### ⚡ 高速な開発環境

- **uv**: pip の 10-100 倍高速な Python パッケージマネージャー
- **pixi**: Conda 代替、GPU 環境の自動設定
- **ruff**: 高速リンター & フォーマッター
- **ty**: 高速型チェッカー（by Astral）
- **pytest**: テストフレームワーク

### 🎯 9種類の PyTorch + CUDA プリセット

| PyTorch | CUDA バージョン |
|---------|----------------|
| 2.9.0   | 12.6, 13.0     |
| 2.8.0   | 12.6, 12.8     |
| 2.7.1   | 11.8, 12.6     |
| 2.6.0   | 12.4           |
| 2.5.1   | 12.1           |
| 2.4.1   | 11.8           |

## クイックスタート

### 前提条件

```bash
# uv のインストール
curl -LsSf https://astral.sh/uv/install.sh | sh

# または pixi のインストール
curl -fsSL https://pixi.sh/install.sh | bash
```

### プロジェクト生成

```bash
# GitHub から直接（推奨）
uvx copier copy --trust gh:nishide-dev/ml-research-template my-project

# ローカルクローンから
git clone https://github.com/nishide-dev/ml-research-template.git
uvx copier copy --trust ./ml-research-template my-project

# プロジェクトに移動
cd my-project

# 訓練開始
uv run python src/my_project/train.py  # uv の場合
pixi run train                           # pixi の場合
```

**ヒント**: Git 設定から作者情報を自動入力：

```bash
uvx copier copy --trust gh:nishide-dev/ml-research-template my-project \
  --data author_name="$(git config user.name)" \
  --data author_email="$(git config user.email)"
```

### 設定オプション

インタラクティブプロンプトで以下を設定：

**プロジェクト基本情報**:
- プロジェクト名、パッケージ名、説明
- 作者名、メールアドレス
- Python バージョン (3.10, 3.11, 3.12, 3.13)

**パッケージマネージャー**:
- `uv`: 高速、pip 互換、シンプル
- `pixi`: Conda ベース、GPU 環境自動設定

**PyTorch/CUDA 設定**:
- 9 種類のプリセット（PyTorch 2.4-2.9, CUDA 11.8-13.0）
- カスタム設定も可能
- torchvision / torchaudio の選択

**ML フレームワーク**:
- PyTorch Lightning (2.2, 2.3, 2.4)
- Hydra (1.2, 1.3)
- PyTorch Geometric（GNN 用）

**実験トラッキング**:
- TensorBoard（シンプル）
- Weights & Biases（高機能）
- MLflow（オンプレミス向け）
- 両方（TensorBoard + W&B）
- なし

**開発ツール**:
- ruff (linter/formatter)
- ty (type checker)
- pytest (testing)

## 生成されるプロジェクト構造

```
my-project/
├── src/
│   └── my_project/
│       ├── __init__.py
│       ├── train.py              # メイン訓練スクリプト（Hydra）
│       ├── models/
│       │   ├── __init__.py
│       │   └── base_model.py     # LightningModule
│       ├── data/
│       │   ├── __init__.py
│       │   └── datamodule.py     # LightningDataModule
│       └── utils/
│           └── __init__.py
├── tests/
│   ├── __init__.py
│   └── test_my_project.py
├── configs/                      # Hydra 設定
│   ├── config.yaml               # メイン設定
│   ├── model/
│   │   └── default.yaml
│   ├── data/
│   │   └── default.yaml
│   ├── trainer/
│   │   └── default.yaml
│   ├── logger/
│   │   ├── tensorboard.yaml
│   │   ├── wandb.yaml
│   │   └── mlflow.yaml
│   └── experiment/
│       └── baseline.yaml
├── pyproject.toml                # uv + 依存関係
├── pixi.toml                     # pixi 設定（pixi 選択時）
├── ruff.toml                     # ruff 設定
├── .gitignore
├── .python-version
└── README.md
```

## 使用例

### 基本的な訓練

```bash
# デフォルト設定で訓練
uv run python src/my_project/train.py

# パラメータをオーバーライド
uv run python src/my_project/train.py trainer.max_epochs=50 data.batch_size=64

# 特定の実験設定を使用
uv run python src/my_project/train.py experiment=baseline
```

### Hydra の設定管理

```bash
# 複数のパラメータをオーバーライド
uv run python src/my_project/train.py \
  trainer.max_epochs=100 \
  data.batch_size=128 \
  model.learning_rate=0.001 \
  logger.name=my_experiment

# マルチランスイープ（ハイパーパラメータ探索）
uv run python src/my_project/train.py -m \
  data.batch_size=32,64,128 \
  model.learning_rate=0.0001,0.001,0.01
```

### 実験トラッキング

```bash
# TensorBoard
tensorboard --logdir logs/

# Weights & Biases（事前にログインが必要）
wandb login
uv run python src/my_project/train.py

# MLflow
mlflow ui --backend-store-uri file:./mlruns
```

### コード品質チェック

```bash
# リント + フォーマット
uv run ruff format .
uv run ruff check . --fix

# 型チェック
uv run ty check src/

# テスト実行
uv run pytest tests/ -v

# カバレッジ付きテスト
uv run pytest --cov=src --cov-report=html
```

## 開発

### テンプレートのテスト

```bash
# デフォルト設定でテスト
uvx copier copy --trust --defaults . /tmp/test-project

# カスタム設定でテスト
uvx copier copy --trust \
  --data project_name="test-ml" \
  --data python_version="3.12" \
  --data package_manager="uv" \
  --data pytorch_cuda_preset="pytorch-2.8.0-cuda-12.6" \
  --data use_lightning=true \
  --data use_hydra=true \
  --data logger_choice="tensorboard" \
  . /tmp/test-project

# 生成されたプロジェクトを検証
cd /tmp/test-project
uv sync
uv run pytest tests/
uv run ruff check .
```

### テンプレートの更新

```bash
# 既存プロジェクトを最新テンプレートで更新
uvx copier update /path/to/my-project
```

## 関連プロジェクト

- **[claude-code-ml-research](https://github.com/nishide-dev/claude-code-ml-research)**: Claude Code プラグイン（このテンプレートを使用）
- **[uv](https://github.com/astral-sh/uv)**: 高速 Python パッケージマネージャー
- **[pixi](https://pixi.sh/)**: 高速 Conda 代替
- **[PyTorch Lightning](https://lightning.ai/)**: PyTorch 訓練フレームワーク
- **[Hydra](https://hydra.cc/)**: 設定管理フレームワーク

## コントリビューティング

コントリビューションを歓迎します！詳細は [CONTRIBUTING.md](CONTRIBUTING.md) を参照してください。

## ライセンス

MIT License - 詳細は [LICENSE](LICENSE) を参照してください。

## サポート

- **Issues**: [GitHub Issues](https://github.com/nishide-dev/ml-research-template/issues)
- **Discussions**: [GitHub Discussions](https://github.com/nishide-dev/ml-research-template/discussions)

---

**Made with ❤️ for ML researchers**
