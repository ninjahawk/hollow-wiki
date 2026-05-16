---
title: Setup
nav_order: 3
---

# Setup
{: .no_toc }

Three paths depending on your OS. All three end up at the same place: three agents running locally, picking their own goals, and an operator panel open on your desktop.

## What you need first

- **Disk**: depends on which model. Roughly:
  - `llama3.2:3b` (CPU-only): ~7 GB total
  - `gemma3:4b`: ~8 GB total
  - `qwen3.5:9b`: ~11 GB total
  - `qwen3.6:35b-a3b` (default): ~31 GB total

  Numbers include Docker Desktop (~3-5 GB), Hollow container images (~2-3 GB), and the model itself.
- **GPU is optional.** A modern NVIDIA card with 24 GB+ VRAM is recommended for the default model. Anything less and the wizard picks a smaller model automatically.
- **Internet** for the initial install and model download.
- **Python 3.12+** (the wizard installs it on Windows; verify with `python3 --version` elsewhere).

That's it. No accounts, no API keys, no cloud anything.

---

## Windows

1. Download `Hollow-agentOS.zip` from [releases](https://github.com/ninjahawk/hollow-agentOS/releases/latest).
2. Right-click → **Extract All**. Pick a permanent location (Desktop or Documents, not Downloads, since that may auto-clean).
3. Open the folder, double-click **`install.bat`**.

The installer handles everything from there:

- Installs Docker Desktop if missing
- Installs Ollama if missing
- Asks which AI model to use (recommends based on your hardware)
- Downloads the model (~2–7 GB)
- Starts the agents
- Opens the live monitor

**If Windows needs a restart** during install (Docker requires this on first install), that's normal. Restart, open the folder again, double-click `install.bat`: it picks up where it left off.

After the first run, **double-click `panel.bat`** for day-to-day operation. That's the operator panel, start/stop, per-agent controls, file injection, etc.

---

## macOS

1. Install [Docker Desktop for Mac](https://docs.docker.com/get-docker/) and start it.
2. Install Python 3.12+ (Homebrew: `brew install python@3.12`).
3. Clone and run:

```bash
git clone https://github.com/ninjahawk/hollow-agentOS
cd hollow-agentOS
pip3 install rich pywebview httpx
python3 hollow.py
```

The wizard installs Ollama via Homebrew if needed, walks you through model selection, downloads the model, and starts the agents.

For the panel: `python3 panel.py` (requires `pywebview httpx`: already installed by the line above).

---

## Linux

```bash
git clone https://github.com/ninjahawk/hollow-agentOS
cd hollow-agentOS
pip3 install rich httpx
python3 hollow.py
```

**pywebview on Linux requires extra steps.** Unlike Windows and macOS, you need to pick a backend (Qt or GTK) and may need system packages first. See the [pywebview Linux installation guide](https://pywebview.flowrl.com/guide/installation.html) for the full picture. The short version:

```bash
# Qt backend (works on most distros)
pip3 install pywebview[qt]

# GTK backend (alternative)
pip3 install pywebview[gtk]
```

On some distros (Xfce, minimal installs) you may also need a Qt package and a couple of apt libs. The pywebview guide covers those cases by distro.

There are two other Linux-specific things to know:

### Docker Desktop file sharing

If you use Docker Desktop on Linux (not native Docker engine), it sandboxes filesystem access. If `docker compose up` fails with `mounts denied: ... is not shared from the host`:

- Open **Docker Desktop → Settings → Resources → File Sharing**
- Add the path containing your `hollow-agentOS` clone
- Apply & restart Docker Desktop
- Re-run `python3 hollow.py`

If your project sits under `/mnt/` or `/media/` (external drive), Docker Desktop's VM can't see it at all. Move it to your home directory (e.g. `~/hollow-agentOS`) or switch to native Docker engine.

### NVIDIA GPU on Linux

CUDA toolkit alone is **not enough**: Docker needs `nvidia-container-toolkit` separately:

```bash
# Ubuntu / Mint / Debian
sudo apt-get install -y nvidia-container-toolkit
sudo systemctl restart docker
```

Without it, you'll see `could not select device driver "nvidia" with capabilities: [[gpu]]`. The setup wizard will detect this and fall back to CPU-only mode, but you'll lose 7-10× inference speed.

If you don't have a GPU at all, the wizard handles it automatically, picks a smaller model and edits the compose file to drop the GPU reservation block.

---

## Verifying it's running

After setup completes:

```bash
# Check API health
curl http://localhost:7777/health

# Check loaded model
curl http://localhost:11434/api/ps

# Watch agent activity (most useful)
python3 thoughts.py
```

The `thoughts.py` stream shows every goal an agent picks, every tool call, every suffering change, every lesson promoted. This is the live view of what's happening inside the system.

---

## What's next

See [Operator Panel]({{ '/Operator-Panel.html' | relative_url }}) for day-to-day controls. See [Troubleshooting]({{ '/Troubleshooting.html' | relative_url }}) if something didn't work.
