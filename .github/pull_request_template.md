# Pull Request

## Description

<!-- Provide a clear and concise description of your changes -->

## Type of Change

<!-- Check all that apply -->

- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to change)
- [ ] Documentation update
- [ ] Template enhancement (new template variant, improved structure)
- [ ] Configuration improvement (better defaults, new options)

## Component Affected

<!-- Check all that apply -->

- [ ] copier.yml (template configuration)
- [ ] Generated project structure (src/, configs/, tests/)
- [ ] PyTorch/CUDA configuration
- [ ] Hydra configuration templates
- [ ] CI/CD templates (.github/workflows)
- [ ] Documentation (README.md, generated README.md.jinja)
- [ ] Dependencies (pyproject.toml.jinja, pixi.toml.jinja)
- [ ] Development tools (ruff.toml.jinja, .pre-commit-config)

## Motivation and Context

<!-- Why is this change required? What problem does it solve? -->

Fixes #(issue)

## Changes Made

<!-- Provide a detailed list of changes -->

-
-
-

## Testing

<!-- Describe the tests you ran to verify your changes -->

### Template Generation Tests

- [ ] Tested with defaults: `uvx copier copy --trust --defaults . /tmp/test`
- [ ] Tested with uv package manager
- [ ] Tested with pixi package manager
- [ ] Tested multiple Python versions (3.10, 3.11, 3.12, 3.13)
- [ ] Tested multiple PyTorch/CUDA presets

### Generated Project Tests

- [ ] Generated project installs dependencies successfully (`uv sync` or `pixi install`)
- [ ] Tests pass: `uv run pytest tests/` or `pixi run pytest tests/`
- [ ] Linting passes: `uv run ruff check .` or `pixi run ruff check .`
- [ ] Type checking passes: `uv run ty check` (if applicable)
- [ ] Training script runs without errors

### Specific Tests

<!-- Add specific test commands if relevant -->

```bash
# Example test commands
uvx copier copy --trust \
  --data project_name="test-ml" \
  --data pytorch_cuda_preset="pytorch-2.8.0-cuda-12.6" \
  . /tmp/test-ml

cd /tmp/test-ml
uv sync
uv run pytest tests/ -v
```

## ML Framework Compatibility

<!-- If applicable, check frameworks tested with -->

- [ ] PyTorch Lightning
- [ ] Hugging Face Transformers
- [ ] PyTorch Geometric
- [ ] Hydra
- [ ] Weights & Biases
- [ ] Not framework-specific

## Breaking Changes

<!-- If this PR introduces breaking changes, describe them here -->

- [ ] No breaking changes
- [ ] Changes copier.yml questions (may affect existing users)
- [ ] Changes generated project structure
- [ ] Changes dependency requirements
- [ ] Requires user action (migration guide below)

### Migration Guide

<!-- If breaking changes, provide migration instructions -->

```markdown
<!-- Example:
Old behavior: ...
New behavior: ...
Migration: ...
-->
```

## Documentation

<!-- Check all that apply -->

- [ ] Updated README.md (template repository)
- [ ] Updated README.md.jinja (generated project README)
- [ ] Updated copier.yml help text
- [ ] Added/updated code comments
- [ ] Added/updated example usage

## Checklist

- [ ] My code follows the template's style and conventions
- [ ] I have performed a self-review of my changes
- [ ] I have tested template generation with multiple configurations
- [ ] I have verified that generated projects work correctly
- [ ] I have made corresponding changes to the documentation
- [ ] My changes generate no new warnings or errors
- [ ] I have checked that copier.yml syntax is valid
- [ ] All .jinja files have valid syntax

## Additional Context

<!-- Add any other context, screenshots, or references about the pull request here -->

## Related Issues and PRs

<!-- Link related issues and pull requests -->

- Related to #
- Depends on #
- Blocks #
