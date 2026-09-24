# TheoryA.ai working context

Context for any Claude working on the TheoryA.ai website and published material.

## The firm

TheoryA.ai. Two founders: **Jim Scully** and **Emily Liddle**, both Co-Founder.
Theory A is a management philosophy of organizational design for the age of AI.
The firm works on **corporate functions**, meaning HR, IT, Finance, Procurement,
Legal, and the shared services that deliver them, rather than enterprise-wide
change (Language Standard LS-049).

## The site

- Repo: `github.com/emliddle/theorya-website`, branch `main`
- Hosting: Cloudflare Pages, deploys automatically on push to `main`
- Static HTML and CSS only. No build step, no framework, no shared stylesheet.
  Every page carries its own inline `<style>` block.

### Working directory

Clone into a dedicated directory that contains nothing but the site, for example
`~/theorya-website`.

Do not clone into a folder that holds unrelated files. A previous setup used the
Downloads folder as the repo root, which meant every downloaded file appeared as
untracked, and a single `git add -A` once staged 3.5GB of personal material and
broke the push. That configuration also forced a whitelist-style `.gitignore` that
ignores everything and allows only named folders back in.

The whitelist `.gitignore` was replaced with a conventional one in September 2026.

### Structure

```
index.html          homepage (ends on Work With Us, #contact)
people/             founders page, linked from the top nav
llms.txt            summary of the site for AI tools, update when pages are added
theory/             "Why Theory A Exists"
manifesto/          "Organizing Agency: The Theory A Manifesto"
signals/            index + one directory per Signal
provocations/       one directory per provocation + a .pdf per provocation
oai/                Organizational Agency Index landing page
images/             founder headshots
_headers            Cloudflare headers, sets PDF download filenames
sitemap.xml         every public page, maintained by hand
robots.txt          points at the sitemap
CNAME               custom domain
.gitignore          conventional; ignores source documents and "Claude outputs/"
```

### Content types

- **Signals**: short essays, one per directory under `signals/`. Author is Jim or Emily.
- **Provocations**: long-form papers, one per Collection. HTML page plus a
  downloadable PDF at `provocations/<slug>.pdf`.
- **Collections**: The AI Enterprise (Jim), AI Native Shared Services (Jim),
  Design for Agency (Emily).

## Standards

The canonical standards are living documents in Google Drive. Read them fresh at
the start of any content task rather than working from a cached summary, including
the summary below. Titles, not stored file IDs: the IDs change between versions.

- **Language Standard**: the canonical dictionary. Terminology, capitalization,
  prohibited terms, tone.
- **Design Standard**: colour tokens, typography, layout.
- **Content Classification Framework**: Zone 1 public, Zone 2 protected Collection
  IP, Zone 3 internal only.
- **Publication Standard**: final checklist before anything ships.

This requires a Google Drive connector. If none is available in the environment,
say so rather than proceeding on the summary in this file.

A `theory-a-brand-voice` skill exists that points at these documents. It may or may
not be installed in a given environment.

### Standing rules

- **No em dashes** in Emily's writing or on site pages, including this file. Use
  commas or full stops.
- **No dates** on Signals. No byline dates, no version numbers. A dated piece starts
  ageing the day after it publishes.
- **Method note** on every Signal and provocation:
  *"Authored by [Name] and drafted with AI as a thinking partner and adversarial
  reviewer. The argument and the final judgment are [his/hers]."* linked to
  `/#how-we-work`. It is a method note, not a disclaimer. It states who is
  responsible rather than distancing them from the work.
- Voice is **declarative, precise, calm, thought-provoking**. Not hedged, not
  general, not breathless, not reassuring. If the language could belong to any
  consulting firm, it is not sharp enough.
- "Theory A" is always two words, both capitalised. "TheoryA.ai" has no space and a
  lowercase ai. "Collection" is always capitalised.
- "Transformation" and "transform" are use-with-care in the Language Standard: flag each use and prefer the specific change. "AI Native" has no hyphen.

## Design tokens

Source of truth is the Design Standard (v0.4, long-form and web register). The
People page carries these; every new or rebuilt page must match them. The homepage
redesign in these tokens is in progress on the local branch `homepage-redesign` and
is not live; the live homepage still uses the old tokens until Emily approves it.

```
--ink     #171512    headlines, wordmark, strong rules, dark bands
--body    #241f19    all reading text
--muted   #6d675e    eyebrows, labels, bylines, captions, meta
--rule    #cfc9be    thin dividers
--soft    #e2ddd3    soft dividers
--panel   #e9e5dd    table label column
--paper   #f3f1ec    page background, with a faint paper grain
```

