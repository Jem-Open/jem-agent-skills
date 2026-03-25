---
name: jem-brand-guidelines
description: >
  Jem brand guidelines — the single source of truth for colours, typography, tone of voice, logo
  usage, iconography, and visual identity. Use this skill whenever creating any content, collateral,
  code, or communication that needs to be on-brand for Jem. Trigger when someone asks to "check brand
  guidelines", "use Jem colours", "write in Jem's voice", "what font does Jem use", "stay on brand",
  or any request involving Jem's visual identity, tone, or brand standards. Also use when reviewing
  content for brand consistency, building branded templates, choosing colours or fonts for Jem assets,
  or when another skill needs brand context. This is a reference skill — it provides the rules, not
  the execution. For creating visual collateral, use the jem-collateral skill. For reviewing content
  voice, use the brand-voice skill.
---

# Jem Brand Guidelines

These guidelines define how Jem looks, sounds, and feels across every touchpoint. Internalise them
before producing any branded output.

## Who Jem is

Jem builds the ultimate HR system for frontline employees. Everything is delivered via WhatsApp —
no apps to download, no training required. Jem serves 240,000+ deskless workers across South Africa
through HR, Communications, and Benefits products.

Backed by Old Mutual + NEXT176.

## Mission

Build the ultimate HR and financial services platform for deskless workers.

## Brand Values

Four values underpin everything:

**Human** — We leverage familiar tools like WhatsApp to give every interaction a personal touch,
ensuring our solutions are as intuitive and comfortable as they are cutting-edge.

**Innovative** — We never stop pushing the boundaries of HR technology, boosting engagement and
giving our customers the best possible experience.

**Community** — We build strong relationships at every level — from within our own teams to the
employees we serve and the entire ecosystem of HR professionals.

**Trustworthy** — We provide consistent, dependable services that HR teams and employees can trust.

## Brand Personality

Four traits that should come through in every piece of work:

**Conversational** — Engage with a warm and welcoming tone, using language our customers use.
Interact like you're having a conversation with a friend, without being unprofessional.
*Linked to the Human value.*

**Adaptable** — Tailor communication to fit the context and platform. High-level in a blog post,
detailed in a white paper. Showing flexibility demonstrates innovation.
*Linked to the Innovative value.*

**Reliable** — Always provide clear, consistent, and accurate information. Check comms for factual
and practical reliability before sharing. Being the voice of the deskless means taking responsibility.
*Linked to the Trustworthy value.*

**Energetic** — Use active language that inspires and motivates. Enthusiasm is contagious; we bring
positive energy to every interaction.
*Linked to the Community value.*

## Brand Purpose: Voice of the Deskless

We build emotional connections by being the voice for the deskless workforce. Casting a spotlight
on their realities, broadcasting their experiences and celebrating their importance.

Our communication is direct and unapologetic, capturing the true essence of their daily lives.

## Tone of Voice — Quick Reference

- Short, punchy sentences. Active voice.
- Warm and direct — like explaining something to a friend
- No jargon, no corporate buzzwords
- South African English (organisation, colour, programme — not US spelling)
- Numbers and stats are powerful — use them when available
- Questions make great headlines

## Visual Identity — Quick Reference

For full specifications (hex codes, CMYK, font weights, type scale), read `references/visual-spec.md`.

### Colour palette (70/20/10 rule)

| Role | Colour | Digital hex | Usage ratio |
|------|--------|-------------|-------------|
| Background | Cream | `#FFF7EC` | 70% |
| Text & structure | Navy | `#062133` | 20% |
| Accent & CTAs | Jem Pink | `#FF697F` | 10% |

Cream dominates, navy grounds everything, pink draws the eye to what matters most.

### Typography

| Role | Font | Weight |
|------|------|--------|
| Headlines & titles | Inter | Semi Bold (600) or Bold (700) |
| Descriptors & labels | Inter | Medium (500), 2px letter-spacing, uppercase |
| Body copy | Manrope | Regular (400) or Light (300) |

Both are free on Google Fonts. If Manrope isn't available, Inter Regular can substitute for body copy.

### Logo

The Jem logo is lowercase "jem" in a pink rounded-rectangle pill. Three versions exist:
1. **Primary (V1)** — Pink pill with white text. Default for most use cases and all digital.
2. **Solid background (V2)** — White text only, no pill. For solid Jem Pink panels only. Very selective use.
3. **Social media (V3)** — Pink circle with white text. For profile pictures and circular crops.

Logo variations (white pill on navy, pink text on white, etc.) exist for rare cases where the primary doesn't suit the substrate.

### Iconography

Jem has a custom icon library in two colourways: Navy background (cream circle) and Cream background
(navy circle). Icons use pink as the primary detail colour with navy/cream accents.

For the full icon listing, read `references/icon-manifest.md`.

## Writing Rules

### Capitalisation
- "Jem" — always capital J, lowercase em
- Product names: Jem Mobile, Jem Savings, Jem EWA, Jem EAP, Jem HR, Jem Comms, Jem T&A, Jem Hub
- Never write "JEM" in all caps

### Language preferences
- South African English throughout (colour, organisation, programme, recognise, etc.)
- "Deskless workers" or "frontline employees" — not "blue-collar" or "non-desk"
- "WhatsApp" — always capitalised with capital A
- "Employers" and "employees" — Jem serves both audiences

### What to avoid
- Corporate buzzwords: "leverage synergies", "paradigm shift", "holistic ecosystem"
- Overly technical language unless the audience is developers
- Passive voice (prefer "Jem sends payslips" over "Payslips are sent by Jem")
- Overpromising — be confident but honest

## Downloadable Assets

The skill includes ready-to-use brand assets in the `assets/` folder:

- **`assets/logos/jem-logo.svg`** — Primary logo (SVG, scalable)
- **`assets/logos/Jem_logo_2024.ai`** — Logo source file (Adobe Illustrator)
- **Icon library** — Available in the shared `Icon Library/` folder in two colourways (Navy and Cream). See `references/icon-manifest.md` for the full listing.

The brand guidelines microsite at https://jem-brand-guidelines.lovable.app/ also has downloadable fonts, logos, and the full icon library.

## When to read the reference files

| You need... | Read... |
|-------------|---------|
| Exact hex codes, CMYK values, Pantone refs, print vs digital colours | `references/visual-spec.md` |
| Full type scale (H1–H6 sizes, line heights, letter spacing) | `references/visual-spec.md` |
| Secondary colour palette with all 8 accent colours | `references/visual-spec.md` |
| Logo CSS implementation or clear-space rules | `references/visual-spec.md` |
| Which icon to use for a specific concept | `references/icon-manifest.md` |
| Icon filenames and paths for both colourways | `references/icon-manifest.md` |
