# Renewal Radar

A post-deployment console for an AI service desk. It answers the questions a deployment success team needs every week:

- **Who is at risk?** Accounts ranked by annual recurring revenue (ARR) at risk, weighted by health and days to renewal.
- **What is broken?** Failed workflow runs grouped by error signature (an Okta 403, a Slack lookup on alias emails, a Workday rate limit), with the request, response, root cause and a proposed patch. One fix clears every affected customer.
- **Where do we grow?** The next workflows to build inside live departments, ranked by agent hours handed back, and the next departments to switch on.

From those answers it drafts a renewal brief for the account team, a workflow scaffold for the top automation gap, and a weekly playbook queue, so the motion scales without adding headcount for every new customer.

> Independent concept by Valerie Vu, built as a work sample for Serval's Agent Deployment team. All companies and numbers are synthetic. Not affiliated with or endorsed by Serval.

## Run it

It's one self-contained HTML file with no build step and no dependencies.

```bash
open index.html        # macOS
# or
python3 -m http.server # then visit http://localhost:8000
```

## How the health score works (0–100)

```
health = 0.30·automation + 0.25·adoption + 0.15·momentum + 0.15·reliability + 0.15·sentiment
```

| Component | Input | Scoring |
|---|---|---|
| Automation | Share of last 4 weeks' tickets resolved with no human | 60% = 100 |
| Adoption | Monthly unique requesters ÷ licensed seats | 45% = 100 |
| Momentum | Automation rate, last 4 weeks vs first 4 of 12 | flat = 50, +10 points = 100 |
| Reliability | Workflow run failure rate | 0% = 100, 10%+ = 0 |
| Sentiment | Customer satisfaction (CSAT), 1–5 | 3.5 = 0, 4.8 = 100 |

Bands: 75+ healthy, 55–74 watch, under 55 at risk. An account is a priority when it is not healthy and renews within 120 days.

## Code map

Everything lives in `index.html`:

- `CATS`: request-type catalog (department, volume per 1,000 seats, handling minutes, automation ceiling, integration).
- `SIGS`: error signatures with sample API request/response, root cause, fix and patch.
- `ACCOUNTS` → `build()`: seeded synthetic data per account, health score, gaps, whitespace, failures.
- `renderPortfolio()`, `renderAccount()`, `renderDiagnostics()`: the three views.
- `brief()` and `scaffold()`: the generated renewal brief and workflow code.

The "How it works" tab includes the warehouse SQL that would compute the same inputs from real ticket and workflow-run tables.
