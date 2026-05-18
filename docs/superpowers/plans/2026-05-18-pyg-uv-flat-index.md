# PyG uv Flat Index Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Make generated uv-based projects with PyTorch Geometric resolve PyG wheel pages correctly by emitting a flat uv index and binding PyG extension packages to that explicit source.

**Architecture:** Keep the current template structure intact and change only the generated uv configuration for PyG: mark the wheel page as a flat index and map PyG extension packages to that explicit index. Validate by regenerating projects that enable PyG and rerunning dependency resolution.

**Tech Stack:** Copier, Jinja2, uv, PyTorch Geometric

---

### Task 1: Add flat index metadata and source mappings for PyG uv packages

**Files:**
- Modify: `pyproject.toml.jinja`
- Test: generated `pyproject.toml` in a temporary copied project

- [ ] **Step 1: Write the failing test**

Use the existing reproduction as the failing test: generate a project with uv + PyG enabled and run `uv lock`, expecting failure before the template change.

```bash
uvx copier copy --trust --defaults \
  --data project_name="tmp-pyg-flat" \
  --data package_name="tmp_pyg_flat" \
  --data python_version="3.12" \
  --data package_manager="uv" \
  --data pytorch_cuda_preset="pytorch-2.8.0-cuda-12.6" \
  --data use_pytorch_geometric=true \
  --data use_lightning=true \
  --data use_hydra=true \
  --data logger_choice="none" \
  . /tmp/tmp-pyg-flat
```

- [ ] **Step 2: Run test to verify it fails**

Run:

```bash
rtk uv lock --project /tmp/tmp-pyg-flat
```

Expected: dependency resolution fails while probing the PyG wheel page or while resolving PyG extension wheels, often by falling back to a source build for `torch-cluster`.

- [ ] **Step 3: Write minimal implementation**

Update the PyG-specific uv configuration in `pyproject.toml.jinja`.

```toml
[[tool.uv.index]]
name = "pytorch-geometric"
url = "https://data.pyg.org/whl/torch-{{ pytorch_version }}+cu{{ cuda_version | replace('.', '') }}.html"
format = "flat"
explicit = true

[tool.uv.sources]
torch-scatter = { index = "pytorch-geometric" }
torch-sparse = { index = "pytorch-geometric" }
torch-cluster = { index = "pytorch-geometric" }
```

- [ ] **Step 4: Run test to verify it passes**

Regenerate the same project, then run:

```bash
rtk uv lock --project /tmp/tmp-pyg-flat
```

Expected: `uv lock` succeeds and resolves PyG dependencies.

- [ ] **Step 5: Commit**

```bash
git add pyproject.toml.jinja
git commit -m "fix: mark PyG uv source as flat index"
```

### Task 2: Sanity-check generated configuration

**Files:**
- Modify: none
- Test: generated `/tmp/tmp-pyg-flat/pyproject.toml`

- [ ] **Step 1: Write the failing test**

Check that the generated file lacks the required flat format and source mappings before the change.

```bash
grep -n 'format = "flat"' /tmp/tmp-pyg-flat/pyproject.toml
grep -n '\[tool\.uv\.sources\]\|torch-scatter\|torch-sparse\|torch-cluster' /tmp/tmp-pyg-flat/pyproject.toml
```

- [ ] **Step 2: Run test to verify it fails**

Expected: no matching lines before the template fix.

- [ ] **Step 3: Write minimal implementation**

No additional implementation beyond Task 1.

- [ ] **Step 4: Run test to verify it passes**

Regenerate the project and run:

```bash
grep -n 'format = "flat"' /tmp/tmp-pyg-flat/pyproject.toml
grep -n '\[tool\.uv\.sources\]\|torch-scatter\|torch-sparse\|torch-cluster' /tmp/tmp-pyg-flat/pyproject.toml
```

Expected: the generated PyG uv configuration contains `format = "flat"` and the three source mappings.

- [ ] **Step 5: Commit**

No additional commit beyond Task 1 unless follow-up changes are needed.
