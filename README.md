# clean-sheet-simulator# Clean Sheet — FIFA World Cup Financial Crime Simulator

**[Play Live](https://nikkiill.github.io/clean-sheet-simulator/cleansheet.html)** &nbsp;|&nbsp; Built by [Nikhil Shinde](https://linkedin.com/in/nikhil-shinde-55a0a210b) — KYC/AML Product Specialist

---

## What Is This?

The FIFA World Cup 2026 generates $1.6bn in official revenue. It also generates one of the largest financial crime windows of any four-year cycle.

You start with **£1,000,000 in criminal proceeds**. Seven decisions. Five stages. 30 seconds per move. Can you clean it through the World Cup economy before the final whistle?

Every choice maps to a real typology. Every detection alert references actual AML legislation. Every case is drawn from enforcement records. And if you freeze — the game panics and picks the worst option for you.

This is not a game about how to launder money. It is a game about how detection systems think — and why the gap between £100bn laundered and less than 1% seized is a product problem.

---

## What's New in v2.0

**30-second decision timer** — each scene has a live countdown. Freeze and `autoPanic()` fires: the game automatically selects the highest-risk option. Audio tick fires every second in the final 10.

**Pundit commentary** — after every choice, a random one-liner from a live commentator. Tiered by risk level. Low risk: *"Safe as houses. Which, coincidentally, is the next typology."* High risk: *"The NCA appreciates your confidence. They're writing it down."* Panic: *"Time's up. You panicked. The system picked for you."*

**Sound engine** — built on the Web Audio API with zero dependencies. Referee whistle on scene start, cash register click when money moves, three-tone sawtooth alarm on a red card.

**Collapsible intel** — scout report collapses by default so choices are the first thing you see. Tap to expand for full regulatory context.

**Shorter scenario text** — all seven scenes rewritten to 2-3 punchy lines. The detail lives in the post-choice reveal, not the setup.

---

## The Seven Matches

| Round | Scenario | Stage | Typology |
|---|---|---|---|
| Group Stage I | The Ticket Operation | Placement | Structuring / Smurfing |
| Group Stage II | The Betting Syndicate | Placement | Sports Betting ML |
| Group Stage III | The Agent Cut | Placement | Professional Services Layering |
| Round of 16 | The Sponsorship Shell | Layering | Shell Company / TBML |
| Quarter-Final | The Transfer Window | Layering | Over-Invoicing / Crypto |
| Semi-Final | The Stadium Investment | Integration | Real Estate / Cash-Intensive |
| The Final | The Final Whistle | Integration | Investment / Charitable Integration |

---

## Outcomes

| Result | Condition | What It Means |
|---|---|---|
| Champion | Heat below 25%, no cards | Clean sheet — system never detected you |
| Runner-Up | Heat below 55%, no red card | Survived with a trail — investigation possible |
| Under Investigation | Heat 55%+, yellow card | SARs filed — NCA reviewing |
| Red Card | Heat 85%+ or red card issued | Arrested — assets frozen |

---

## Jurisdiction System

Choose your operating base before you start. Your jurisdiction's FATF AML rating wires directly into every heat calculation as a multiplier.

| Jurisdiction | FATF AML Rating | Heat Multiplier | Why People Choose It |
|---|---|---|---|
| United Kingdom | 95/100 | x1.35 | They don't. Hardest in the world. |
| United Arab Emirates | 70/100 | x1.00 | Rapidly improving. Still gaps. |
| Malta | 55/100 | x0.82 | EU member. Light-touch enforcement history. |
| Cayman Islands | 42/100 | x0.65 | This is exactly why people pick Cayman. |

---

## Real Cases Referenced

| Case | Value | Typology |
|---|---|---|
| NatWest — FCA Fine | £264.8m | Cash-intensive business structuring |
| BNP Paribas — US Settlement | $8.9bn | Trade-based value transfer / TBML |
| Panama Papers — Mossack Fonseca | 11.5m documents | Shell company layering |
| Tornado Cash — OFAC Sanctions | $7bn+ | Cryptocurrency mixing |
| Zamira Hajiyeva — NCA UWO | £22m at Harrods | Offshore property / unexplained wealth |
| FATF Football ML Report | 2018 | Club ownership / agent fee abuse |
| UNODC Illegal Betting | $1.7bn/match | Match fixing / sports integrity |

---

## Regulatory Framework

- **POCA 2002** — Proceeds of Crime Act (ss.327-336)
- **MLR 2017** — Money Laundering Regulations (EDD, CDD, SAR obligations)
- **FATF Recommendations** — 11, 16, 24, 25 and Football ML Report 2018
- **Economic Crime Act 2022** — Register of Overseas Entities, UWO powers
- **MiCA** — EU Markets in Crypto-Assets Regulation (Art 38, 68)
- **OFAC** — Tornado Cash sanctions designation 2022
- **FATF TBML Report 2020** — Trade-Based Money Laundering
- **Wolfsberg Group** — Trade Finance Principles 2019
- **UNODC** — Sport Integrity and Illegal Betting Reports

---

## SQL Detection Layer

Each scenario maps to a real SQL query a financial intelligence analyst would run.

**Match 1 — Ticket structuring detection**
```sql
SELECT
    account_id,
    COUNT(*) AS tx_count,
    SUM(amount) AS total_deposited
FROM transactions
WHERE
    tx_date BETWEEN '2026-06-01' AND '2026-07-20'
    AND amount BETWEEN 7000 AND 9999
    AND merchant_category = 'HOSPITALITY_EVENT'
GROUP BY account_id
HAVING COUNT(*) >= 3
    AND SUM(amount) > 25000
ORDER BY total_deposited DESC;
```

**Match 2 — Betting syndicate velocity flag**
```sql
WITH win_rates AS (
  SELECT
    customer_id,
    SUM(CASE WHEN outcome = 'WIN' THEN 1 ELSE 0 END) / COUNT(*) AS win_rate,
    SUM(payout) AS total_winnings
  FROM betting_transactions
  WHERE tournament = 'FIFA_WC_2026'
  GROUP BY customer_id
)
SELECT * FROM win_rates
WHERE win_rate > 0.72
  AND total_winnings > 50000
ORDER BY win_rate DESC;
```

**Match 3 — Agent fee anomaly (2 std dev above market)**
```sql
SELECT
    t.agent_company,
    t.fee_amount,
    ROUND(t.fee_amount / t.transfer_value * 100, 2) AS fee_pct,
    b.avg_fee_pct
FROM player_transfers t
CROSS JOIN (
  SELECT
    AVG(fee_amount / transfer_value * 100) AS avg_fee_pct,
    STDDEV(fee_amount / transfer_value * 100) AS stddev_fee_pct
  FROM player_transfers
) b
WHERE (t.fee_amount / t.transfer_value * 100) > (b.avg_fee_pct + 2 * b.stddev_fee_pct)
ORDER BY fee_pct DESC;
```

---

## Power BI Dashboard Concept

| Page | Visuals | Data Source |
|---|---|---|
| Tournament Overview | SAR volume by match week, heat map by jurisdiction | transactions, sar_filings |
| Typology Breakdown | Stacked bar by typology | ml_typology_flags |
| Jurisdiction Risk | Choropleth — AML rating vs flagged volume | jurisdiction_ratings |
| Card Tracker | Yellow/red card timeline vs scrutiny level | simulation_runs |
| Outcome Funnel | Started vs escaped vs flagged vs caught | game_outcomes |

```
Structuring Flag Rate =
DIVIDE(
    CALCULATE(COUNT(transactions[tx_id]),
        transactions[amount] >= 7000 && transactions[amount] <= 9999,
        transactions[tx_count_7d] >= 3),
    COUNT(transactions[tx_id])
)
```

---

## Technical Stack

| Layer | Detail |
|---|---|
| Language | Vanilla HTML / CSS / JavaScript |
| Dependencies | Zero — no frameworks, no libraries, no build step |
| Audio | Web Audio API (browser-native) |
| Deployment | GitHub Pages |
| Fonts | Outfit + JetBrains Mono via Google Fonts |
| Storage | None — no data collected or stored |

---

## Run Locally

```bash
git clone https://github.com/nikkiill/clean-sheet-simulator.git
cd clean-sheet-simulator
open cleansheet.html
```

---

## Background

Built during the FIFA World Cup 2026. Companion to [Flagged — AML Detection Simulator](https://nikkiill.github.io/AML-Laundromat-Game/).

Both projects come from direct experience as a KYC/AML Product Owner — sitting in typology reviews, reading FATF reports and NCA SAR data, and building detection logic into financial crime compliance products.

The World Cup is not just a sporting event. It is one of the world's most significant financial crime windows — and almost nobody in the compliance industry talks about it in a way the general public can engage with.

---

## Author

**Nikhil Shinde** — Product and Operations Analyst | KYC/AML Product Specialist

- LinkedIn: [linkedin.com/in/nikhil-shinde-55a0a210b](https://linkedin.com/in/nikhil-shinde-55a0a210b)
- Flagged (original game): [nikkiill.github.io/AML-Laundromat-Game](https://nikkiill.github.io/AML-Laundromat-Game/)

---

## Disclaimer

Educational simulation built for professional awareness. All scenarios are hypothetical and based on publicly documented regulatory cases and FATF typology guidance. No actual financial crime is facilitated or encouraged.

---

*$1.7bn is bet illegally on every major World Cup match. The official economy and the criminal economy run side by side. Most people only ever see one of them.*
