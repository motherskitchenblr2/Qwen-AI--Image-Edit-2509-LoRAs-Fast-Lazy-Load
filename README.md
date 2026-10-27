<div align="center">
  <img src="https://github.com/user-attachments/assets/174a5031-43a7-46a1-ae5e-6f3b5e2ad76d" width="100%" alt="Qwen Image Edit 2509 preview" />
</div>

<div align="center" style="padding: 12px 0 20px 0;">
  <img src="https://img.shields.io/badge/Qwen-Image-Edit%202509-5B5BF7?style=for-the-badge&logo=ai&logoColor=white" alt="Qwen Image Edit 2509" />
  <img src="https://img.shields.io/badge/LoRA-Lazy%20Load-00D9FF?style=for-the-badge" alt="Lazy Load LoRA" />
  <img src="https://img.shields.io/badge/Mode-Text%20%2B%20Image%20Editing-00F5A0?style=for-the-badge" alt="Text + image editing" />
  <img src="https://img.shields.io/badge/Status-Production%20Demo-FF7A18?style=for-the-badge" alt="Production demo" />
</div>

<h1 align="center" style="margin-bottom: 8px; font-size: 42px; letter-spacing: 0.04em; color: #E6F3FF; text-shadow: 0 0 24px rgba(88, 140, 255, 0.9);">
  Qwen-Image-Edit-2509-LoRAs-Fast-Lazy-Load
</h1>

<p align="center" style="max-width: 900px; margin: 0 auto; font-size: 18px; line-height: 1.6; color: #C8D7F3;">
  A high-performance image editing and style-transfer platform built on top of the <strong>Qwen/Qwen-Image-Edit-2509</strong> base model with an optimized transformer architecture and a lazy-loaded LoRA registry.
</p>

<p align="center" style="max-width: 900px; margin: 18px auto 0 auto; font-size: 17px; color: #A8B8D9;">
  Text-to-image generation and image-to-text-to-image editing with live adapter streaming, cross-attention optimization, and a polished SPA interface for fast creative iteration.
</p>

<div style="background: linear-gradient(135deg, rgba(15,23,42,0.95), rgba(17,24,39,0.8)); border: 1px solid rgba(125,211,252,0.25); border-radius: 24px; padding: 24px; margin: 28px 0 26px 0; box-shadow: 0 20px 50px rgba(14,165,233,0.18);">
  <div style="display: flex; flex-wrap: wrap; gap: 18px; align-items: center; justify-content: center; margin-bottom: 12px;">
    <span style="background: rgba(99,102,241,0.18); border: 1px solid rgba(129,140,248,0.45); color: #E0E7FF; padding: 8px 14px; border-radius: 999px; font-size: 13px; font-weight: 700;">Lazy Adapter Registry</span>
    <span style="background: rgba(6,182,212,0.18); border: 1px solid rgba(34,211,238,0.45); color: #DFFBFF; padding: 8px 14px; border-radius: 999px; font-size: 13px; font-weight: 700;">Flash Attention 3</span>
    <span style="background: rgba(16,185,129,0.18); border: 1px solid rgba(52,211,153,0.45); color: #D1FAE5; padding: 8px 14px; border-radius: 999px; font-size: 13px; font-weight: 700;">Smart Aspect Ratio</span>
    <span style="background: rgba(250,204,21,0.18); border: 1px solid rgba(250,204,21,0.45); color: #FEF3C7; padding: 8px 14px; border-radius: 999px; font-size: 13px; font-weight: 700;">A/B Compare Studio</span>
  </div>
</div>

## Overview

Qwen-Image-Edit-2509-LoRAs-Fast-Lazy-Load is a high-performance image editing and style-transfer platform built on top of the `Qwen/Qwen-Image-Edit-2509` base model and an optimized transformer architecture. It is engineered for low-latency creative workflows and specialized visual transformations.

Using a lazy-loading design for LoRA adapters, the system dynamically downloads and fuses task-specific adapters on demand—including Photo-to-Anime, Multiple Angles, Light Restoration, Relight, Multi-Attribute Enhancement, and more—without loading the entire adapter registry upfront.

This makes the project ideal for experimentation, controlled visual finetuning, and fast reproduction of editing tasks through a browser-based interface.

## Key Features

