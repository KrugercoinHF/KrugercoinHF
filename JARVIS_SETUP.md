# JARVIS Setup Guide

This guide is for running the JARVIS / HuggingGPT project in a local environment using the lite configuration, which is the most practical option when you do not have a CUDA GPU or a full local model stack.

Important:
- Keep API keys and tokens out of version control.
- Do not paste secrets into chat or commit them to git.
- Use environment variables or a local untracked config file.

## 1) Decide which config to use

Use the lite path unless you have a dedicated CUDA-enabled machine and want the full local-stack setup.

- `config.lite.yaml`: recommended for a normal dev machine
  - uses Hugging Face inference endpoints
  - requires OpenAI API key
  - requires Hugging Face access token
  - no GPU or model downloads required in the local environment
- `config.default.yaml`: full local mode
  - requires Python 3.8 via conda
  - requires CUDA-capable GPU
  - downloads several GB of local models
  - is not practical in this sandbox or on a low-memory machine

## 2) Clone the repo

```bash
git clone https://github.com/microsoft/JARVIS.git
cd JARVIS
```

If you already unpacked the source tree locally, skip this step and open the extracted repo directory.

## 3) Set environment variables

Set these in your shell before starting the server:

```bash
export OPENAI_API_KEY="your_openai_key_here"
export HUGGINGFACE_ACCESS_TOKEN="your_hf_token_here"
```

If the project expects a different variable name, use the one defined in the repo config, but the usual names are:

- `OPENAI_API_KEY`
- `HUGGINGFACE_ACCESS_TOKEN`

Do not hardcode these values into committed files.

## 4) Configure the server

Open the project config file used by the backend. For the lite setup, prefer the lite config:

```bash
cd hugginggpt/server
```

Then edit the relevant config file in the `configs/` folder, or duplicate and rename it if the repo expects a specific file name.

Typical structure:

```yaml
openai:
  api_key: ${OPENAI_API_KEY}

huggingface:
  access_token: ${HUGGINGFACE_ACCESS_TOKEN}
```

If the repo expects direct values instead of environment placeholders, replace them with your actual local values only in a local untracked file.

## 5) Create a Python environment

Use Python 3.8–3.10 if possible. Python 3.13 is not recommended for this project because several pinned dependencies are older and may fail to install.

Example with conda:

```bash
conda create -n jarvis python=3.9 -y
conda activate jarvis
```

If you are not using conda, use a project-local virtual environment instead:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

## 6) Install backend dependencies

From the repo root:

```bash
pip install -U pip setuptools wheel
pip install -r requirements.txt
```

If the repo has a server-specific requirements file, use that instead, for example:

```bash
pip install -r hugginggpt/server/requirements.txt
```

If installation fails due to old package pins, prefer the repo's lite instructions or a compatible Python version rather than forcing a broken dependency set.

## 7) Start the backend server

From the repo root or the server directory:

```bash
cd hugginggpt/server
python app.py
```

Or the repo may expose a different entrypoint such as:

```bash
python -m hugginggpt.server
```

Check the actual `README` or `run.sh` in the repo for the exact startup command.

## 8) Start the web client

Open the frontend project:

```bash
cd hugginggpt/web
npm install
npm run dev
```

Then open the local URL printed by Vite in the terminal, usually something like:

```text
http://localhost:5173
```

## 9) Troubleshooting

### Common issues

- `ModuleNotFoundError`: install missing Python dependencies
- `OpenAI API key missing`: set `OPENAI_API_KEY`
- `Hugging Face token missing`: set `HUGGINGFACE_ACCESS_TOKEN`
- old dependency pins break on Python 3.13: use Python 3.8–3.10 and a conda env if needed
- GPU errors: use default config only on a CUDA machine; otherwise use lite config

### Recommended mode

For a regular machine, use:
- Python 3.9
- `config.lite.yaml`
- environment variables for both tokens
- no GPU required

### Avoid in this sandbox

This environment is not suitable for full local JARVIS because it lacks:
- conda
- CUDA GPU
- enough memory for large model downloads
- a supported Python version for the older pin set

## 10) Security checklist

- do not commit `config.yaml` if it contains secrets
- add config files to `.gitignore` if needed
- never paste API keys into issue reports or chat messages
- rotate your keys if they are accidentally exposed

## 11) Recommended next step

If you want the simplest path, use the lite config and set both tokens locally. That is the best option for a development machine without dedicated GPU hardware.

If you want the full local AI stack, use a CUDA-enabled Linux machine with Python 3.8 and follow the repo's default configuration instructions.

---

This repo is not the JARVIS source tree itself; it is a local reference guide for running the project. Adjust the exact commands to the version of JARVIS you extracted locally.
