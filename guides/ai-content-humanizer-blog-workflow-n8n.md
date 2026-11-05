# AI Content Humanizer for Blog Posts with n8n and Claude | Remove AI Writing Patterns Without Breaking SEO

**Workflow file:** [`ai-content-humanizer-blog-workflow-n8n.json`](../ai-content-humanizer-blog-workflow-n8n.json)

This n8n workflow takes a blog post sent to a webhook and rewrites it so it no longer reads as AI-generated. It uses the [content-humanizer skill](https://github.com/Matt-Payne/content-humanizer), a catalog of 33 AI writing patterns based on Wikipedia's "Signs of AI writing" guide, with rules for marketing copy so the post still sells and still ranks. Claude does the work in three passes (draft, audit, final). The workflow then checks that the headings, links, target keyword, images, and code all survived, and sends the final pass back for another try when they didn't. The result comes back as JSON and, optionally, as a new Google Doc.

---

## What it's used for

- **Cleaning AI drafts before they publish.** Send any AI-assisted post from your CMS, Zapier, or a script and get a version without the tells: em dashes, "a testament to," rule-of-three lists, "in today's evolving landscape," generic conclusions.
- **Keeping the SEO you paid for.** The primary keyword stays in the first 100 words and in a subheading, every heading keeps its level and order, and every link keeps its target. All of this is checked in code, not left to trust.
- **Never inventing facts.** A separate audit pass lists any fact, number, name, or quote in the draft that wasn't in the original, and the final pass removes them.
- **Matching a voice.** Pass a writing sample and the rewrite follows its sentence length, vocabulary, and punctuation habits.

## At a glance

| | |
|---|---|
| **Trigger** | Webhook, `POST /webhook/humanize-blog`, protected by a header API key |
| **Input** | HTML, Markdown, or plain text (format is detected automatically) |
| **AI** | Claude Opus 5.5 (`claude-opus-5-5`) through the Anthropic Messages API, three calls per post plus up to two retries |
| **Skill** | content-humanizer v2.12.1: `SKILL.md` and all five reference files, embedded in the *Humanizer Skill* node and sent as a cached system prompt |
| **Checks** | Protected content, heading structure, links, keyword placement, length, em/en dashes |
| **Output** | JSON response (or a POST to `callback_url`), plus an optional Google Doc |
| **Credentials** | Anthropic API key, a webhook API key, and Google Drive (only if you want Docs) |

---

## How it works

### Step 1: Validate and protect the post

**Validate Input** checks the request, detects the format, and holds the workflow settings in a `CONFIG` block at the top. Bad requests get a `400` with a list of errors.

**Protect Structure** swaps anything the rewrite must not touch for tokens like `[[KEEP_3]]`. That covers code blocks, inline code, images (with their alt text), tables, figures, iframes, embeds, WordPress block comments, shortcodes, and YAML frontmatter. Claude sees a short preview of each token so it can still read the sentence around it. The node also records the post's headings, link targets, word count, and where the primary keyword appears.

### Step 2: Draft

Claude reads the full skill and the post, finds the AI patterns, and writes a draft rewrite (steps 1 and 2 of the skill's Process).

### Step 3: Audit

Claude answers the skill's two audit questions about the draft, returned as structured JSON:

- `still_ai`: what still reads as AI, with the pattern number (for example `§9`) and the phrase
- `fabrications`: any fact in the draft that isn't in the original
- `seo_issues`: broken keyword placement, headings, links, images, or CTA
- `content_type`: whether it treated the post as marketing or reference copy

### Step 4: Final, with an integrity check

Claude revises the draft using the audit. **Integrity Check** then compares the final rewrite to the original:

| Check | Fails when |
|---|---|
| Protected tokens | A token is missing, duplicated, or invented |
| Headings | The heading levels or their order changed |
| Links | Any original link target is gone |
| Primary keyword | It disappeared, or left the first 100 words or the subheadings when the original had it there |
| Length | The rewrite is under 50% of the original word count |
| Dashes | Any em or en dash remains (skipped when your voice sample uses them) |

When a check fails, the final pass runs again. Claude gets its previous attempt and the exact problems to fix. After the last attempt (3 by default), leftover dashes are replaced mechanically, and any other remaining issue comes back with `status: "needs_review"` so a person can look.

### Step 5: Google Doc and response

If `create_google_doc` is on, the rewrite is uploaded to Google Drive and converted to a Google Doc. HTML and Markdown keep their headings, lists, and links. The Doc is named `<title> (humanized)` and goes in your chosen folder.

The JSON result goes back to the caller. When the request includes a `callback_url`, the webhook answers `202` straight away and the result is POSTed to that URL when it's ready.

---

## Setup

### 1. Import the workflow

In n8n, go to **Workflows → Import from File** and pick `ai-content-humanizer-blog-workflow-n8n.json`.

### 2. Connect credentials

| Node(s) | Credential | Setup |
|---|---|---|
| Blog Webhook | Header Auth, named `Humanizer API Key` | Name: `X-API-Key` (or any header you like). Value: a long random string your callers will send. |
| Claude: Draft, Claude: Audit, Claude: Final | Header Auth, named `Anthropic API Key (x-api-key)` | Name: `x-api-key`. Value: your Anthropic API key from [platform.claude.com](https://platform.claude.com). Create it once and select it on all three nodes. |
| Upload to Google Docs | Google Drive OAuth2 | Any Google Drive credential with access to the target folder. |

The Claude nodes call the Messages API directly with an HTTP Request node. That keeps control over prompt caching, structured output, effort, and API version headers, which n8n's built-in LLM chain nodes don't expose.

### 3. Adjust settings

Open **Validate Input** and edit `CONFIG`:

| Setting | Default | What it does |
|---|---|---|
| `model` | `claude-opus-5-5` | Model for all three passes. `claude-sonnet-5-5` is about half the price. |
| `draft_effort` / `audit_effort` / `final_effort` | `high` / `medium` / `high` | How hard Claude thinks on each pass. |
| `max_tokens` | `32000` | Per call, including thinking. Raise it for very long posts. |
| `max_final_attempts` | `3` | Final pass attempts before returning `needs_review`. |
| `create_google_doc_default` | `true` | Used when a request doesn't set `create_google_doc`. |
| `google_folder_id` | blank | Drive folder ID for new Docs. Blank means My Drive root. |
| `min_length_ratio` | `0.5` | Minimum final-to-original word ratio before the length check fails. |

### 4. Test it

Activate the workflow (or use the test URL) and send a post:

```bash
curl -X POST https://YOUR-N8N/webhook/humanize-blog \
  -H "X-API-Key: YOUR_WEBHOOK_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "What Is Intelligent Document Processing?",
    "content": "<p>Intelligent document processing stands as a pivotal ...</p><h2>How it works</h2><p>...</p>",
    "target_keyword": "intelligent document processing",
    "create_google_doc": true
  }'
```

---

## Request reference

| Field | Required | Description |
|---|---|---|
| `content` | Yes | The post body: HTML, Markdown, or plain text. |
| `title` | No | Used for the Doc name and as context. The title itself isn't rewritten. |
| `format` | No | `auto` (default), `html`, `markdown`, or `text`. |
| `content_type` | No | `auto` (default), `marketing`, or `reference`. Marketing mode keeps the persuasion, CTA, and SEO structure. |
| `target_keyword` | No | Primary keyword. Turns on the keyword checks. |
| `secondary_keywords` | No | Array or comma-separated string. Kept where they read naturally. |
| `voice_sample` | No | A sample of the author's own writing to match. |
| `notes` | No | Editor or brand notes, such as "keep it first person" or "US spelling". |
| `create_google_doc` | No | `true` / `false`. Overrides the default. |
| `google_folder_id` | No | Overrides the Drive folder for this request. |
| `include_draft` | No | Adds the draft rewrite to the response. |
| `callback_url` | No | Answer `202` now and POST the result here when done. |

## Response reference

```json
{
  "status": "ok",
  "title": "What Is Intelligent Document Processing?",
  "format": "html",
  "content_type": "marketing",
  "humanized_content": "<p>...</p>",
  "integrity": { "passed": true, "issues": [], "fixes_applied": [], "final_attempts": 1 },
  "draft_audit": { "still_ai": ["§9 \"not just X, but Y\" in the intro"], "fabrications": [], "seo_issues": [] },
  "stats": { "words_original": 1840, "words_final": 1610, "length_ratio": 0.88, "headings": 7, "links": 12, "protected_items": 5, "dashes_original": 23, "dashes_final": 0, "keyword_mentions_original": 6, "keyword_mentions_final": 6 },
  "google_doc": { "id": "1AbC...", "name": "What Is Intelligent Document Processing? (humanized)", "url": "https://docs.google.com/document/d/1AbC.../edit" },
  "model": "claude-opus-5-5",
  "skill_version": "2.12.1",
  "usage": { "calls": 3, "input_tokens": 9100, "output_tokens": 14200, "cache_creation_input_tokens": 13800, "cache_read_input_tokens": 27600, "by_step": [] },
  "execution_id": "1234",
  "completed_at": "2026-11-05T15:04:05.000Z"
}
```

`status` is `needs_review` when the integrity check still had issues after the last attempt. The rewrite is still returned, and `integrity.issues` lists what's wrong. `draft_audit` describes the draft, not the final, so it shows what the final pass was asked to fix.

---

## Cost and speed

The skill is about 13,000 tokens. It's sent as a cached system prompt, so only the first call per post pays full price for it, and later calls read it from cache at a fraction of the cost. Each post takes at least three calls, and a long post at high effort can take several minutes. The `usage` field in the response reports the real token counts, including cache reads.

For posts that take longer than your caller's HTTP timeout, use `callback_url`.

## Updating the skill

The *Humanizer Skill* node holds the full text of `SKILL.md` and `references/*.md` from content-humanizer v2.12.1. When the skill changes, paste the new text into the `SYSTEM_PROMPT` constant, keeping the reference files wrapped in `<reference path="...">` tags, and update `SKILL_VERSION`. The "Running inside an n8n pipeline" section at the end of the prompt explains the token and output rules to Claude. Keep it.

## Troubleshooting

| Problem | Fix |
|---|---|
| `401` from the Claude nodes | The Header Auth credential name must be exactly `x-api-key`. |
| `400` mentioning `fallbacks` or `output_config` | Check the `anthropic-version` and `anthropic-beta` headers on the Claude nodes weren't removed. |
| "hit max_tokens" error | Raise `max_tokens` in `CONFIG`, or lower the effort settings. |
| Caller times out | Send a `callback_url`, or raise the timeout on your client and on any proxy in front of n8n. |
| `needs_review` with a heading issue | Usually a post with headings in odd places, such as inside a protected table. Read `integrity.issues` and fix it by hand, or raise `max_final_attempts`. |
| `google_doc.error` in the response | The Drive credential can't write to `google_folder_id`, or the folder ID is wrong. The rewrite is still returned. |
| Tables weren't humanized | Tables are protected on purpose, so their data can't be changed. Remove `table` from the protect list in **Protect Structure** if you want them rewritten. |