- **Lazy-Loaded Adapter Registry:** On-demand downloading and weight-fusing for 11+ specialized LoRA adapters (e.g., *Photo-to-Anime*, *Multiple-Angles*, *Light-Restoration*, *Relight*, *Multi-Angle Variation*, *Skin Enhancement*, *Scene Propagation*, *Studio Effects*, and *Upscale*).
- **Flash Attention 3 (FA3) Acceleration:** Hooks natively into the `QwenDoubleStreamAttnProcessorFA3` processor layer to accelerate cross-attention inference phases while reducing active GPU memory consumption.
- **Text-Guided Image Editing:** Offers camera angle rotations, shadow removal, uniform studio relighting, skin detail refinement, scene propagation, and 4K upscaling.
- **Studio SPA Interface:** An interactive single-page application built with modern vanilla web components—featuring an A/B image comparison slider, history filmstrip, quick prompt chips, and drag-and-drop asset workflow.
- **Smart Aspect Ratio Snapping:** Automatically resizes uploaded images to stay within 1024px while snapping width and height to multiples of 8 to prevent shape mismatch errors during inference.

## Repository Structure

```text
├── examples/
│   ├── 1.jpg
│   ├── 10.jpeg
│   ├── 11.jpg
│   ├── 12.jpg
│   ├── 13.jpg
│   ├── 14.jpg
│   ├── 2.jpeg
│   ├── 4.jpg
│   ├── 5.jpg
│   ├── 6.jpg
│   ├── 7.jpg
│   ├── 8.jpg
│   ├── 9.jpg
│   ├── DI.jpg
│   └── ELS.jpg
├── qwenimage/
│   ├── __init__.py
│   ├── pipeline_qwenimage_edit_plus.py
│   ├── qwen_fa3_processor.py
│   └── transformer_qwenimage.py
├── app.py
├── index.html
├── LICENSE
├── pre-requirements.txt
├── pyproject.toml
├── README.md
├── requirements.txt
└── uv.lock
```

## Installation and Requirements

To set up the Qwen-Image-Edit-2509-LoRAs-Fast-Lazy-Load environment locally, configure your system according to the specifications below. A modern CUDA-enabled GPU is required.

- **Python Version:** Minimum Python **3.12** is required; Python **3.12** or **3.14** is recommended.
- **PyTorch Version:** `torch==2.11.0` or above is required for best compatibility.
- **CUDA Version:** CUDA **13.0** is recommended (`--extra-index-url https://download.pytorch.org/whl/cu130`), matching the environment used on the live Hugging Face demo.

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(260px, 1fr)); gap: 18px; margin: 22px 0 28px;">
  <div style="background: rgba(15,23,42,0.9); border: 1px solid rgba(96,165,250,0.35); border-radius: 20px; padding: 18px; box-shadow: 0 12px 30px rgba(29,78,216,0.18);">
    <h3 style="margin-top: 0; color: #E0F2FE;">⚡ Fast Setup</h3>
    <p style="margin: 0; color: #C7D2FE; line-height: 1.6;">Use <strong>uv</strong> for the recommended environment, faster dependency resolution, and reproducible builds.</p>
  </div>
  <div style="background: rgba(15,23,42,0.9); border: 1px solid rgba(34,197,94,0.35); border-radius: 20px; padding: 18px; box-shadow: 0 12px 30px rgba(16,185,129,0.18);">
    <h3 style="margin-top: 0; color: #D1FAE5;">🚀 Optimized Runtime</h3>
    <p style="margin: 0; color: #BBF7D0; line-height: 1.6;">Built for modern transformer pipelines with lazy adapter loading, performance-aware memory handling, and prompt-driven editing.</p>
  </div>
  <div style="background: rgba(15,23,42,0.9); border: 1px solid rgba(251,146,60,0.35); border-radius: 20px; padding: 18px; box-shadow: 0 12px 30px rgba(249,115,22,0.18);">
    <h3 style="margin-top: 0; color: #FFEDD5;">🎨 Creative Workflow</h3>
    <p style="margin: 0; color: #FED7AA; line-height: 1.6;">Upload assets, choose a LoRA style, guide edits with text prompts, compare outputs, and iterate rapidly.</p>
  </div>
</div>

### Running with `uv` (Recommended)

`uv` is an ultra-fast Python package and project manager written in Rust. It ensures rapid virtual environment setup and exact dependency synchronization based on the `uv.lock` file.

