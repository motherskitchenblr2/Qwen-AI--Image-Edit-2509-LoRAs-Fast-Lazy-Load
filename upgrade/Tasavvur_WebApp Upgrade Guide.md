🎨 Tasavvur — Art to Transform the Visualization with Realistic Image

Complete Project Blueprint, Architecture Framework & Development Roadmap

---

1. Project Overview

Project Name: Tasavvur

Tagline: Art to Transform the Visualization with Realistic Image

Core Function: A serverless, multi-provider AI image generation and editing platform with enterprise-grade security, autonomous self-healing infrastructure, and a unified multi-layout interface (Wide, Square, Vertical) for desktop and mobile touch devices.

Deployment Topology:

```
┌─────────────────────────────────────────────────────────────────────┐
│  PRESENTATION LAYER — Vercel (Next.js 15 App Router)                │
│  • 3 responsive viewports: Wide (1920×1080), Square (1:1),         │
│    Vertical (9:16 for mobile touch)                                 │
│  • Admin Portal (separate URL) + User Application (main URL)       │
└──────────────────────┬──────────────────────────────────────────────┘
                       │ HTTPS
┌──────────────────────▼──────────────────────────────────────────────┐
│  CONTROL PLANE — Supabase                                           │
│  • PostgreSQL: users, generations, api_keys, audit_logs,           │
│    sentinel_events, admin_credentials                               │
│  • Row Level Security (RLS): user isolation + admin bypass         │
│  • Supabase Vault: encrypted secrets (API keys, tokens)            │
│  • Storage Bucket: original uploads, generated outputs             │
│  • Edge Functions: webhook receiver, API validation proxy          │
└──────────────────────┬──────────────────────────────────────────────┘
                       │ Server-to-Server + Pooler
┌──────────────────────▼──────────────────────────────────────────────┐
│  AI COMPUTE LAYER — Multi-Target                                 │
│  • Primary: Hugging Face ZeroGPU Space (Gradio SDK)                │
│  • Secondary: External API Providers (Flux, SDXL, Ideogram, etc.)  │
│  • Satellite: CPU Spaces for post-processing (rembg, upscale)      │
└─────────────────────────────────────────────────────────────────────┘
```

---

2. Complete System Architecture

2.1 Request Lifecycle

```
User (Browser)
    │
    ▼
[Next.js Client Component]
    │  POST /api/generate
    ▼
[Next.js Route Handler] ── validates session (Supabase Auth) ──► [Supabase PostgreSQL]
    │                                                              │
    │  checks quota, RLS policy, model routing                    │
    ▼                                                              ▼
[AI Provider Abstraction Layer]                              [Quota ledger updated]
    │
    ├──► [HF ZeroGPU Space] — Gradio Client (gradio_client Python SDK)
    ├──► [External API: Flux/SDXL/etc.] — REST call with Supabase Vault key
    └──► [CPU Satellite Space] — post-processing (rembg, potrace, upscale)
    │
    ▼
[Generated Image] ──► [Supabase Storage Bucket] ──► [Signed URL returned to client]
```

2.2 ZeroGPU Space Internal Architecture

The ZeroGPU Space runs a Gradio application. Critical constraint: models must be loaded at module scope (outside the @spaces.GPU decorator) but .to("cuda") is intercepted and deferred by the spaces package. The actual CUDA initialization happens inside a forked subprocess when the decorated function is called.

