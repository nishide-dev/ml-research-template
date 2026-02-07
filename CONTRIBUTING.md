# Contributing to ML Research Template

Thank you for considering contributing to the ML Research Template! This document provides guidelines and instructions for contributing.

## Development Setup

### 1. Clone the Repository

```bash
git clone https://github.com/nishide-dev/ml-research-template.git
cd ml-research-template
```

### 2. Install Pre-commit Hooks

```bash
pip install pre-commit
pre-commit install
```

### 3. Install uv (for testing)

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

## Making Changes

### Testing Your Changes

#### Test template generation

```bash
# Test with defaults
uvx copier copy --trust --defaults . /tmp/test-project

# Test with custom config
uvx copier copy --trust \
  --data project_name="test-ml" \
  --data python_version="3.12" \
  --data package_manager="uv" \
  --data pytorch_cuda_preset="pytorch-2.8.0-cuda-12.6" \
  --data use_lightning=true \
  --data use_hydra=true \
  --data logger_choice="tensorboard" \
  . /tmp/test-project
```

#### Verify the generated project

```bash
cd /tmp/test-project

# Install dependencies
uv sync

# Run tests
uv run pytest tests/ -v

# Check code quality
uv run ruff check .
uv run ruff format --check .
uv run ty check src/

# Try training (should not error)
uv run python src/test_ml/train.py trainer.max_epochs=1
```

#### Test with different configurations

Test with multiple Python versions, package managers, and PyTorch presets:

```bash
# Test with pixi
uvx copier copy --trust \
  --data package_manager="pixi" \
  --defaults . /tmp/test-pixi

cd /tmp/test-pixi
pixi install
pixi run pytest tests/

# Test with Python 3.13
uvx copier copy --trust \
  --data python_version="3.13" \
  --defaults . /tmp/test-py313
```

### Code Style

- Follow the existing code style in `.jinja` templates
- Use clear, descriptive variable names
- Add comments for complex logic
- Update documentation for new features

### Commit Messages

Follow conventional commits format:

```
type(scope): brief description

Longer description if needed

BREAKING CHANGE: description of breaking change
```

**Types**:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `chore`: Maintenance tasks
- `refactor`: Code refactoring
- `test`: Test changes
- `ci`: CI/CD changes

**Examples**:
```
feat(copier): add PyTorch 2.9.0 + CUDA 13.0 preset
fix(hydra): correct logger configuration path
docs(readme): add troubleshooting section
```

## Pull Request Process

### 1. Create a Feature Branch

```bash
git checkout -b feat/my-feature
# or
git checkout -b fix/my-bugfix
```

### 2. Make Your Changes

- Edit files as needed
- Test template generation
- Verify generated projects work correctly

### 3. Run Pre-commit Hooks

```bash
pre-commit run --all-files
```

### 4. Commit Your Changes

```bash
git add .
git commit -m "feat: add new feature"
```

### 5. Push and Create PR

```bash
git push origin feat/my-feature
```

Then create a pull request on GitHub using the PR template.

### 6. PR Review Process

- Maintainers will review your PR
- Address any feedback or requested changes
- Once approved, your PR will be merged

## Guidelines

### Adding New Features

#### New Framework Support (e.g., JAX)

1. Add framework questions to `copier.yml`

2. Create Jinja templates for framework-specific files

3. Add conditional logic in templates:
   ```jinja
   {% if use_jax %}
   # JAX-specific code
   {% endif %}
   ```

4. Update documentation

#### New Configuration Options

1. Add question to `copier.yml`:
   ```yaml
   my_new_option:
     type: bool
     help: "Enable my new feature?"
     default: false
   ```

2. Use in templates:
   ```jinja
   {% if my_new_option %}
   # Feature code
   {% endif %}
   ```

3. Document in README.md

### Modifying Existing Templates

- **Test thoroughly**: Changes affect all generated projects
- **Maintain backwards compatibility**: Don't break existing users
- **Update documentation**: README.md, comments, help text
- **Consider edge cases**: Different OS, Python versions, package managers

### Documentation

- Update README.md for user-facing changes
- Update CONTRIBUTING.md for contributor-facing changes
- Add inline comments for complex Jinja logic
- Update help text in copier.yml

## Testing Checklist

Before submitting a PR, verify:

- [ ] Template generates successfully with defaults
- [ ] Template generates successfully with various configurations
- [ ] Generated project structure is correct
- [ ] Dependencies install successfully (uv and pixi)
- [ ] Tests pass in generated project
- [ ] Linting passes in generated project
- [ ] Training script runs without errors
- [ ] Documentation is updated
- [ ] Pre-commit hooks pass
- [ ] copier.yml syntax is valid
- [ ] All .jinja files have valid syntax

## Reporting Issues

Use GitHub Issues with the appropriate template:

- **Bug Report**: For bugs in template generation or generated projects
- **Feature Request**: For new features or enhancements

Provide:
- Copier version
- Python version
- Operating system
- Detailed reproduction steps
- Expected vs actual behavior
- Error logs (if applicable)

## Questions?

- Open a discussion on GitHub Discussions
- Reach out to maintainers
- Check existing issues and PRs

---

Thank you for contributing! 🎉