**Step 1 — Install `uv`**

- **macOS / Linux:** `curl -LsSf https://astral.sh/uv/install.sh | sh`
- **Windows:** `powershell -c "irm https://astral.sh/uv/install.ps1 | iex"`

**Step 2 — Clone the repository**

```bash
git clone https://github.com/PRITHIVSAKTHIUR/Qwen-Image-Edit-2509-LoRAs-Fast-Lazy-Load.git
cd Qwen-Image-Edit-2509-LoRAs-Fast-Lazy-Load
```

**Step 3 — Initialize the project and install dependencies**

```bash
uv sync
```

**Step 4 — Run the script**

```bash
uv run app.py
```

---

### Standard PIP Implementation

**1. Update Package Manager**

Upgrade your local package manager:

```bash
pip install pip>=26.1.2
```

**2. Install Core Dependencies**

Install the primary deep learning stack, transformer libraries, and core computing utilities listed in `requirements.txt`:

```bash
pip install -r requirements.txt
```

#### Core Requirements List (`requirements.txt`)

```text
--extra-index-url https://download.pytorch.org/whl/cu130
torch==2.11.0
torchvision==0.26.0
transformers==5.14.1
accelerate==1.14.0
diffusers==0.39.0
peft==0.19.1
gradio==6.22.0
av==17.1.0
spaces==0.51.1
huggingface-hub==1.24.0
kernels==0.16.0
```

## Usage

Once the web server initializes, open your browser to the local address output in your terminal (typically `http://127.0.0.1:7860/`).

1. **Upload Asset:** Drag and drop an image into the main canvas workspace, paste an image from your clipboard, or click the upload icon in the left rail.
2. **Select Style / LoRA:** Choose your target editing task from the **Style / LoRA** dropdown menu in the right inspector panel. The adapter weights will download lazily on first use.
3. **Refine Instructions:** Type your instructions inside the prompt field, or click one of the **Quick Prompts** chips to instantly fill it. Press ⌘/Ctrl + Enter or click **Edit Image**.
4. **Compare & Chain:** Use the **Compare** tool on the left rail to view an A/B slider of the before and after states. Click **Use as Input** to chain multiple edits sequentially.

## Links and Source

- **GitHub Repository:** [https://github.com/PRITHIVSAKTHIUR/Qwen-Image-Edit-2509-LoRAs-Fast-Lazy-Load.git](https://github.com/PRITHIVSAKTHIUR/Qwen-Image-Edit-2509-LoRAs-Fast-Lazy-Load.git)
- **Hugging Face Live Space:** [https://huggingface.co/spaces/prithivMLmods/Qwen-Image-Edit-2509-LoRAs-Fast](https://huggingface.co/spaces/prithivMLmods/Qwen-Image-Edit-2509-LoRAs-Fast)
- **License:** [Apache License 2.0](https://github.com/PRITHIVSAKTHIUR/Qwen-Image-Edit-2509-LoRAs-Fast-Lazy-Load/blob/main/LICENSE)
- **Acknowledgement:** Thanks to **AK** (aka [akhaliq](https://huggingface.co/akhaliq)) for the initial [PR #27](https://huggingface.co/spaces/prithivMLmods/Qwen-Image-Edit-2511-LoRAs-Fast/discussions/27), customization improvements, and support for the project ecosystem.

<div align="center" style="margin: 34px 0 18px 0;">
  <a href="https://github.com/PRITHIVSAKTHIUR/Qwen-Image-Edit-2509-LoRAs-Fast-Lazy-Load" target="_blank">
    <img src="https://img.shields.io/badge/View%20Repo-on%20GitHub-181717?style=for-the-badge&logo=github" alt="View Repo on GitHub" />
  </a>
  <a href="https://huggingface.co/spaces/prithivMLmods/Qwen-Image-Edit-2509-LoRAs-Fast" target="_blank">
    <img src="https://img.shields.io/badge/Live%20Demo-on%20Hugging%20Face-FF9E0F?style=for-the-badge&logo=huggingface" alt="Live Demo on Hugging Face" />
  </a>
</div>

---

<p align="center" style="color: #A5B4FC; font-size: 13px; letter-spacing: 0.08em; text-transform: uppercase; margin-top: 14px;">
  Built for creative speed · efficient adapter orchestration · cinematic AI editing
</p>