```python
# ═══════════════════════════════════════════════════════════════
# app.py — ZeroGPU Space Entry Point
# ═══════════════════════════════════════════════════════════════
import spaces  # MUST be imported before any torch or CUDA library
import torch
import gradio as gr
from diffusers import QwenImageEditPlusPipeline

# ── Module scope: eager load (deferred CUDA via spaces interceptor) ──
pipe = QwenImageEditPlusPipeline.from_pretrained(
    "Qwen/Qwen-Image-Edit-2509",
    torch_dtype=torch.bfloat16
).to("cuda")

# ── GPU-decorated functions: subprocess forked here ──
@spaces.GPU(duration=60)  # Tune precisely; over-declaration wastes quota
def generate_image(prompt, negative_prompt, steps, guidance, seed):
    generator = torch.Generator(device="cuda").manual_seed(seed)
    return pipe(
        prompt=prompt,
        negative_prompt=negative_prompt,
        num_inference_steps=steps,
        guidance_scale=guidance,
        generator=generator
    ).images[0]

@spaces.GPU(duration=90)  # Editing requires more steps
def edit_image(image, prompt, steps, guidance, seed):
    generator = torch.Generator(device="cuda").manual_seed(seed)
    return pipe(
        image=image,
        prompt=prompt,
        num_inference_steps=steps,
        true_cfg_scale=guidance,
        generator=generator
    ).images[0]

# ── Gradio UI ──
with gr.Blocks(title="Tasavvur AI Engine") as demo:
    with gr.Tabs():
        with gr.TabItem("Text to Image"):
            prompt = gr.Textbox(label="Prompt")
            btn = gr.Button("Generate")
            output = gr.Image()
            btn.click(generate_image, [prompt], output)
        with gr.TabItem("Image Editing"):
            img = gr.Image(type="pil")
            edit_prompt = gr.Textbox(label="Edit Instruction")
            edit_btn = gr.Button("Edit")
            edit_output = gr.Image()
            edit_btn.click(edit_image, [img, edit_prompt], edit_output)

demo.launch()
```

ZeroGPU Duration Strategy:
The duration parameter in @spaces.GPU(duration=...) declares the maximum GPU execution window. It caps at 120 seconds for standard inference (AoT compilation can use up to 1500 seconds). ZeroGPU allocates an H200 MIG slice (3g.71gb profile) per request. Declare the duration based on expected inference time; over-declaration reduces the total number of calls available within the daily allocation window.

2.3 Satellite CPU Spaces

Post-processing tools that do not require GPU are deployed as separate CPU-Basic Spaces to preserve the ZeroGPU allocation:

Satellite Space Tools Trigger
Background Removal rembg (U2NET) User clicks "Remove Background"
Vectorization potrace, vtracer User requests SVG export
Upscaling RealESRGAN (CPU-optimized) User requests 2×/4× upscale
Face Restoration CodeFormer Automatic on portrait detection

These Spaces do not consume ZeroGPU quota and run on free CPU infrastructure.

---

3. Technology Stack

3.1 Frontend (Vercel)

Layer Technology Purpose
Framework Next.js 15 (App Router) SSR, routing, API proxy
3D Landing React Three Fiber + Drei WebGL 3D hero scene
UI Library shadcn/ui + Radix Primitives Accessible components
Styling Tailwind CSS 4 Utility-first styling
State Zustand + TanStack Query Client state + server cache
Auth Client @supabase/ssr Cookie-based session

3.2 Backend (Supabase)

Component Configuration
Database PostgreSQL 15+
Connection (Serverless) Transaction Pooler, port 6543 — mandatory for Vercel serverless functions to prevent connection exhaustion
Auth Supabase Auth (email + OAuth)
Storage 1 GB bucket (originals + outputs; compress client-side before upload)
Secrets Supabase Vault — encrypted storage for API keys, GitHub PAT, Vercel token
Edge Functions Deno runtime — webhook receiver, API validation proxy

3.3 AI Compute (Hugging Face)

Resource Role
ZeroGPU Space Primary inference (Qwen-Image-Edit pipeline)
CPU Spaces (×3) Satellite post-processing
Inference Providers Fallback for external models (Flux, SDXL, etc.)

---

4. Database Schema

4.1 Core Tables

