# 🔥 FireGuard TX — Fire Sprinkler Estimator & AI Plan Analyzer

Professional fire sprinkler project estimator for Texas contractors. Features live cost calculations, multi-floor support, Dallas/Outside Dallas pricing logic, and AI-powered fire protection plan analysis via Google Gemini.

---

## 🚀 Deployment (2 parts)

### Part 1 — Deploy the Cloudflare Worker (API proxy)

This keeps your Gemini API key secure and off the client.

```bash
# 1. Install dependencies
npm install

# 2. Set your Gemini API key as a Cloudflare secret (never stored in code/git)
npm run set:key
# Paste your Gemini API key when prompted

# 3. Deploy the worker
npm run deploy:worker
```

After deploying, Wrangler will give you a Worker URL like:
`https://fireguard-tx-api.YOUR_SUBDOMAIN.workers.dev`

**Copy that URL.**

### Part 2 — Deploy the Frontend to GitHub Pages

1. Open `public/index.html`
2. Find this line near the top of the `<script>` section:
   ```js
   const WORKER_URL = 'https://fireguard-tx-api.YOUR_SUBDOMAIN.workers.dev';
   ```
3. Replace it with your actual Worker URL from above.
4. Commit and push to GitHub.
5. In your GitHub repo → Settings → Pages → set source to `main` branch, `/public` folder (or root if you prefer).
6. Your site is live at `https://yourusername.github.io/repo-name/`

---

## 🔑 Updating Your Gemini API Key

```bash
npm run set:key
# Enter new key when prompted, then redeploy:
npm run deploy:worker
```

---

## 📁 Project Structure

```
fireguard-tx/
├── public/
│   └── index.html          ← Frontend (deploy to GitHub Pages)
├── worker/
│   └── index.js            ← Cloudflare Worker (API proxy)
├── wrangler.toml           ← Cloudflare config
├── package.json            ← npm scripts
├── .gitignore              ← Keeps secrets out of git
└── README.md
```

---

## 🧮 Calculation Formulas

### New Construction
- **SH** = Total Sq Ft ÷ 95
- **Material** = SH × $105 (Dallas) | SH × $150 (Outside Dallas) | SH × $180 (Dry System)
- **Labor** = SH × $32
- **Design** = max(SH × $11, $1,000)
- **Misc** = $5,000 base + Fire Pump ($10k) + Dry Add-on ($14k) + Backflow ($5k)
- **Taxes** = (Material + Misc) × 8.25% (unless Tax Exempt)
- **Profit** = Subtotal × 20%
- **Final** = Subtotal + Taxes + Profit

### Existing Construction
- **SH** = Total Sq Ft ÷ 95
- **Final** = SH × $300 (1–50 heads) | SH × $150 (51–150 heads) | SH × $100 (151+ heads)

---

## 🤖 AI Plan Analyzer

Upload fire protection plans (PDF or images). The Worker forwards them to Google Gemini Vision which extracts:
- Square footage, floor count
- Sprinkler head count & types
- System type (Wet/Dry)
- Fire pump, backflow, dry system presence
- Building type, occupancy, address
- Engineer/contractor info

Results are used to auto-generate a cost estimate and can be exported as a PDF report.
