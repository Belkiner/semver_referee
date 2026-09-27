# SemVerReferee

Intelligent Contract for GenLayer. Validators read a package's release notes and decide whether the claimed SemVer bump (`patch` / `minor` / `major`) matches the changes. Publishers accumulate an on-chain honesty record: `honest`, `under_declared`, `over_declared`.

`under_declared` is the dangerous case: a breaking change shipped as a minor or patch.

## Why this needs GenLayer

A Solidity contract cannot read GitHub release notes and classify the change. The decision is a judgment over prose. Consensus is over a three-word enum, not free text.

Equivalence principle: `gl.eq_principle.prompt_comparative` on the canonical class (`patch` | `minor` | `major` | `unclear`). Raw notes differ across nodes; the enum word must match.

## Methods

| Method | Kind | What it does |
|---|---|---|
| `review_release(package, version, claimed_bump, notes_url, publisher)` | write | Classify notes vs claimed bump |
| `get_verdict(package, version)` | view | Stored JSON record |
| `publisher_record(publisher)` | view | honest / under / over + trust_pct |
| `reviewed_count()` | view | Total reviews |

## First call

```
review_release("left-pad", "2.0.0", "patch", "<url to release notes>", "author1")
```

## Consensus design

The LLM is constrained to one of three words. The contract then compares the class against the publisher's claim and updates counters. Validators do not have to agree on wording of the notes, only on the resulting class.

## Limits

- Header uses `py-genlayer:test`. Pin a current runner before production deploy.
- `strict_eq` around an LLM is tight. If validators disagree on borderline notes, the tx fails rather than inventing a class.
- Deploy on Studio / testnet and attach the tx hash before submitting to the Portal.

## License

MIT
