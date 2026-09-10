## Syamjith NK

Cinematographer and AI creative technologist in Abu Dhabi. I work where film craft meets applied
AI, and I publish open evaluation of the Arabic tooling that production depends on.

**Checkable in ten seconds, without taking my word for anything:**

| | |
|---|---|
| 🟣 **Merged into matplotlib** | [#32263](https://github.com/matplotlib/matplotlib/pull/32263), 4 Sep 2026. A documentation change, thirty lines: the upgrade guide now tells 3.11 upgraders to remove the Arabic workaround. The research behind it is the substance, not the diff. |
| ✅ **Accepted answer, 15,226 views** | [Matplotlib: Writing right-to-left text](https://stackoverflow.com/a/80001368) — the canonical question, asked thirteen years ago. Every prior answer predates 3.11 and now reverses your text. |
| 📦 **`pip install arabic-lint`** | [PyPI](https://pypi.org/project/arabic-lint/) — finds Arabic corrupted before it was stored, and now the source code that will corrupt it at render time. Zero dependencies, CI-ready. |
| 📊 **Four open datasets** | [huggingface.co/syamjithnk](https://huggingface.co/syamjithnk) — CC BY 4.0, test sets, scorers and raw results included. |
| 🔬 **Corpus audit: 341 Arabic datasets** | [arabic-corpus-audit](https://huggingface.co/datasets/syamjithnk/arabic-corpus-audit) — 276 readable, 119,517 text fields. One is 100% corrupted, and it is an OCR ground-truth set. |

Full write-up with the measurements: **[syamjithnk.com/evidence](https://syamjithnk.com/evidence)**

### Arabic breaks silently — three benchmarks

Each measures a failure that looks correct to anyone who does not read Arabic, which is exactly
why it ships. All CC BY 4.0, with the test set, the scorer, the raw per-item results, and a plain
statement of what the measurement does **not** establish.

| | finding |
|---|---|
| **[arnum-tts](https://github.com/Syamjith-NK/arnum-tts)** | The same Arabic sentence with Arabic-Indic digits (٢٠٢٦) instead of Western (2026) drops from **73% to 7%** intelligible on one engine. Another handles both at 80% — a missing normalisation step, not a hard problem. |
| **[arshape](https://github.com/Syamjith-NK/arshape)** | The `arabic_reshaper` + `python-bidi` recipe recommended in nearly every tutorial **corrupts** Arabic on any renderer that already shapes: 15/15 correct without it, 0/15 with it. |
| **[arpdf](https://github.com/Syamjith-NK/arpdf)** | Reversed, ligature-mangled and space-collapsed are three distinct failure modes that a single pass/fail test collapses into one. |

Datasets: **[huggingface.co/syamjithnk](https://huggingface.co/syamjithnk)**

### The fix, not just the finding

The numeral failure above is now a package anyone can install:

```
pip install arabic-tts-frontend
```

**[arabic-tts-frontend](https://github.com/Syamjith-NK/arabic-tts-frontend)** ·
[PyPI](https://pypi.org/project/arabic-tts-frontend/) — numerals, dates, currency, times and
percentages converted to spoken Arabic before the text reaches a TTS engine. MIT, zero
dependencies. Re-measured against the released build on the same 45 sentences, same engine and
scorer: **Arabic-Indic digits 0/15 → 11/15**. The scorer ships with it, so the figures can be
re-derived rather than trusted, and the release notes state the run-to-run noise (±3) instead of
quoting the overall number precisely.

### Writing

**[The Arabic fix everyone recommends is now the bug](https://syamjith-nk.github.io/arabic-reshape-bidi-is-now-the-bug/)**
— the `get_display(reshape(...))` recipe is in 1,180 indexed files on GitHub and now
renders Arabic backwards on matplotlib 3.11, silently. Why the shaping half is
detectable and the reordering half is not, and what happened when I filed it upstream.

More at **[syamjith-nk.github.io](https://syamjith-nk.github.io)**.

### Upstream

Filing the finding where the bug lives has reached far more people than publishing it ever did.

**[matplotlib](https://github.com/matplotlib/matplotlib/issues/32262)** — matplotlib 3.11 shapes
Arabic itself, so the reshape+bidi recipe now runs twice and renders the label reversed. Nothing
raises; it looks like Arabic to anyone who cannot read it. Measured as mean absolute pixel
difference against a reference render: **7.75** for the raw logical string (antialiasing only),
**76.28** pre-shaped. Three maintainers replied within two days, including the project lead — and
one of them went and **edited a StackOverflow answer** to say the workaround is only for older
versions ([q47057509](https://stackoverflow.com/q/47057509), 30 August 2026). To be exact: that
happened on one of the two big questions, not both. The canonical one still carried the pre-3.11
advice, so I answered it there myself, and the asker accepted it. That is the widest-reaching
outcome of any of this and it cost nothing but filing accurately. Docs [PR #32263](https://github.com/matplotlib/matplotlib/pull/32263) was **merged on 4 September 2026**.

The project lead proposed a fix I had not thought of — wrapping pre-processed text in Unicode
LRO/PDF — and said he was not confident it was right for Arabic. I could check, so I did: across
three fonts and nine strings it reads correctly in all of them, but does not always *render*
identically, because the wrapped string draws the font's static presentation forms instead of the
font's own shaping. That exchange is the argument for filing upstream rather than blogging first.
He had the better fix; I had the only way to verify it.

**[Pillow](https://github.com/python-pillow/Pillow/pull/9925)** — the same class of failure,
different mechanism: Pillow's behaviour depends on whether it was built with Raqm, so the identical
script produces correct text on one machine and broken text on another. On a Raqm-enabled build,
**0 of 15** strings survive the recipe unchanged. The maintainer pushed back on my first draft for
overclaiming a security angle. He was right, I dropped it, and the warning that remains is narrower
and true.

**[python-arabic-reshaper](https://github.com/mpcabd/python-arabic-reshaper/issues/102)** —
[issue #102](https://github.com/mpcabd/python-arabic-reshaper/issues/102) and
[PR #103](https://github.com/mpcabd/python-arabic-reshaper/pull/103), adding the
`PIL.features.check("raqm")` test to the README so readers can tell which case they are in.
Still unanswered.

### The linter

**[arabic-lint](https://github.com/Syamjith-NK/arabic-lint)** — `pip install arabic-lint`. Finds
Arabic that was corrupted *before it was stored*, in JSON, localisation files, database exports and
source. Zero dependencies, exit code 1 on a finding, so it drops into CI unchanged.

**0.2.0** adds a source check: the recipe appears in **3,168 indexed Python files**, and it reports
only the ones where a renderer that already shapes is actually being drawn to. ReportLab, non-Raqm
Pillow, terminal output and dead helpers stay silent, because a checker that flagged all 3,168
would be switched off in a day. I read six real projects to build it: three were broken and I filed
those ([1](https://github.com/whiteout-project/bot/issues/110),
[2](https://github.com/shahkoorosh/ComfyUI-PersianText/issues/3),
[3](https://github.com/kingshot-project/Kingshot-Discord-Bot/issues/28),
[4](https://github.com/NoorBayan/Diwan/issues/3)); three were correct and are documented in
[the write-up](https://syamjith-nk.github.io/most-of-these-are-not-bugs/).

Detection is asymmetric, which is why this survived years of being copied: the shaping half leaves
presentation-form codepoints that correctly authored Arabic never contains, so it is detectable —
but the reordering half produces identical codepoints in a different order, so no general detector
exists for it. And the damage is not cleanly reversible: lam-alef is one codepoint that decomposes
to two, in logical order, while the text around it is in visual order. `الإمارات` goes 8 codepoints
to 7, and the obvious NFKC repair turns `السلام` into `السالم` — a real but different word. The
tool refuses to auto-fix those rather than guess.

### How I work

In two of the three benchmarks the measurement contradicted the assumption I started with, and the
write-ups say so. In `arshape` a pass/fail scorer reported a broken configuration as working;
adding a middle band is what surfaced the real result. Designing a test that can prove you wrong is
most of the work.

When I found four faults in my own scorer I published a documented correction with old-to-new
figures rather than editing the numbers quietly.

### Also

Brand and product films, VFX compositing, and AI-assisted image pipelines where the craft still
governs the tool — a generated frame is judged on light, framing and rhythm like a photographed one.

[syamjithnk.com](https://syamjithnk.com) · Abu Dhabi, GMT+4
