# ai-reasoning-doctrine

**How to reason before asserting, when you put AI agents to work.**
14 principles, 14 expert "lenses", 8 error types, and a mandatory contradiction
protocol. Every principle was born from a **real, measured mistake** — not from a
good intention.

[Version française →](README.md)

---

## The problem

An AI agent producing an analysis is **very convincing**, and often wrong. Not
out of malice — by construction.

It measures its own artifact and believes it is measuring the world. It turns an
absence of results into proof of absence. It announces a number without saying
which population it covers. It declares a task successful because the task ran,
without ever re-reading what it produced.

These failures are not fixed by telling the agent to "be more rigorous". They are
fixed by **rules**, and by a **contradictor whose job is to contradict you**.

This repository is those rules, written down after watching them fail.

---

## What is in it

| Document | Content |
| --- | --- |
| **[14-principes.md](doctrine/14-principes.md)** | The 14 principles (French). Each one: the real error, the measurement, what the contradictor corrected, the rule. |
| **[14-principles.en.md](doctrine/14-principles.en.md)** | English version of the principles. |
| **[les-14-lunettes.md](doctrine/les-14-lunettes.md)** | 14 expert perspectives (investor, senior dev, lawyer, security, actuary…) plus the **Council of 14** method: pick 5 lenses, force them to disagree, arbitrate, decide. |
| **[8-types-erreurs.md](doctrine/8-types-erreurs.md)** | The taxonomy of 8 error families and the mandatory-contradictor protocol. |
| **[8-error-types.en.md](doctrine/8-error-types.en.md)** | English version. |
| **[explorer-une-api.md](doctrine/explorer-une-api.md)** | Exploring an undocumented API by reflection instead of guessing names and units. |

### The 14 principles

1. **Measure before asserting.**
2. An absence of results is not proof of absence.
3. Verify what you have before building on it.
4. Test your own tool before trusting it.
5. Distinguish "false" from "unverifiable".
6. Correction for multiple comparisons.
7. Look for data leakage.
8. Never promise beyond what you measured.
9. Check the **file a task produced**, not its **claim** of success.
10. Choose the right method for the source.
11. **Add first, configure after** (APIs).
12. The mandatory contradictor.
13. What matters is not being right, but the work being right.
14. The server computes, the workstation controls.

---

## The three ideas that carry the rest

**1. A script that writes must be paired with a script that re-reads.**
"The script ran" is a claim, not evidence. The measured case behind principle 9:
two scheduled tasks were declared "active" while they had **never produced a
single file**. Work with no output log is **indistinguishable from dead work**.

**2. A contradictor cannot conclude "all clear" without having tried.**
List at least 5 attempted attacks and produce at least 1 real defect — or show
why none is findable **by showing your attempts**. A report that says "compliant"
without listing its attacks is rejected.

**3. APIs have silent defaults.**
Setting a property **before** inserting the object into its container is ignored:
the container overwrites your value at insertion. The code is valid, no exception
is raised, and the result is wrong. See principle 11.

---

## What I measured

The cases below come from my own video-editing and scripting work. They are the
only cases I publish, because they are the only ones that are entirely mine.

| Measurement | Method | Result |
| --- | --- | --- |
| Keyframes actually applied, wrong order (set before add) | counting animated events after the run | **72 / 288** |
| Same frames, correct order | idem | **288 / 288** |
| `Timecode`: the constructor's real unit | construct / read back | **milliseconds** (`new Timecode(4000.0).ToMilliseconds() == 4000`) |
| Assembling 4 × 2.000 s segments, `acrossfade` default | `ffprobe` | **6.500000 s** instead of 8.000000 s |
| Same assembly with `overlap=0` | `ffprobe` | **8.000000 s** — no loss |
| Assembly with mixed sample rates | `ffprobe` | **2.544218 s** instead of 2.500000 s |
| Chain of 30 `acrossfade` at 30 ms on 31 × 1 s sources | `ffprobe`, 3 runs | **1.36 s / 1.83 s / 0.82 s** instead of 30.100 s expected |

### What I removed before publishing

This work comes from a **private** working system, and it cited that system:
projects, their figures, internal file paths. **All of it has been removed or
generalised.** Where an entire section rested on a private project, the section is
marked as removed, and **no replacement figure was invented** — an invented
figure would be precisely the error this document is about.

### A correction I have to publish

An earlier version of my notes claimed that `acrossfade`'s default `overlap=1`
**"shortens every junction by 30 ms"** as a hidden defect, and that `overlap=0`
and `overlap=1` produced the **same** duration.

**Both claims were false.** The measurement gives:

- the shortening is **exactly `d`**, the crossfade duration, per junction — not a
  mysterious 30 ms;
- `overlap=0` and `overlap=1` are **not** equivalent: `overlap=0` keeps the total
  duration, `overlap=1` removes `d` per junction.

That is principle 8 applied to myself: **never promise beyond what you measured**,
including when you have already said it publicly.

---

## Who this is for

- People who **build agents** and want them to stop asserting without measuring.
- People who **consume agent reports** and have to decide whether to believe them.
- People who must **decide without being an expert in everything**: the "lenses"
  part is built for that.

It is not a theoretical framework or a consulting methodology. It is a checklist,
born from mistakes, with the numbers that caught them.

---

## Install / use

Nothing to install; these are documents.

```bash
git clone <this repo>
cd ai-reasoning-doctrine
```

**As agent instructions**, paste `doctrine/14-principes.md` (or the English
version) into the agent's context as a reference document, before the task.

**For review**, `doctrine/8-types-erreurs.md` contains a verification-report
template to copy.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). The bar is strict: a contribution is only
accepted if it brings **a real error and its measurement**. A claim without a
measurement is refused.

## License

[MIT](LICENSE).