```sql
-- ═══════════════════════════════════════════════════════════════
-- USERS (managed by Supabase Auth, extended profile)
-- ═══════════════════════════════════════════════════════════════
CREATE TABLE profiles (
    id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
    email TEXT UNIQUE NOT NULL,
    role TEXT NOT NULL DEFAULT 'user' CHECK (role IN ('user', 'admin')),
    daily_generation_count INTEGER DEFAULT 0,
    daily_reset_at TIMESTAMPTZ DEFAULT NOW() + INTERVAL '24 hours',
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- ═══════════════════════════════════════════════════════════════
-- API KEYS (stored in Supabase Vault; metadata in this table)
-- ═══════════════════════════════════════════════════════════════
CREATE TABLE api_providers (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    provider_name TEXT NOT NULL,        -- 'Flux', 'SDXL', 'Ideogram', etc.
    provider_slug TEXT UNIQUE NOT NULL, -- 'flux', 'sdxl', 'ideogram'
    base_url TEXT NOT NULL,
    auth_header_name TEXT DEFAULT 'Authorization',
    auth_header_prefix TEXT DEFAULT 'Bearer ',
    is_active BOOLEAN DEFAULT false,
    is_custom BOOLEAN DEFAULT false,
    model_list JSONB DEFAULT '[]',
    last_validated_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE api_secrets (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    provider_id UUID REFERENCES api_providers(id) ON DELETE CASCADE,
    vault_secret_id UUID NOT NULL,      -- Reference to Vault secret
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- ═══════════════════════════════════════════════════════════════
-- GENERATIONS (user history)
-- ═══════════════════════════════════════════════════════════════
CREATE TABLE generations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES profiles(id) ON DELETE CASCADE,
    provider_slug TEXT NOT NULL,
    model_name TEXT,
    prompt TEXT NOT NULL,
    negative_prompt TEXT,
    parameters JSONB DEFAULT '{}',
    input_image_url TEXT,
    output_image_url TEXT,
    status TEXT DEFAULT 'pending' CHECK (status IN ('pending', 'processing', 'completed', 'failed')),
    duration_ms INTEGER,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- ═══════════════════════════════════════════════════════════════
-- SENTINEL EVENTS (autonomous agent audit trail)
-- ═══════════════════════════════════════════════════════════════
CREATE TABLE sentinel_events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    event_type TEXT NOT NULL,          -- 'error_detected', 'healing_applied', 'pr_created'
    severity TEXT CHECK (severity IN ('info', 'warning', 'critical')),
    source TEXT,                       -- 'frontend', 'backend', 'supabase', 'zerogpu'
    error_message TEXT,
    stack_trace TEXT,
    healing_action TEXT,
    pr_url TEXT,
    resolved BOOLEAN DEFAULT false,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- ═══════════════════════════════════════════════════════════════
-- ADMIN CREDENTIALS (separate from Supabase Auth)
-- ═══════════════════════════════════════════════════════════════
CREATE TABLE admin_credentials (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    username TEXT UNIQUE NOT NULL DEFAULT 'Admin',
    password_hash TEXT NOT NULL,       -- bcrypt hash, NOT plaintext
    updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

4.2 Row Level Security Policies

```sql
-- Enable RLS on all tables
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE generations ENABLE ROW LEVEL SECURITY;
ALTER TABLE api_providers ENABLE ROW LEVEL SECURITY;
ALTER TABLE api_secrets ENABLE ROW LEVEL SECURITY;
ALTER TABLE sentinel_events ENABLE ROW LEVEL SECURITY;
ALTER TABLE admin_credentials ENABLE ROW LEVEL SECURITY;

-- Users can only read their own profile
CREATE POLICY "Users read own profile"
    ON profiles FOR SELECT
    USING (auth.uid() = id);

-- Users can only read their own generations
CREATE POLICY "Users read own generations"
    ON generations FOR SELECT
    USING (auth.uid() = user_id);

-- Admin bypass: full access via service_role (server-side only)
-- The service_role key is NEVER exposed to the client
CREATE POLICY "Service role full access"
    ON profiles FOR ALL
    TO service_role
    USING (true)
    WITH CHECK (true);

-- Public read of active providers (no secrets exposed)
CREATE POLICY "Public read active providers"
    ON api_providers FOR SELECT
    USING (is_active = true);

-- Sentinel events: admin-only read
CREATE POLICY "Admin read sentinel events"
    ON sentinel_events FOR SELECT
    TO service_role
    USING (true);
```

4.3 Connection Configuration for Vercel

```
# ═══════════════════════════════════════════════════════════════
# .env.local — Vercel Environment Variables
# ═══════════════════════════════════════════════════════════════

