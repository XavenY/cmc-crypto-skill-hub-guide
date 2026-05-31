---
name: cmc-crypto-skill-hub
description: Use CMC Crypto Skill Hub for crypto market, BTC, ETH, ETF, onchain, derivatives, portfolio, scanner, macro-crypto, and crypto cross-asset research. It helps the agent discover CMC Crypto Skill Hub skills, validate parameters, execute selected skills, parse envelopes, and render clean chat-ready Markdown research summaries.
---

# CMC Crypto Skill Hub

Use this skill when the user asks for crypto market, BTC, ETH, ETF, derivatives, onchain, token, portfolio, scanner, macro-crypto, or crypto cross-asset research.

CMC Crypto Skill Hub is exposed through an MCP server with `find_skill` and `execute_skill`. The Production MCP server id is `crypto-skill-hub`. Test environments may use `crypto-skill-hub-beta` or a temporary local server id, but user-facing installation should keep the Production id as `crypto-skill-hub`.

Tool namespace priority:

1. If a `crypto-skill-hub` or `crypto-skill-hub*` MCP server is connected, use only that server's `find_skill` and `execute_skill` tools for CMC Crypto Skill Hub calls.
2. Do not use app/connector aliases when a `crypto-skill-hub` MCP server is available.
3. If no `crypto-skill-hub` server is connected, do not guess alternate server names. Use another exposed MCP namespace only after verifying it provides the same `find_skill` and `execute_skill` tools for CMC Crypto Skill Hub.

For pure non-crypto tasks such as weather, email, coding, or standalone traditional equities, do not call CMC Crypto Skill Hub. Answer with the appropriate available capability outside this skill. If a traditional market question is asked as context for BTC or crypto, it is in scope.

## Operating Principle

Use the smallest set of CMC Crypto Skill Hub skills needed to answer the user's request well.

Prefer one broad CMC Crypto Skill Hub skill when it covers the requested lenses. Use multiple skills only when the user asks for separate lenses that one skill does not cover, such as ETF demand plus cross-asset behavior plus derivatives structure. Do not execute another skill just to add volume.

Treat CMC Crypto Skill Hub as the primary CMC-derived source. External sources may be used when the user asks for latest news, verification, official context, broader market background, or when CMC Crypto Skill Hub data is partial, stale, low-confidence, unavailable, or returns an execution error. Label external context separately from CMC-derived findings.

## Skill Execution Unit

For each selected Skill Hub skill:

1. Call `find_skill(query=...)` to confirm the `unique_name` and `input_schema`.
2. Validate the planned parameters against `input_schema`.
3. If a required field is missing, stop before execution and ask the user for the missing field.
4. Call `execute_skill(unique_name=..., parameters=...)` once for that skill and parameter set. `parameters` must be a JSON object, not a JSON string.
5. Do not silently retry a failed execution. If the only selected skill fails, report the exact error and stop. If a supplemental skill fails but other evidence is available, disclose the failed skill and lower confidence.

Do not expose tool traces, schema dumps, or raw wrapper structures in the final answer.

After any successful `execute_skill` call for an analytical crypto research request, default to the report format below. If the user explicitly asks for another format, honor that format while preserving status, confidence, missing-data warnings, and key evidence.

## Routing Notes

- Daily crypto market overview: prefer the daily market overview skill.
- BTC versus equities, DXY, rates, gold, or oil: prefer the BTC cross-asset correlation skill.
- BTC ETF demand or institutional demand: prefer the BTC ETF institutional demand skill.
- Broad crypto macro regime: prefer the crypto macro overview skill.
- Macro news affecting BTC and equities: prefer a macro news or cross-asset skill; use external sources only when the user asks for current news/verification or the Skill Hub result is unavailable.
- Onchain scanner requests: inspect schema first. If chain, time window, candidate count, or other required fields are missing, ask for them before execution.

## Envelope Parsing

Before rendering, normalize the tool response:

- If the response is wrapped as `{"raw_output": "...escaped JSON string..."}`, unescape and parse the inner JSON.
- Repeat this step if the inner object is wrapped again.
- If parsing fails, state the parse error. Do not summarize the wrapper as if it were the result.
- Preserve numbers, status strings, warning strings, risk flags, timestamps, confidence, and skill ids exactly when used as evidence.

## Error and Status Handling

If a CMC Crypto Skill Hub tool returns an error, state the exact `error_code` and reason. If the client reports a transport error rather than an `error_code`, state the transport error literally. Do not fabricate values.

If the user explicitly asked for a CMC Crypto Skill Hub result, stop after the error. For a general crypto question, you may continue with allowed external sources only when they can answer the user's request; disclose that CMC Crypto Skill Hub failed and lower confidence.

If `status` is `blocked`:

- Skip `Details` entirely.
- In `TL;DR`, use the block reason from `conclusion` as the first sentence.
- Suggest one fallback skill only if the response provides one.
- End with the footer.

