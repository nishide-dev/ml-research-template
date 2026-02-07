# ML Research Template

**A Copier template for machine learning research projects using PyTorch Lightning + Hydra + W&B**

[日本語版 README](README-ja.md)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![uv](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/astral-sh/uv/main/assets/badge/v0.json)](https://github.com/astral-sh/uv)

## Features

### 🔥 Modern ML Stack

- **PyTorch Lightning 2.x**: High-level training framework
- **Hydra 1.3+**: Powerful configuration management
- **W&B/TensorBoard/MLflow**: Experiment tracking
- **PyTorch Geometric**: Graph Neural Networks (optional)

### ⚡ Fast Development Environment

- **uv**: 10-100x faster Python package manager than pip
- **pixi**: Conda alternative with automatic GPU environment setup
- **ruff**: Fast linter & formatter
- **ty**: Fast type checker (by Astral)
- **pytest**: Testing framework

### 🎯 9 PyTorch + CUDA Presets

| PyTorch | CUDA Versions |
|---------|---------------|
| 2.9.0   | 12.6, 13.0    |
| 2.8.0   | 12.6, 12.8    |
| 2.7.1   | 11.8, 12.6    |
| 2.6.0   | 12.4          |
| 2.5.1   | 12.1          |
| 2.4.1   | 11.8          |

## Quick Start

### Prerequisites

```bash
# Install uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# Or install pixi
curl -fsSL https://pixi.sh/install.sh | bash
```

### Generate Project

```bash
# From GitHub (recommended)
uvx copier copy --trust gh:nishide-dev/ml-research-template my-project

# From local clone
git clone https://github.com/nishide-dev/ml-research-template.git
uvx copier copy --trust ./ml-research-template my-project

# Navigate to your project
cd my-project

# Start training
uv run python src/my_project/train.py  # if using uv
pixi run train                           # if using pixi
```

**Tip**: Auto-fill author information from Git config:

```bash
uvx copier copy --trust gh:nishide-dev/ml-research-template my-project \
  --data author_name="$(git config user.name)" \
  --data author_email="$(git config user.email)"
```

### Configuration Options

Interactive prompts will ask for:

**Project Basics**:
- Project name, package name, description
- Author name, email
- Python version (3.10, 3.11, 3.12, 3.13)

**Package Manager**:
- `uv`: Fast, pip-compatible, simple
- `pixi`: Conda-based, automatic GPU setup

**PyTorch/CUDA Configuration**:
- 9 presets (PyTorch 2.4-2.9, CUDA 11.8-13.0)
- Custom configuration available
- Optional torchvision / torchaudio

**ML Frameworks**:
- PyTorch Lightning (2.2, 2.3, 2.4)
- Hydra (1.2, 1.3)
- PyTorch Geometric (for GNNs)

**Experiment Tracking**:
- TensorBoard (simple)
- Weights & Biases (feature-rich)
- MLflow (on-premise)
- Both (TensorBoard + W&B)
- None

**Development Tools**:
- ruff (linter/formatter)
- ty (type checker)
- pytest (testing)

## Generated Project Structure

```
my-project/
├── src/
│   └── my_project/
│       ├── __init__.py
│       ├── train.py              # Main training script (Hydra)
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
├── configs/                      # Hydra configuration
│   ├── config.yaml               # Main config
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
├── pyproject.toml                # uv + dependencies
├── pixi.toml                     # pixi config (if pixi selected)
├── ruff.toml                     # ruff config
├── .gitignore
├── .python-version
└── README.md
```

## Usage Examples

### Basic Training

```bash
# Train with default config
uv run python src/my_project/train.py

# Override parameters
uv run python src/my_project/train.py trainer.max_epochs=50 data.batch_size=64

# Use specific experiment config
uv run python src/my_project/train.py experiment=baseline
```

### Hydra Configuration Management

```bash
# Override multiple parameters
uv run python src/my_project/train.py \
  trainer.max_epochs=100 \
  data.batch_size=128 \
  model.learning_rate=0.001 \
  logger.name=my_experiment

# Multi-run sweep (hyperparameter search)
uv run python src/my_project/train.py -m \
  data.batch_size=32,64,128 \
  model.learning_rate=0.0001,0.001,0.01
```

### Experiment Tracking

```bash
# TensorBoard
tensorboard --logdir logs/

# Weights & Biases (login required)
wandb login
uv run python src/my_project/train.py

# MLflow
mlflow ui --backend-store-uri file:./mlruns
```

### Code Quality Checks

```bash
# Format and lint
uv run ruff format .
uv run ruff check . --fix

# Type check
uv run ty check src/

# Run tests
uv run pytest tests/ -v

# Run tests with coverage
uv run pytest --cov=src --cov-report=html
```

## Development

### Testing the Template

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

# Verify the generated project
cd /tmp/test-project
uv sync
uv run pytest tests/
uv run ruff check .
```

### Updating the Template

```bash
# Update existing project with latest template
uvx copier update /path/to/my-project
```

## Related Projects

- **[uv](https://github.com/astral-sh/uv)**: Fast Python package manager
- **[pixi](https://pixi.sh/)**: Fast Conda alternative
- **[PyTorch Lightning](https://lightning.ai/)**: PyTorch training framework
- **[Hydra](https://hydra.cc/)**: Configuration management framework

## Contributing

Contributions are welcome! See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

## License

MIT License - see [LICENSE](LICENSE) for details.

## Support

- **Issues**: [GitHub Issues](https://github.com/nishide-dev/ml-research-template/issues)
- **Discussions**: [GitHub Discussions](https://github.com/nishide-dev/ml-research-template/discussions)

---

**Made with ❤️ for ML researchers**
