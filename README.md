<div align="center">
  <img src="https://github.com/user-attachments/assets/174a5031-43a7-46a1-ae5e-6f3b5e2ad76d" alt="Qwen Image Edit 2509 preview" width="100%" />
</div>

<div align="center" style="padding: 12px 0 20px 0;">
  <img src="https://img.shields.io/badge/Qwen-Image%20Edit-2509-6D5EF7?style=for-the-badge&logo=ai&logoColor=white" alt="Qwen Image Edit 2509" />
  <img src="https://img.shields.io/badge/LoRA-Lazy%20Load-00D8FF?style=for-the-badge" alt="Lazy Load" />
  <img src="https://img.shields.io/badge/Mode-Text%2BImage-2AF598?style=for-the-badge" alt="Text + image" />
  <img src="https://img.shields.io/badge/Runtime-CUDA%2013-FF8A00?style=for-the-badge" alt="CUDA 13" />
</div>

<h1 align="center" style="margin: 0; font-size: 42px; letter-spacing: 0.06em; color: #EAF2FF; text-shadow: 0 0 28px rgba(95, 150, 255, 0.92);">
  Qwen-Image-Edit-2509-LoRAs-Fast-Lazy-Load
</h1>

<p align="center" style="max-width: 940px; margin: 16px auto 0 auto; font-size: 18px; line-height: 1.7; color: #D3DEF7;">
  A futuristic AI image studio built on <strong>Qwen/Qwen-Image-Edit-2509</strong> with lazy-loaded LoRA adapters, fast transformer execution, and a polished browser workflow for text-driven image editing and style transfer.
</p>

<p align="center" style="max-width: 940px; margin: 14px auto 0 auto; font-size: 17px; line-height: 1.7; color: #AFC0E6;">
  This project dynamically downloads and fuses specialized adapters on demand, enabling Photo-to-Anime, Multiple Angles, Light Restoration, Relight, scene propagation, and high-end creative refinement inside a sleek single-page editing interface.
</p>

<div style="margin: 24px 0 28px 0; background: linear-gradient(135deg, rgba(13,16,32,0.96), rgba(17,26,48,0.8)); border: 1px solid rgba(120,170,255,0.38); border-radius: 26px; padding: 16px; box-shadow: 0 20px 55px rgba(93,125,255,0.18);">
  <div style="display: flex; flex-wrap: wrap; justify-content: center; gap: 12px;">
    <span style="background: rgba(99,102,241,0.18); border: 1px solid rgba(129,140,248,0.5); color: #E0E7FF; padding: 8px 14px; border-radius: 999px; font-size: 12px; font-weight: 700;">Lazy-Loaded LoRA Registry</span>
    <span style="background: rgba(34,211,238,0.15); border: 1px solid rgba(34,211,238,0.5); color: #DFFBFF; padding: 8px 14px; border-radius: 999px; font-size: 12px; font-weight: 700;">FA3 Acceleration</span>
    <span style="background: rgba(52,211,153,0.15); border: 1px solid rgba(52,211,153,0.5); color: #D1FAE5; padding: 8px 14px; border-radius: 999px; font-size: 12px; font-weight: 700;">Smart Aspect Ratio</span>
    <span style="background: rgba(250,204,21,0.15); border: 1px solid rgba(250,204,21,0.5); color: #FEF3C7; padding: 8px 14px; border-radius: 999px; font-size: 12px; font-weight: 700;">A/B Compare Studio</span>
  </div>
</div>

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); gap: 18px; margin: 20px 0 30px 0;">
  <div style="background: rgba(15,22,36,0.88); border: 1px solid rgba(96,165,250,0.35); border-radius: 18px; padding: 18px; box-shadow: 0 12px 28px rgba(37,99,235,0.14);">
    <p style="margin: 0 0 10px 0; color: #7DD3FC; font-size: 13px; font-weight: 700; letter-spacing: 0.08em; text-transform: uppercase;">Adapters</p>
    <h3 style="margin: 0; font-size: 30px; color: #EAF2FF;">11+</h3>
    <p style="margin: 10px 0 0 0; color: #CFDBFF; line-height: 1.6;">Specialized LoRA profiles for style transfer, relighting, enhancement, and creative transformations.</p>
  </div>
  <div style="background: rgba(15,22,36,0.88); border: 1px solid rgba(16,185,129,0.35); border-radius: 18px; padding: 18px; box-shadow: 0 12px 28px rgba(16,185,129,0.14);">
    <p style="margin: 0 0 10px 0; color: #6EE7B7; font-size: 13px; font-weight: 700; letter-spacing: 0.08em; text-transform: uppercase;">Acceleration</p>
    <h3 style="margin: 0; font-size: 30px; color: #EAF2FF;">FA3</h3>
    <p style="margin: 10px 0 0 0; color: #CFDBFF; line-height: 1.6;">Cross-attention optimization reduces memory overhead while accelerating image editing inference.</p>
  </div>
  <div style="background: rgba(15,22,36,0.88); border: 1px solid rgba(251,146,60,0.35); border-radius: 18px; padding: 18px; box-shadow: 0 12px 28px rgba(249,115,22,0.14);">
    <p style="margin: 0 0 10px 0; color: #FDBA74; font-size: 13px; font-weight: 700; letter-spacing: 0.08em; text-transform: uppercase;">Output</p>
    <h3 style="margin: 0; font-size: 30px; color: #EAF2FF;">4K</h3>
    <p style="margin: 10px 0 0 0; color: #CFDBFF; line-height: 1.6;">Image editing includes relighting, skin refinement, scene propagation, and upscale workflows.</p>
  </div>