Type: **Georgia** for headlines and all reading text, 18px body, line-height 1.8.
Helvetica Neue / Arial for labels, eyebrows, nav, and buttons only. No Newsreader,
no third typeface. Monochrome only, no accent color. Corners 0 to 2px, no shadows.

Homepage redesign layout (branch `homepage-redesign`): a three-column grid (section number in the left margin, reading
column, right margin for Manifesto quotes or indexes). The Principles section and the
Work With Us close are dark bands (#171512). Do not add "Figure N" labels.

Known drift: index.html, theory/, manifesto/, signals/, provocations/, and oai/ still use the old
tokens (#fafafa background, Newsreader or Georgia on older values). They are due to be
migrated to the tokens above.

Homepage copy rule from Emily: no setup-then-reversal constructions ("X is not Y.
It is Z.", "Theory A is public. The expertise isn't.") and no two to four word
sentences or fragments. Write the fuller descriptive sentence instead.

## Publishing a Signal, all three steps

Missing any one of these leaves the library inconsistent.

1. Create `signals/<slug>/index.html`. Build it by copying a recent Signal and
   swapping the content. Never hand-write the page chrome.
2. Add a card to `signals/index.html`, newest first.
3. Add a `<url>` entry to `sitemap.xml`.
4. Add a line to `llms.txt` under Signals.

Then verify: card count equals directory count, every directory appears in the
sitemap, and the XML parses.

Drafts often arrive carrying their own chrome. Strip: dates from bylines, section
numbers (that is a provocation device, not a Signal one), Google Docs bold residue
(bold that starts mid-word is edit history, not emphasis), and any method note
already in the body, since the template supplies it.

## Gotchas that have actually bitten

- **CSS cascade.** Media queries add no specificity. At equal specificity the later
  rule wins, so responsive overrides must appear after the base rules in source
  order. This has broken the layout twice.
- **Cloudflare CDN caching.** After pushing, a plain URL may serve the old version.
  Append a query string such as `?v=2` to check. This has produced false conclusions
  that a deploy failed.
- **Read-only file permissions.** Files copied from a read-only source keep that mode
  and cannot be overwritten on the next update. Run `chmod 644` after copying,
  particularly for the provocation PDFs.
- **Full-bleed backgrounds** require the element to sit outside `.container` with its
  own inner container.
- **Concurrent git access.** If two processes touch the repo at once, one can leave a
  stale `.git/index.lock` that blocks all subsequent git commands. Remove the lock
  file to clear it.
- **Never embed a credential in the git remote URL.** Use a credential helper or SSH.
  A token in `.git/config` travels with any copy of the folder.

## Third-party embed

The Organizational Agency Index page at `/oai/` is a landing page that hosts a
GaugeWright chat embed: script from `embed.gaugewright.com`, panel host
`panels.gaugewright.com/d/<slug>`. The page is provisional and has been used to test
the embed before the real agent is ready.

Two things established by inspection:

- The widget does **not** use shadow DOM, so site CSS can style its internals
  directly.
- It gates on Cloudflare Turnstile. The embedding domain must be on the allowed
  hostnames for that deployment's Turnstile site key, or it fails with client error
  110200 and a 428 from `/bootstrap`. That is a vendor-side setting.

GaugeWright contact is Jack Scully.

## How to work with Emily

- Conversational and direct. No consultant-deck formatting, no heavy bullet lists
  where prose will do.
- Challenge the thinking. Say what does not hold up rather than agreeing.
- Flag confidence: certain, likely, or guessing.
- Surface the thing she has not asked about if it matters.
- Execute technical work directly whenever the available tools permit it. Inspect and
  modify site files, run the appropriate commands, verify the result, and use the
  established GitHub and deployment workflow. Do not hand Emily manual Terminal, git,
  GitHub, Cloudflare, HTML or CSS steps for work that can be performed directly.
  Bring her in only when authentication or permissions require her action, when an
  operation is consequential enough to need explicit approval, or when a substantive
  content, design or business decision is needed.
- Verify rather than assert. Check tag balance, link targets, card and sitemap parity,
  and the live page after deploying.
- Build previews as standalone HTML files she can open, rather than describing visual
  options in words.

## Anchors other pages depend on

Every Signal links to `/#how-we-work`, and every page's nav links to `/#works`. Keep
both ids on the homepage. The People nav item goes to `/people/`.

## Organizational Agency Index page

`/oai/` stays unlinked from the nav and footer, noindex, and out of the sitemap and
llms.txt until Emily decides to activate it (held September 2026).
