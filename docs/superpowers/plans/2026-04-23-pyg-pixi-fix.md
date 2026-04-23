# PyG + Pixi Install Fix Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** `pixi install` が PyTorch Geometric 有効時に成功するよう `pyproject.toml.jinja` を修正する。

**Architecture:** 3箇所のテンプレート変更のみ。(1) pixiのプラットフォームをlinux-64限定に、(2) pixiセクションにfind-linksを追加してPyGホイールサーバーを参照、(3) UVセクションにsourcesを追加してexplicit indexを有効活用。

**Tech Stack:** Jinja2テンプレート, TOML, pixi, uv

---

### Task 1: pixiのプラットフォームをlinux-64のみに変更

**Files:**
- Modify: `pyproject.toml.jinja:170`

- [ ] **Step 1: 変更を適用する**

`pyproject.toml.jinja` の170行目を以下のように変更する:

```toml
# 変更前
platforms = ["linux-64", "osx-arm64"]

# 変更後
platforms = ["linux-64"]
```

- [ ] **Step 2: 生成されたファイルを確認する**

```bash
uvx copier copy --trust --defaults \
  --data package_manager=pixi \
  --data use_pytorch_geometric=true \
  . /tmp/test-pyg-pixi-task1
grep "platforms" /tmp/test-pyg-pixi-task1/pyproject.toml
```

期待される出力:
```
platforms = ["linux-64"]
```

- [ ] **Step 3: コミット**

```bash
git add pyproject.toml.jinja
git commit -m "fix: limit pixi platforms to linux-64 for CUDA PyG support"
```

---

### Task 2: UVセクションに [tool.uv.sources] を追加

`explicit = true` のindexはパッケージが明示的に紐付けられないと使われない。`[tool.uv.sources]` で torch-scatter 等を PyG index に紐付ける。

**Files:**
- Modify: `pyproject.toml.jinja:102` (既存の `{% endif -%}` の直前に挿入)

- [ ] **Step 1: 変更を適用する**

`pyproject.toml.jinja` の96〜102行目のブロックを以下に置き換える:

```jinja
{% if use_pytorch_geometric -%}
# PyTorch Geometric wheel index
[[tool.uv.index]]
name = "pytorch-geometric"
url = "https://data.pyg.org/whl/torch-{{ pytorch_version }}+cu{{ cuda_version | replace('.', '') }}.html"
explicit = true

[tool.uv.sources]
torch-scatter = { index = "pytorch-geometric" }
torch-sparse = { index = "pytorch-geometric" }
torch-cluster = { index = "pytorch-geometric" }
{% endif -%}
```

- [ ] **Step 2: UV向け生成ファイルを確認する**

```bash
rm -rf /tmp/test-pyg-uv
uvx copier copy --trust --defaults \
  --data package_manager=uv \
  --data use_pytorch_geometric=true \
  . /tmp/test-pyg-uv
grep -A 10 "tool.uv.sources" /tmp/test-pyg-uv/pyproject.toml
```

期待される出力:
```toml
[tool.uv.sources]
torch-scatter = { index = "pytorch-geometric" }
torch-sparse = { index = "pytorch-geometric" }
torch-cluster = { index = "pytorch-geometric" }
```

- [ ] **Step 3: PyG無効時にsourcesが生成されないことを確認する**

```bash
rm -rf /tmp/test-no-pyg
uvx copier copy --trust --defaults \
  --data package_manager=uv \
  --data use_pytorch_geometric=false \
  . /tmp/test-no-pyg
grep "tool.uv.sources" /tmp/test-no-pyg/pyproject.toml && echo "ERROR: should not exist" || echo "OK: not present"
```

期待される出力:
```
OK: not present
```

- [ ] **Step 4: コミット**

```bash
git add pyproject.toml.jinja
git commit -m "fix: add uv.sources to pin PyG packages to explicit wheel index"
```

---

### Task 3: pixiセクションに [tool.uv] find-links を追加

pixi は PyPI解決に内部でuvを使用しており、`pyproject.toml` の `[tool.uv]` を読む。PyG有効時にPyGのwheelサーバーをfind-linksで指定することで、pixi/uvリゾルバーがビルド済みホイールを自動発見できるようになる。

**Files:**
- Modify: `pyproject.toml.jinja:248` (既存の `{% endif -%}` の直後に挿入)

