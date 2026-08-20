## Syamjith NK

Cinematographer and AI creative technologist in Abu Dhabi. I work where film craft meets applied
AI, and I publish open evaluation of the Arabic tooling that production depends on.

### Arabic breaks silently — three benchmarks

Each measures a failure that looks correct to anyone who does not read Arabic, which is exactly
why it ships. All CC BY 4.0, with the test set, the scorer, the raw per-item results, and a plain
statement of what the measurement does **not** establish.

| | finding |
|---|---|
| **[arnum-tts](https://github.com/genviz-ai/arnum-tts)** | The same Arabic sentence with Arabic-Indic digits (٢٠٢٦) instead of Western (2026) drops from **73% to 7%** intelligible on one engine. Another handles both at 80% — a missing normalisation step, not a hard problem. |
| **[arshape](https://github.com/genviz-ai/arshape)** | The `arabic_reshaper` + `python-bidi` recipe recommended in nearly every tutorial **corrupts** Arabic on any renderer that already shapes: 15/15 correct without it, 0/15 with it. |
| **[arpdf](https://github.com/genviz-ai/arpdf)** | Reversed, ligature-mangled and space-collapsed are three distinct failure modes that a single pass/fail test collapses into one. |

Datasets: **[huggingface.co/syamjithnk](https://huggingface.co/syamjithnk)**

### Upstream

The shaping finding is filed against `python-arabic-reshaper` — a package downloaded **5.4 million
times a month** — as [issue #102](https://github.com/mpcabd/python-arabic-reshaper/issues/102) and
[PR #103](https://github.com/mpcabd/python-arabic-reshaper/pull/103), which adds the
`PIL.features.check("raqm")` test to the README so readers can tell which case they are in.

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