# Supabase (Transaction Pooler for serverless)
DATABASE_URL="postgresql://postgres.[ref]:[password]@aws-0-[region].pooler.supabase.com:6543/postgres"

# Supabase Client (public)
NEXT_PUBLIC_SUPABASE_URL="https://[ref].supabase.co"
NEXT_PUBLIC_SUPABASE_ANON_KEY="eyJ..."

# Supabase Service Role (server-side only — NEVER expose to client)
SUPABASE_SERVICE_ROLE_KEY="eyJ..."

# ZeroGPU Space
HF_SPACE_URL="https://[username]-tasavvur-ai-engine.hf.space"
```

Critical: Use port 6543 (Transaction Pooler) for all serverless database connections. Port 5432 (direct connection) will exhaust the connection limit under Vercel's concurrent function invocations.

---

5. AI Provider Abstraction Layer

5.1 Provider Registry Architecture

The system uses a provider adapter pattern — each AI image generation API is encapsulated behind a common interface. The frontend never calls provider APIs directly; all requests route through the Next.js backend, which resolves the correct adapter and injects the decrypted API key from Supabase Vault.

```typescript
// ═══════════════════════════════════════════════════════════════
// lib/providers/types.ts
// ═══════════════════════════════════════════════════════════════
export interface ImageProvider {
  slug: string;
  name: string;
  validateKey(apiKey: string): Promise<boolean>;
  listModels(apiKey: string): Promise<ModelInfo[]>;
  generate(params: GenerationParams): Promise<GenerationResult>;
}

export interface GenerationParams {
  prompt: string;
  negativePrompt?: string;
  image?: Buffer;
  width?: number;
  height?: number;
  steps?: number;
  guidance?: number;
  seed?: number;
  modelName?: string;
}

export interface GenerationResult {
  imageUrl: string;
  provider: string;
  model: string;
  durationMs: number;
}
```

5.2 Built-In Provider Matrix

Provider Slug Models Auth Type Rate-Limit Handling
Hugging Face ZeroGPU hf-zerogpu Qwen-Image-Edit Internal (Space) Duration-based allocation
Flux (BFL) flux FLUX.1-dev, FLUX.1-schnell, FLUX.2 Bearer token Exponential backoff
Stable Diffusion stability SDXL, SD3, SD3.5 Bearer token Queue-based
Ideogram ideogram Ideogram v2, v3 Bearer token Token bucket
NVIDIA SANA sana Sana-1.6B, Sana-4.8B Bearer token Request queue
Pollinations pollinations Multiple (proxy) None (public endpoint) Client-side throttle
Qwen-Image qwen Qwen-Image Bearer token Exponential backoff
HunyuanImage hunyuan HunyuanImage Bearer token Exponential backoff
Craiyon craiyon DALL-E mini variants Bearer token Rate-limited

5.3 Custom Provider Registration (Admin Panel)

The admin panel includes a "Add Custom Provider" modal with fields:

Field Type Validation
Provider Name Text Required, unique
Provider Slug Text Lowercase, alphanumeric + hyphens
Base URL URL Must be HTTPS
Auth Header Name Text Default: Authorization
Auth Header Prefix Text Default: Bearer 
API Key Password Encrypted via Supabase Vault
Model List Endpoint URL (optional) Auto-discovery endpoint
Test Prompt Text Default: a red apple on a table

Validation Flow:

1. Admin submits the form.
2. Next.js Route Handler (/api/admin/providers/validate) receives the key.
3. The handler makes a minimal test request to the provider's model list or a low-cost generation endpoint.
4. If validation succeeds → the key is encrypted and stored in Supabase Vault; the provider row is marked is_active = true.
5. If validation fails → the error is returned to the admin UI with the provider's response body.

5.4 API Key Security Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│  ADMIN BROWSER                                                      │
│  (Authenticated via admin_credentials, NOT Supabase Auth)           │
└──────────────────────┬──────────────────────────────────────────────┘
                       │ POST /api/admin/providers (HTTPS)
                       ▼
┌─────────────────────────────────────────────────────────────────────┐
│  NEXT.JS ROUTE HANDLER                                              │
│  1. Verify admin session (JWT from admin login)                     │
│  2. Validate API key against provider endpoint                      │
│  3. Encrypt key → store in Supabase Vault                           │
│  4. Store provider metadata in api_providers table                  │
│  5. Return success (key is NEVER returned to client)                │
└──────────────────────┬──────────────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────────────┐
│  SUPABASE VAULT                                                     │
│  • Encrypted at rest (AES-256)                                      │
│  • Decrypted only at request time by service_role                   │
│  • Never exposed in API responses                                   │
│  • Audited via Vault access logs                                    │
└─────────────────────────────────────────────────────────────────────┘
```