If `status` is `partial` or `missing_or_stale_inputs` is not empty:

- Add a `⚠️` callout at the top of `TL;DR` naming the missing or stale inputs.
- Render available data normally.
- Use lower-confidence language.

If the response says inputs are missing, stale, unavailable, proxied, low coverage, or low confidence, add the same `⚠️` callout even when top-level `status` is `ok`.

If CMC Crypto Skill Hub data is stale, low-confidence, or unavailable and allowed external sources are used, still render the same report skeleton unless the user requested another format. Put the limitation in the top `⚠️` callout and label external evidence inside bullets.

## Output Format

Default format for CMC Crypto Skill Hub analytical reports: plain Markdown suitable for chat clients. No tables. No HTML. Keep normal reports at or below 2500 characters unless the user asks for depth. Scanner reports may extend to 4000 characters when candidate lists are necessary.

Match the language of the user's most recent message.

Use bold with double asterisks:

```text
**TL;DR**

Sentence 1.
Sentence 2.
Sentence 3.

🚨 **Notable anomalies:**

- ...

———

**Details**

**Topic**

- ...

💡 **Takeaway:** ...

🕐 timestamp · status · confidence
```

Append `· skill_id` to the footer when the parsed response provides it. For multiple executed skills, keep the footer short and list skill ids in a source note only if it fits. Use `n/a` for footer values that are not present; do not invent timestamps, status, confidence, or skill ids.

The section labels are fixed tokens. Do not translate these labels:

- `**TL;DR**`
- `🚨 **Notable anomalies:**`
- `📰 **Macro News:**`
- `**Details**`
- `💡 **Takeaway:**`

The divider must be exactly `———`, not `---`.

For blocked responses, omit `———` and `Details`.

## TL;DR

Start with `**TL;DR**` on its own line.

Include these parts, with a blank line between distinct blocks:

1. Three plain-language sentences:
   - Sentence 1: what the Skill Hub result found or what is happening.
   - Sentence 2: the bottom-line conclusion, including what the user should do or avoid.
   - Sentence 3: the 1-2 key data points that justify the conclusion.
2. `🚨 **Notable anomalies:**`
   - Provide 1-3 bullets for values that meaningfully deviate from baseline, cross thresholds, conflict across sources, or look concentrated.
   - If nothing is clearly anomalous, write `(none significant in this run)`.
3. `📰 **Macro News:**`
   - Include only if the Skill Hub response or allowed external sources contain macro news, news, key event summary, or equivalent narrative context.
   - Write one short market-view sentence and 2-3 bullets.
   - Skip this block when no such field or allowed external context exists.

Translate technical terms into everyday language. Preserve important numbers exactly when they are used as evidence.

## Details

Start with `**Details**` on its own line.

Group findings by natural topics from the response. Do not force a fixed taxonomy.

Common topic styles:

- `**🏛 Conditions**` for regime, rates, inflation, policy, or broad market backdrop.
- `**💧 Liquidity**` for liquidity, stablecoin, funding-stress, or cash-condition evidence.
- `**💰 ETF**` for ETF flow, AUM, absorption, or institutional demand.
- `**🔗 Cross-Asset**` for equities, DXY, gold, oil, rates, or divergence.
- `**📊 Structure**` for derivatives, funding, open interest, liquidation levels, or CVD.
- `**📋 Identity**`, `**👥 Holders**`, `**🔐 Security**`, `**🕯 Candles**`, or `**📰 Narrative**` for token and onchain reports.
- `**🚦 Funnel**`, `**🥇 Top**`, `**🎒 Backup**`, or `**⚠️ Coverage**` for scanner reports.

Do not include a Macro News topic in Details if Macro News was already covered in TL;DR.

For each topic block, use this order:

1. Blank line.
2. Bold topic header with emoji.
3. Bullets with evidence. Preserve numbers verbatim. Quote risk flags and warnings literally.
4. Blank line.
5. A takeaway line beginning with exactly `💡 **Takeaway:**`, including the ASCII colon.
6. Blank line.

Each takeaway should say, in at most three short sentences:

- what the data means in plain language;
- what the user should watch next;
- a concrete next action.

End with the footer on its own line, preceded by a blank line:

```text
🕐 timestamp · status · confidence
```

Before final answer, verify:

- exactly one `**TL;DR**`;
- exactly one `———` for non-blocked reports;
- exactly one `**Details**` for non-blocked reports;
- `🚨 **Notable anomalies:**` is present for non-blocked reports;
- at least one `💡 **Takeaway:**` line is present for non-blocked reports;
- no table, no HTML, no raw wrapper, no debug summary.

## Spacing

- Insert at least one blank line between distinct blocks.
- Never run two different sections together on adjacent lines.
- Place exactly one `———` line between TL;DR and Details.
- Do not use `———` anywhere else.
- Bullet marker is `-` only.
- Each bullet is on its own line.
