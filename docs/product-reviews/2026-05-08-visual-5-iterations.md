# EAI2 Product Review — Visual 5-Iteration Run

Date: 2026-05-08  
Stage: MVP  
Focus: remove generic AI-template styling and improve technical publication credibility

## Issue Being Answered

The current homepage had enough product substance, but the visual system still read too much like an AI-generated technology template: bright cyan gradients, decorative grids, large rounded cards, heavy soft shadows, and overdesigned topic badges.

## Five Iterations Completed

1. **Palette restraint:** shifted the homepage from bright blue/cyan to a quieter off-white, charcoal, and muted green system.
2. **Hero reset:** changed the hero from a slogan-heavy "future of robots" treatment to `Embodied AI field guide`, with tighter copy and fewer CTAs.
3. **Card discipline:** removed large shadows and heavy radius from homepage panels, topic cards, trust cards, newsletter cards, and related cards.
4. **Information density:** converted the knowledge map and latest notes blocks into bordered table/list surfaces instead of floating cards.
5. **Article/page consistency:** quieted article hero styling and newsletter/regions surfaces so downstream pages match the more editorial homepage tone.

## Product Judgment

Verdict: materially improved visual credibility for MVP validation.

The site now reads closer to a technical briefing / field guide than a promotional AI landing page. The robot image still gives domain context, but it is visually subdued. The remaining risk is that the theme still carries legacy bright category visuals in article lists and some older components; this run focused on the highest-visibility homepage and article surface first.

## Rendered Verification

Local rendered checks passed:

- `hugo --minify`: passed, 206 pages.
- Homepage desktop screenshot: no horizontal overflow, H1 renders as `Embodied AI field guide.`
- Homepage mobile screenshot: no horizontal overflow at 390px width.
- Newsletter page: no horizontal overflow.
- Edge AI article page: no horizontal overflow.

## Next Visual Move

If another visual pass is needed, the next highest-leverage move is to refactor post-list cards and category color gradients. Those still carry more template-like color treatment than the new homepage system.
