# PyG uv Flat Index Design

## Goal
Fix generated uv-based projects that enable PyTorch Geometric so `uv lock` can resolve PyG extension wheels from `data.pyg.org` without failing on index probing or falling back to source builds.

## Problem
The template currently emits a PyG-specific `[[tool.uv.index]]` entry pointing at a `data.pyg.org` HTML wheel listing, but it does not mark that source as a flat index and it does not bind PyG extension packages to that explicit index. `uv` treats normal indexes as Simple API-style repositories unless told otherwise, and indexes marked `explicit = true` are only used for packages explicitly mapped to them. As a result, generated projects can fail during `uv lock` with 403 errors while probing the PyG URL or by attempting to build packages like `torch-cluster` from source instead of resolving wheels.

## Chosen Approach
Update the generated uv configuration for PyG by adding `format = "flat"` to the `pytorch-geometric` `[[tool.uv.index]]` block in `pyproject.toml.jinja` and add `[tool.uv.sources]` mappings for `torch-scatter`, `torch-sparse`, and `torch-cluster` so those packages resolve from the explicit PyG index.

## Why This Approach
- It matches `uv`'s documented handling of find-links style package pages.
- It matches a known-good working configuration already validated in another project.
- It is the smallest possible template change that addresses the root cause.
- It preserves the current package-selection model and does not require changing Copier questions or post-generation installation steps.

## Scope
### In scope
- Add `format = "flat"` to the generated PyG uv index block.
- Add `[tool.uv.sources]` mappings for `torch-scatter`, `torch-sparse`, and `torch-cluster`.
- Regenerate uv + PyG projects and verify `uv lock` succeeds.

### Out of scope
- Changing available PyTorch/CUDA presets.
- Reworking Pixi behavior.
- Replacing the uv index approach with custom install commands.

## Testing
1. Generate projects with `package_manager=uv` and `use_pytorch_geometric=true`.
2. Confirm the generated `pyproject.toml` includes both `format = "flat"` and `[tool.uv.sources]` mappings for the PyG extension packages.
3. Run `uv lock` in the generated projects.
4. Confirm dependency resolution succeeds for PyG packages such as `torch-cluster` without source build fallback.

## Risks
The only meaningful risk is that some PyG wheel pages may still have upstream availability gaps for specific torch/CUDA combinations. That would be a separate compatibility issue, not a misconfiguration of `uv` index format.
