# Ninja Brand-Voice SEO Content Pipeline (n8n)

✨ **Intro Description**  
A system for generating and editing articles in your brand voice and author style with SEO rules fully automated in n8n.  
Supports the cycle: Outline → Instructions → Section Writing → Editing/SEO → Export to Google Docs (Markdown).

🧭 **Scheme**  
n8n pipeline: **WF1 (instructions) → WF2 (sections) → WF3 (editor) → WF4 (export)**  
Auxiliary nodes: Brand Pack Retrieval, Style RAG, SEO Linter, Internal Linking, Hallucination Guard

🎧 / 📚 **Samples**  
Before/After (tone/lexicon/rhythm):  
- `samples/before/section-1.md` → `samples/after/section-1.md`  
- `samples/before/section-2.md` → `samples/after/section-2.md`  

SEO scorecard (before/after): `samples/seo/scorecard.json`  
Style score (before/after): `samples/style/style-diff.md`  

📖 **Full Description**  

🧩 **Problem Solved**  
Generic AI texts with no brand voice reduce SEO performance and increase editorial workload.

✅ **Solution & Achievements**  
**Solution:** An n8n pipeline where Ninja + LLM nodes generate content strictly by Brand Pack and author style, with automatic SEO checks and publishing.  

**Key Achievements:**  
- Consistent brand voice and author signature style across all sections.  
- Automatic internal linking, meta title/description, alt-texts.  
- Style/SEO score in JSON for quality tracking and A/B comparison.  
- Full export to Google Docs in Markdown + validation.

🧱 **Workflows (n8n)**  

**WF1 — Heading Instructions**  
Generates clear guidelines per H2/H3 heading based on Brand Pack and section goals.  
**Output:** `instructions.json`

**WF2 — Section Writer**  
Writes sections based on instructions. Integrates Style RAG (author samples), Glossary/Stop-Words, inserts examples/checklists.  
**Output:** `sections/*.md` + `style_score`

**WF3 — Editor/SEO Pass**  
Style Critic + SEO Linter: keyword density, heading hierarchy, internal links, meta, alt-text.  
**Output:** `final/article.md`, `seo_score`, `notes.json`

**WF4 — Export to Google Docs**  
Converts Markdown → Google Doc, attaches metadata, validates lists/anchors.  
**Output:** doc link + publishing log

🧰 **Key Features**  
- **Brand Pack (YAML):** mission, tone, do/don’t, glossary, CTA, internal links  
- **Author Profiles (JSON):** rhythm, sentence length, signature moves  
- **Style RAG:** retrieves author paragraphs into prompts  
- **SEO Rules:** keywords, snippets, structural elements, scorecard  
- **Quality Gates:** style_score, seo_score, hallucination guard  
- n8n full automation with webhooks and manual override

🔨 **Tech Stack**  
- n8n (workflows, triggers, webhooks)  
- Ninja (outline/SEO data and/or text generation via API)  
- LLM Provider (OpenAI/Anthropic – optional editorial steps)  
- Vector DB (Supabase/PGVector/Weaviate) for Style RAG  
- Google Docs API (Markdown export)  
- Node.js / Python helper scripts (linter, converters)

📇 **Repository Structure**

```
ninja-brand-voice-seo-pipeline/
├─ .github/
│  └─ workflows/ci.yml
├─ n8n/
│  ├─ wf1_heading_instructions.json
│  ├─ wf2_section_writer.json
│  ├─ wf3_editor_seo.json
│  └─ wf4_export_gdocs.json
├─ brand/
│  ├─ brand_pack.yaml
│  ├─ glossary.csv
│  └─ internal_links.yaml
├─ authors/
│  ├─ maria_profile.json
│  ├─ ivan_profile.json
│  └─ corpus/
├─ samples/
│  ├─ before/
│  ├─ after/
│  └─ seo/
├─ img/
│  └─ scheme.png
├─ scripts/
│  ├─ markdown_validator.js
│  ├─ internal_linker.py
│  └─ scorecard_merge.py
├─ config/
│  ├─ env.example
│  └─ seo_rules.json
├─ docs/
│  ├─ API-Ninja.md
│  ├─ Style-RAG.md
│  └─ SEO-Checklist.md
└─ README.md
```

⚙️ **Setup**  

**Prerequisites**  
- n8n ≥ 1.70  
- Node.js ≥ 18  
- Access to Ninja API  
- Google Cloud project with Docs API  
- (Optional) Postgres/Supabase with pgvector  

**Env**  
`config/env.example` → `.env`

```
NINJA_API_KEY=...
OPENAI_API_KEY=...
ANTHROPIC_API_KEY=...
GOOGLE_SERVICE_ACCOUNT_JSON=./config/gservice.json
VECTOR_DB_URL=...
VECTOR_DB_TOKEN=...
```

**Install & Run**

```bash
npm i
# import workflows from /n8n into your n8n instance
# load brand_pack.yaml, profiles and corpus into nodes
```

🧩 **Configuration**  

**Brand Pack (YAML)**

```yaml
brand:
  mission: "Make complex things clear and practical."
  tone: ["confident","friendly","no hype"]
  do:
    - "short sentences; takeaway → explanation"
    - "active voice; concrete examples"
  dont:
    - "avoid empty superlatives; avoid unsupported promises"
glossary:
  - term: "workflow"
    prefer: "workflow"
    avoid: ["pipeline"]
internal_links:
  - anchor: "n8n Setup Guide"
    url: "/blog/n8n-setup"
```

**Author Profile (JSON)**

```json
{
  "author": "Maria Ivanova",
  "sentence_length": "10-18",
  "cadence": "thesis → reason → example",
  "diction": ["simple verbs","minimal adjectives"],
  "signature_moves": ["starts with thesis","ends with mini-checklist"]
}
```

**SEO Rules (JSON)**

```json
{
  "h2_max": 8,
  "h3_max": 24,
  "keywords": [{"kw":"n8n workflows","target_density":0.8}],
  "internal_linking": true,
  "require_meta": true,
  "require_alt": true
}
```

🔌 **Integrations**  

**Ninja API (example call)**  
See `docs/API-Ninja.md`. Example n8n HTTP Request node:

```
POST /content/generate
Body: outline, section_id, style_context (Brand Pack + Author Profile + RAG paragraphs), seo_rules
Response: { markdown, style_score, seo_score, notes[] }
```

**Google Docs Export**  
WF4 converts `final/article.md` → Doc, attaches metadata, returns share link.

🧪 **Quality Gates**  
- `style_score` (0–1) — match with author profile  
- `seo_score` (0–100) — keywords, structure, meta, links  
- `hallucination_notes[]` — source requests: `<!-- NEED_SOURCE: ... -->`

📌 **Usage**  
1. Place outline in `inputs/outline.md` or via Webhook.  
2. Run WF1 → `instructions.json`  
3. Run WF2 → `sections/*.md` + style scores  
4. Run WF3 → `final/article.md` + SEO score  
5. Run WF4 → Google Doc + log

📚 **References**  
- Ninja (official site + API)  
- n8n HTTP Request and Credentials docs  
- SEO content structure best practices

🗺️ **Roadmap**  
- SERP-feature templates (FAQPage/HowTo)  
- Multilingual brand packs  
- Auto-image generation + alt text  
- CMS export (WordPress/Headless)

🔒 **Security**  
Store keys (Ninja/LLM/Google) in n8n Credentials. Do not commit secrets.