- [ ] **Step 1: 変更を適用する**

`pyproject.toml.jinja` の238〜248行目のブロックを以下に置き換える:

```jinja
{% if use_pytorch_geometric -%}
# ============================================================================
# Pixi Feature: PyTorch Geometric
# ============================================================================

[tool.pixi.feature.pyg.pypi-dependencies]
torch-geometric = "*"
torch-scatter = "*"
torch-sparse = "*"
torch-cluster = "*"

# pixi uses uv internally for PyPI resolution; find-links makes pre-built
# wheels discoverable from the PyG wheel server instead of building from source
[tool.uv]
find-links = ["https://data.pyg.org/whl/torch-{{ pytorch_version }}+cu{{ cuda_version | replace('.', '') }}.html"]
{% endif -%}
```

- [ ] **Step 2: pixi向け生成ファイルを確認する**

```bash
rm -rf /tmp/test-pyg-pixi
uvx copier copy --trust --defaults \
  --data package_manager=pixi \
  --data use_pytorch_geometric=true \
  . /tmp/test-pyg-pixi
grep -A 3 "tool.uv\]" /tmp/test-pyg-pixi/pyproject.toml
```

期待される出力 (`find-links` が含まれること):
```toml
[tool.uv]
find-links = ["https://data.pyg.org/whl/torch-2.8.0+cu126.html"]
```

- [ ] **Step 3: PyG無効時にtool.uvが生成されないことを確認する**

```bash
rm -rf /tmp/test-pixi-no-pyg
uvx copier copy --trust --defaults \
  --data package_manager=pixi \
  --data use_pytorch_geometric=false \
  . /tmp/test-pixi-no-pyg
grep "find-links" /tmp/test-pixi-no-pyg/pyproject.toml && echo "ERROR: should not exist" || echo "OK: not present"
```

期待される出力:
```
OK: not present
```

- [ ] **Step 4: TOML構文が正しいことを確認する**

```bash
python3 -c "import tomllib; tomllib.loads(open('/tmp/test-pyg-pixi/pyproject.toml').read()); print('OK: valid TOML')"
```

期待される出力:
```
OK: valid TOML
```

- [ ] **Step 5: コミット**

```bash
git add pyproject.toml.jinja
git commit -m "fix: add uv find-links for PyG wheel server in pixi config"
```

---

### Task 4: 総合確認

すべての変更が揃った状態で、UV・pixi 両方のパスを横断的に検証する。

**Files:** 変更なし（確認のみ）

- [ ] **Step 1: デフォルト設定（PyG無効）でUV生成が壊れていないことを確認**

```bash
rm -rf /tmp/test-defaults
uvx copier copy --trust --defaults . /tmp/test-defaults
python3 -c "import tomllib; tomllib.loads(open('/tmp/test-defaults/pyproject.toml').read()); print('OK: valid TOML')"
```

期待される出力:
```
OK: valid TOML
```

- [ ] **Step 2: PyG有効 + pixi の生成内容を総合確認**

```bash
rm -rf /tmp/test-final-pixi
uvx copier copy --trust --defaults \
  --data package_manager=pixi \
  --data use_pytorch_geometric=true \
  . /tmp/test-final-pixi
cat /tmp/test-final-pixi/pyproject.toml | grep -E "platforms|find-links|tool\.uv\]"
```

期待される出力:
```
platforms = ["linux-64"]
[tool.uv]
find-links = ["https://data.pyg.org/whl/torch-2.8.0+cu126.html"]
```

- [ ] **Step 3: PyG有効 + UV の生成内容を総合確認**

```bash
rm -rf /tmp/test-final-uv
uvx copier copy --trust --defaults \
  --data package_manager=uv \
  --data use_pytorch_geometric=true \
  . /tmp/test-final-uv
cat /tmp/test-final-uv/pyproject.toml | grep -E "tool\.uv\.sources|pytorch-geometric|torch-scatter"
```

期待される出力:
```toml
[tool.uv.sources]
torch-scatter = { index = "pytorch-geometric" }
torch-sparse = { index = "pytorch-geometric" }
torch-cluster = { index = "pytorch-geometric" }
```

- [ ] **Step 4: 最終コミット（必要な場合）**

Task 1〜3でコミット済みのため、未コミット変更がなければスキップ。

```bash
git status
```