</div>

## Overview

Qwen-Image-Edit-2509-LoRAs-Fast-Lazy-Load is a high-performance image editing and style-transfer platform built on top of the `Qwen/Qwen-Image-Edit-2509` base model and an optimized transformer architecture.

Using a lazy-loading design for LoRA adapters, the system dynamically downloads and fuses task-specific adapters on demand—including Photo-to-Anime, Multiple Angles, Light Restoration, Relight, Multi-Angle Variation, Skin Enhancement, Scene Propagation, Studio Effects, and Upscale tasks—without loading the full adapter registry upfront.

This makes the project ideal for experimentation, controlled visual finetuning, and fast reproduction of editing tasks through a browser-based interface.

## Key Features

- **Lazy-Loaded Adapter Registry:** On-demand downloading and weight-fusing for 11+ specialized LoRA adapters, including Photo-to-Anime, Multiple Angles, Light Restoration, Relight, Multi-Angle Variation, skin refinement, scene propagation, studio effects, and 4K upscale tasks.
- **Flash Attention 3 (FA3) Acceleration:** Hooks natively into the `QwenDoubleStreamAttnProcessorFA3` processor layer to accelerate cross-attention inference phases while reducing active GPU memory consumption.
- **Text-Guided Image Editing:** Offers camera angle rotations, shadow removal, uniform studio relighting, skin detail refinement, scene propagation, and 4K upscaling.
- **Studio SPA Interface:** An interactive single-page application built with modern vanilla web components—featuring an A/B image comparison slider, history filmstrip, quick prompt chips, and drag-and-drop upload workflow.
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

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); gap: 18px; margin: 22px 0 28px 0;">
  <div style="background: rgba(15,23,42,0.9); border: 1px solid rgba(96,165,250,0.35); border-radius: 18px; padding: 18px; box-shadow: 0 12px 30px rgba(29,78,216,0.18);">
    <h3 style="margin-top: 0; color: #E0F2FE;">⚡ Fast Setup</h3>
    <p style="margin: 0; color: #C7D2FE; line-height: 1.6;">Use <strong>uv</strong> for the recommended environment, fast dependency resolution, and reproducible builds.</p>
  </div>
  <div style="background: rgba(15,23,42,0.9); border: 1px solid rgba(34,197,94,0.35); border-radius: 18px; padding: 18px; box-shadow: 0 12px 30px rgba(16,185,129,0.18);">
    <h3 style="margin-top: 0; color: #D1FAE5;">🚀 Optimized Runtime</h3>
    <p style="margin: 0; color: #BBF7D0; line-height: 1.6;">A transformer pipeline tuned for faster inference, lower memory overhead, and efficient editing loops.</p>
  </div>
  <div style="background: rgba(15,23,42,0.9); border: 1px solid rgba(251,146,60,0.35); border-radius: 18px; padding: 18px; box-shadow: 0 12px 30px rgba(249,115,22,0.18);">
    <h3 style="margin-top: 0; color: #FFEDD5;">🎨 Creative Workflow</h3>
    <p style="margin: 0; color: #FED7AA; line-height: 1.6;">Upload assets, select a LoRA style, refine prompts, compare output, and iterate without leaving the app.</p>
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
    <img src="https://img.shields.io/badge/View%20Repo-on%20GitHub-181717?style=for-the-badge&logo=github" alt="View repo on GitHub" />
  </a>
  <a href="https://huggingface.co/spaces/prithivMLmods/Qwen-Image-Edit-2509-LoRAs-Fast" target="_blank">
    <img src="https://img.shields.io/badge/Live%20Demo-on%20Hugging%20Face-FF9E0F?style=for-the-badge&logo=huggingface" alt="Live demo on Hugging Face" />
  </a>
</div>

---

<p align="center" style="color: #A5B4FC; font-size: 13px; letter-spacing: 0.08em; text-transform: uppercase; margin-top: 14px;">
  Built for creative speed · efficient adapter orchestration · cinematic AI editing
</p>