Critical Security Rule: The SUPABASE_SERVICE_ROLE_KEY is used exclusively in Next.js Route Handlers (server-side). It is never included in any client-side bundle, environment variable prefixed with NEXT_PUBLIC_, or API response.

5.5 Serverless IP-Blocking Mitigation

Serverless platforms (Vercel) and AI providers may flag high-volume requests from cloud IP ranges as commercial use, blocking API keys. Mitigation strategies:

Strategy Implementation
Request Spreading Distribute provider calls across multiple API keys (key rotation)
User-Agent Rotation Set a descriptive User-Agent header (e.g., Tasavvur/1.0) — some providers allow legitimate apps
Proxy Layer Route requests through Cloudflare Workers (free tier) to mask Vercel IPs
Provider Diversification Maintain at least 3 active providers; failover automatically when one is rate-limited
Admin Alert Sentinel monitors 403/429 responses and notifies admin to rotate keys or switch providers

---

6. Admin Panel & Security Architecture

6.1 Dual-Authentication Model

Portal URL Auth Method Credentials
User App https://tasavvur.vercel.app Supabase Auth (email/OAuth) User-managed
Admin Portal https://tasavvur.vercel.app/admin Custom admin session (JWT) Admin / Admin@1675 (initial)

The admin portal is not accessible via Supabase Auth. It uses a separate credentials table (admin_credentials) with bcrypt-hashed passwords. The admin session JWT is signed with a dedicated ADMIN_JWT_SECRET and expires after 8 hours.

6.2 Admin Password Change Modal

```typescript
// ═══════════════════════════════════════════════════════════════
// app/admin/settings/page.tsx (Admin Settings)
// ═══════════════════════════════════════════════════════════════
// Modal fields:
// - Current Username (pre-filled: "Admin")
// - New Username
// - Current Password
// - New Password
// - Confirm New Password
//
// On submit → POST /api/admin/credentials
// → Verify current password against bcrypt hash
// → Validate new password (min 12 chars, 1 uppercase, 1 number, 1 symbol)
// → Update admin_credentials with new bcrypt hash
// → Invalidate all existing admin sessions
```

6.3 Cyber Security Systems (Admin Panel)

Module Function
Audit Log Viewer Paginated view of all sentinel_events, filterable by severity/source
API Key Rotation One-click rotation: generate new key → validate → swap in Vault → deactivate old
Rate Limit Dashboard Per-provider request counts, error rates, latency
IP Allowlist Restrict admin portal access to specific IP ranges (optional)
Session Manager View and revoke active admin sessions
Security Headers Enforce CSP, HSTS, X-Frame-Options via next.config.js

---

7. Autonomous Agentic Sentinel System

7.1 Sentinel Architecture

The Sentinel is a background agent running on a schedule (Vercel Cron Jobs) and triggered by error events. It performs four core functions:

```
┌─────────────────────────────────────────────────────────────────────┐
│  SENTINEL CORE                                                      │
│                                                                     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐             │
│  │  MONITOR     │  │  DIAGNOSE    │  │  HEAL        │             │
│  │              │  │              │  │              │             │
│  │ • Health     │  │ • Parse      │  │ • Retry with │             │
│   │   checks     │  │   stack      │  │   backoff    │             │
│  │ • Error      │  │   traces     │  │ • Rotate key │             │
│   │   ingestion  │  │ • Classify   │  │ • Switch     │             │
│  │ • Latency    │  │   root cause │  │   provider   │             │
│   │   tracking   │  │              │  │ • Create PR  │             │
│  └──────────────┘  └──────────────┘  └──────────────┘             │
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  CHAT INTERFACE (Admin ↔ Sentinel)                           │  │
│  │  • Natural language query                                    │  │
│  │  • "Why did generation X fail?"                              │  │
│  │  • "Show me the last 10 critical errors"                     │  │
│  │  • "Create a PR to fix the broken provider adapter"          │  │
│  └──────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```

7.2 Sentinel Healing Actions

Trigger Diagnosis Healing Action Escalation
ZeroGPU 429 (quota exhausted) Daily allocation depleted Pause non-critical jobs; queue requests Notify admin
Provider 401 (invalid key) Key expired/revoked Deactivate provider; failover to next Alert admin to rotate
Frontend TypeError in production Unhandled exception Capture stack trace; create GitHub issue Admin reviews chat
Supabase RLS violation Policy misconfiguration Log event; flag for admin review Admin reviews chat
Build failure on Vercel TypeScript/lint error Capture build log; create PR with suggested fix Admin reviews PR

7.3 GitHub PR Automation

When the Sentinel identifies a code-level issue (e.g., a deprecated API endpoint, a broken import path, or a missing error handler), it can:

1. Create a branch from main (e.g., sentinel/fix-provider-adapter-2026-10-27).
2. Apply the fix via GitHub API (file content update).
3. Open a Pull Request with:
   · Title: [Sentinel] Fix: <description>
   · Body: Error summary, root cause, proposed fix, verification steps.
   · Labels: sentinel, auto-generated.
4. Log the PR URL in sentinel_events.pr_url.

The admin reviews and merges the PR manually — the Sentinel never auto-merges to production.

7.4 Sentinel Chat Interface

The admin portal includes a Sentinel Chat tab (similar to ChatGPT/Gemini):

```
┌─────────────────────────────────────────────────────────────────────┐
│  SENTINEL CHAT                               [Admin: online]       │
├─────────────────────────────────────────────────────────────────────┤
│  Admin: Why did image generation fail for user abc@example.com?    │
│                                                                     │
│  Sentinel: The generation failed because the Flux provider         │
│  returned a 401 (invalid API key). I detected this at 14:32 UTC.   │
│  The key was last validated on 2026-10-20. I have deactivated the  │
│  Flux provider and failed over to Stability AI. The user's request │
│  was retried and succeeded in 4.2s.                                │
│                                                                     │
│  Would you like me to create a PR to add automatic key rotation?   │
│                                                                     │
│  [Yes, create PR]  [No, just log it]                               │
└─────────────────────────────────────────────────────────────────────┘
```

Implementation: The chat interface uses the Hugging Face Inference API (text generation model) for the Sentinel's reasoning layer. The agent has access to:

· sentinel_events table (read/write)
· generations table (read)
· GitHub API (branch creation, PR creation — via admin's stored PAT in Vault)
· Provider health status

Security: The Sentinel's GitHub PAT is stored in Supabase Vault and scoped to only the repository (not the entire GitHub account). It has contents:write and pull_requests:write permissions — no admin or delete permissions.

---

8. User Interface Framework

8.1 Three Viewport Layouts

The application uses CSS Container Queries + Tailwind CSS breakpoints to adapt across three primary viewport classes:

Viewport Target Devices Layout Strategy Navigation
Wide (≥1280px) Desktop, tablets (landscape) Sidebar + main canvas + properties panel (3-column) Persistent sidebar
Square (768–1279px) Tablets (portrait), foldables 2-column: collapsible sidebar + main canvas Hamburger → overlay
Vertical (<768px) Mobile phones (touch) Single-column: bottom tab bar + full-screen canvas Bottom tab bar (thumb-zone)

8.2 Navigation Tabs

The main application has the following tabs (visible based on role):

Tab Icon Access Content
Studio 🎨 All users Image generation/editing canvas
AI Models 🤖 All users Provider list (Flux, SDXL, Ideogram, etc.) with model selector
Tools 🛠️ All users Post-processing tools (rembg, upscale, vectorize)
History 🕐 All users Past generations with re-edit capability
Admin ⚙️ Admin only Provider management, Sentinel chat, audit logs, settings

AI Models Tab — Content Boundary:
Only providers that support AI image generation APIs appear in the AI Models tab. The following providers are included:

· Flux (FLUX.1-dev, FLUX.1-schnell, FLUX.2)
· Stable Diffusion (SDXL, SD3, SD3.5)
· Ideogram (v2, v3)
· NVIDIA SANA
· Qwen-Image
· HunyuanImage
· Pollinations
· Craiyon
· Custom providers (added via Admin → Add Provider)

Tools Tab — Content Boundary:
Only tools that cannot be exposed as AI APIs appear here:

· Background removal (rembg)
· Vectorization (potrace)
· Upscaling (RealESRGAN)
· Face restoration (CodeFormer)

This separation prevents user confusion — AI models generate images; tools transform existing images.

8.3 UI Design Direction

Based on the design registry, the recommended aesthetic is #47 Neuromorphic Glass / Hybrid Mesh combined with #10 Auroramorphism / Glowmorphism:

Design Token Day Theme Night Theme
Background #F9FAFB #030712
Surface (glass) rgba(255,255,255,0.4) + backdrop-filter: blur(20px) rgba(15,22,36,0.5) + backdrop-filter: blur(25px)
Accent (glow) rgba(0,229,255,0.15) #00F5FF / #FF007F
Border rgba(255,255,255,0.6) rgba(255,255,255,0.08)
Typography Inter / Geist Inter / Geist
Glow Blur filter: blur(80px) filter: blur(80px)

The 3D landing page uses React Three Fiber with a floating glass shader and aurora particle field, transitioning into the Studio on scroll.

---

9. Branding & Design System

9.1 Logo Concept (Round / 1:1 for UI)

Prompt:

A minimalist circular emblem for an AI art studio named "Tasavvur." The icon is an abstract human eye merging with a prismatic glass shard, rendered in holographic chrome and soft aurora gradients (cyan, magenta, deep violet). The pupil is a glowing neural node. Clean vector style, no text inside the circle, transparent background. Ultra HD, 8K, photorealistic glass texture. Square 1:1 composition.

9.2 Banner (21:9) — Website Hero

Prompt:

Ultra-wide cinematic banner for "Tasavvur — Art to Transform the Visualization with Realistic Image." A sweeping dark obsidian landscape with floating glass monoliths refracting aurora neon light. A holographic grid dissolves into a photorealistic rose in the center. Negative space on the left for text overlay. Volumetric lighting, 8K, futuristic, no text. 21:9 aspect ratio.

9.3 Square (4:4 / 1:1) — Social Media Post

Prompt:

A square promotional graphic for Tasavvur. Split composition: left side shows a raw pencil sketch of a warrior; right side shows the same warrior in hyper-realistic 8K cinematic rendering. A glowing glass divider with the Tasavvur logo. Dark gradient background, aurora accents. Ultra HD, modern. 1:1 aspect ratio.

9.4 Portrait (9:16) — Mobile Story / Reel Cover

Prompt:

Vertical 9:16 poster for Tasavvur. A hand reaching toward a floating glass orb containing a miniature photorealistic universe. Holographic light trails wrap around the wrist. Deep space black background with cyan/magenta aurora glow. Top third empty for text overlay. Ultra HD, 8K, cinematic. 9:16 aspect ratio.

9.5 Vertical (3:4) — Print / Flyer

Prompt:

3:4 vertical flyer design for Tasavvur. An exploded-view diagram of a digital canvas: layers of glass, light, and neural nodes floating in zero gravity. Title "Tasavvur" in bold futuristic sans-serif at the bottom. Subtitle "Art to Transform the Visualization." Dark background with subtle grid, holographic foil accents. Ultra HD, modern. 3:4 aspect ratio.

9.6 UI Logo Placement

The round 1:1 logo appears in the following positions:

· Wide layout: Top-left of the sidebar, 40×40px, with "Tasavvur" wordmark to the right.
· Square layout: Center of the top navigation bar, 36×36px, wordmark below.
· Vertical layout: Center of the bottom tab bar's "Home" tab, 32×32px, wordmark in the header.

---

10. Development Roadmap

Phase Duration Milestone Success Criteria
Phase 0: Foundation Week 1 Vercel + Supabase skeleton Auth works; 3D landing page loads; DB schema deployed; RLS enabled
Phase 1: Admin Portal Week 2 Admin login, provider management, Vault integration Admin can add/validate/rotate API keys; credentials changeable
Phase 2: AI Core Week 3–4 ZeroGPU Space + Provider Abstraction Layer Text-to-image and editing work through both ZeroGPU and external APIs
Phase 3: User Studio Week 5–6 3-layout responsive UI, history, downloads User can generate, edit, download; history persists
Phase 4: Sentinel Week 7 Monitoring, diagnosis, healing, PR automation Sentinel detects a simulated failure, creates a PR, logs the event
Phase 5: Tools Tab Week 8 Satellite CPU Spaces (rembg, upscale, vectorize) Tools operational; ZeroGPU quota unaffected
Phase 6: Security Hardening Week 9 CSP, HSTS, rate limiting, audit logs OWASP Top 10 checklist passed; pen-test simulated
Phase 7: Launch Week 10 Public beta <2% error rate; Sentinel chat operational; admin portal stable

---

11. Risk Register & Mitigation

Risk Impact Mitigation
ZeroGPU cold-start exceeds declared duration Request fails mid-inference Test with gradio_client before public launch; set duration=90 for safety
ZeroGPU daily allocation exhausted Service unavailable until reset Per-user daily cap (5 generations); provider failover to external APIs
Supabase connection exhaustion on Vercel 500 errors under load Mandatory: Transaction Pooler on port 6543 for all serverless connections
API key blocked by provider (serverless IP) Provider unavailable Key rotation; Cloudflare proxy; provider diversification (3+ active)
Sentinel creates incorrect PR Broken code merged Sentinel never auto-merges; admin review required for all PRs
Admin credentials compromised Full system access bcrypt hashing; 8-hour session expiry; IP allowlist (optional); audit logging
Supabase Storage bucket fills Uploads fail Client-side compression; purge originals after 7 days; store only final outputs

---

12. Environment Variables Reference

```bash
# ═══════════════════════════════════════════════════════════════
# VERCEL ENVIRONMENT VARIABLES (Production)
# ═══════════════════════════════════════════════════════════════

# Supabase (Transaction Pooler — port 6543)
DATABASE_URL="postgresql://postgres.[ref]:[password]@aws-0-[region].pooler.supabase.com:6543/postgres"

# Supabase Client
NEXT_PUBLIC_SUPABASE_URL="https://[ref].supabase.co"
NEXT_PUBLIC_SUPABASE_ANON_KEY="eyJ..."

# Supabase Service Role (server-side only)
SUPABASE_SERVICE_ROLE_KEY="eyJ..."

# Admin JWT
ADMIN_JWT_SECRET="[random-64-char-string]"

# Hugging Face
HF_TOKEN="hf_..."  # For Space deployment
HF_SPACE_URL="https://[username]-tasavvur-ai-engine.hf.space"

# Sentinel GitHub Access
GITHUB_PAT="ghp_..."  # Stored in Vault; scoped to repo only
GITHUB_REPO="motherskitchenblr2/Qwen-AI--Image-Edit-2509-LoRAs-Fast-Lazy-Load"

# Vercel
VERCEL_TOKEN="..."  # For deployment automation
```

Security Note: All sensitive tokens (GITHUB_PAT, VERCEL_TOKEN, provider API keys) are stored in Supabase Vault, not in Vercel environment variables. Vercel environment variables hold only the Vault access credentials and the Supabase connection strings.

---

End of Document

Document Version: 1.0

Last Updated: 2026-10-27

Prepared For: Tasavvur Development Team

Classification: Internal — Project Blueprint
