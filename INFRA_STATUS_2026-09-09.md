# Infrastructure status — 9 September 2026

Shared note written to all TimeLightHub repos (timelighthub-website, TimeLightPay / BTCPay Server, agent-forge-commerce, ai-travel-agent-platform-v1, TimeLightHub_Engine/final_review) so every session works from the same facts. Written after a full check of DNS, GitHub Pages, Actions run history and the three droplet repos.

## Hosting facts (verified 9 Sep 2026)

| Host | Where it lives now | Status |
|---|---|---|
| timelighthub.com / www | **GitHub Pages**, repo `TimeLight-AiAgents/timelighthub-website`, branch `main`, path `/`, CNAME `timelighthub.com`, HTTPS enforced (cert valid to 29 Nov 2026) | Live, 200 |
| travel.timelighthub.com (+ api, agents, admin, app) | Render, from `ai-travel-agent-platform-v1` (`render.yaml` Blueprint) | Live |
| pay.timelighthub.com | DigitalOcean droplet 159.89.108.226 (TimeLightPay gateway-nginx + BTCPay) | **Droplet destroyed — down.** DNS A record to be deleted on GoDaddy; re-add on revival |
| afc.timelighthub.com | Same droplet (Agent Forge Commerce, proxied by gateway-nginx) | **Down** (same) |
| research.timelighthub.com | **New** — Render Static Site from `timelighthub-website`, Root Directory `research`, custom domain + GoDaddy CNAME `research` | Built on branch `research-site`; not yet merged / not yet live |

- **DNS registrar/DNS host: GoDaddy** (nameservers ns35/ns36.domaincontrol.com).
- History: main site was on GitHub Pages → moved to the droplet (May 2026, rsync workflow) → droplet destroyed (late Aug/Sep 2026) → back on GitHub Pages. The droplet **will be revived later**; nothing droplet-related is to be deleted, only paused.
- Render plan: static site is free; custom-domain cap on the workspace is reached (travel + api), so the research domain costs $0.25/month — accepted by Samer.

## What changed on 9 Sep 2026 (repo `timelighthub-website`, branch `research-site`, pushed, NOT merged)

1. `research/` — new sober static page for the public research record (TimeLightHub composable photonic computer): abstract, 13-element catalog, honest status, the one first experiment, PDFs + Complete HTML, Zenodo DOI 10.5281/zenodo.22649324, ORCID 0009-0003-8565-2605, UK patent application GB2615417.9, citation. Public content only, no cost figures. (arXiv declined the preprint on 14 Sep 2026; the page cites Zenodo only.)
2. `index.html` — gold **Research** link in desktop + mobile nav (`nav-research`, `mnav-research`, target=_blank) and a slim fixed bar `.research-bar` above the fixed header (`--rbar-h: 32px`; header, mobile-nav and hero are offset by it). i18n `setText` does not touch the new ids.
3. `sitemap.xml` — entry for research.timelighthub.com.
4. `.github/workflows/deploy.yml` — **PAUSED**: `push` trigger commented out, `workflow_dispatch` kept, secrets (`DEPLOY_SSH_KEY`, `DROPLET_HOST`, `DROPLET_KNOWN_HOSTS`) and the rsync target `/opt/timelighthub-hub/` untouched. Reason: it would fail on every push to main against the dead droplet. GitHub Pages deploys via its own separate `pages-build-deployment` job and is unaffected.

## Droplet revival order (when Samer decides)

1. Re-create the droplet (new IP unless a reserved IP is used).
2. Deploy **TimeLightPay** (repo `TimeLight-AiAgents/TimeLightPay`, WSL `~/projects/BTCPay Server`): its `gateway-nginx` container terminates TLS for timelighthub.com/www, pay, merchant, adminpay, payments and afc, and serves the hub's static files from `/opt/timelighthub-hub` (read-only mount). Its `deploy.yml` defaults `DROPLET_HOST` to 159.89.108.226 — update the repo variable / secret `DROPLET_SSH_KEY`.
3. Deploy **Agent Forge Commerce** (`~/projects/agent-forge-commerce`, `scripts/deploy-do.sh`, binds 172.17.0.1:8090, proxied by gateway-nginx).
4. Hub: update `DROPLET_HOST` + `DROPLET_KNOWN_HOSTS` secrets in `timelighthub-website`, then run the paused workflow manually or restore its `push` trigger.
5. GoDaddy: re-add A records for `pay` and `afc` → new IP; move the apex A records from GitHub Pages (185.199.108–111.153) back to the droplet **only after** step 2 is healthy; if the apex moves, keep the research subdomain on Render (independent).

## Contacts / identifiers used on the research page
Samer Beyrouti · Connect@TimeLightHub.com · ORCID 0009-0003-8565-2605 · Zenodo DOI 10.5281/zenodo.22649324 · arXiv: declined 14 Sep 2026 (do not resubmit) · UK patent application GB2615417.9 (filed 18 Jun 2026).
