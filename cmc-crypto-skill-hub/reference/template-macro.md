# Macro read report template

Applies to `unique_name`: `crypto_macro_overview`, `macro_liquidity_monitor`, `macro_financial_conditions`, `macro_news_aggregator`, `detect_market_regime`, `decode_macro_event_impact`.
Render only from the parsed `execute_skill` response.

## What these services return

A conclusion-first macro read under `result.data`:

- `status`, `confidence`, `macro_regime` (e.g. `mild_tailwind`), `risk_budget.{max_position_pct, leverage, stance}`.
- `decision_report.conclusion` — one-sentence judgment.
- `decision_report.reasoning_summary` — exactly four objects with `topic`, `read`, `evidence`, `implication`.
- `decision_report.bull_base_bear` — `bull`, `base`, `bear` (in that order).
- `decision_report.upgrade_downgrade.{upgrade_if, downgrade_if}` — falsifiable triggers.
- `signal_board[]` — `{signal, status, direction, key_value, impact}`.
- `missing_or_stale_inputs`.

For `macro_news_aggregator`, lead the news block from the response's news/catalyst fields and use `📰 **Macro News:**`.

## Render skeleton

```text
**TL;DR**

[⚠️ if partial / missing inputs: one line naming them]
Sentence 1 — the macro conclusion (decision_report.conclusion) with regime + confidence.
Sentence 2 — the risk budget stance (max position %, leverage) and what it means.
Sentence 3 — the primary driver / key number.

🚨 **Notable anomalies:**

- divergent or threshold-crossing signals, or (none significant in this run)

———

**Details**

**🧭 Reasoning**
- one bullet per reasoning_summary item: topic — read; evidence; implication.

**📊 Signals**
- signal — direction, status, key_value

**🐂🐻 Bull / Base / Bear**
- Bull: ...
- Base: ...
- Bear: ...

**🔀 Triggers**
- Upgrade if: ...
- Downgrade if: ...

💡 **Takeaway:** the stance and what changes it. No execution language (buy/sell/leverage up).

🕐 timestamp · status · confidence · skill_id
```

## Field mapping

| Response field | Renders as |
|---|---|
| `decision_report.conclusion` + `macro_regime` + `confidence` | TL;DR sentence 1 |
| `risk_budget` | TL;DR sentence 2 |
| `reasoning_summary[4]` | `🧭 Reasoning` bullets |
| `signal_board[]` | `📊 Signals` bullets |
| `bull_base_bear` | `🐂🐻 Bull / Base / Bear` block |
| `upgrade_downgrade` | `🔀 Triggers` block |

## Minimum (loose pass line)

- TL;DR leads with the conclusion + regime, not raw data.
- Bull/base/bear and the upgrade/downgrade triggers are both present.
- Tone never exceeds regime / confidence / risk budget.

## Bonus

- Full four-item reasoning; signal board with key values.

## Never

- Execution language (buy, sell, enter, leverage up); tables; provider/SDK names; making the prose more aggressive than the stated regime.
