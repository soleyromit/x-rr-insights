# exxat-rr-insights — GitHub Setup & Auto-Sync

## One-time setup (5 minutes)

### Step 1 — Create the repo
```bash
gh repo create exxat-rr-insights --private --description "Insight Hub: Exxat research intelligence, UX artifacts, and design strategy"
cd exxat-rr-insights
```

If you don't have the GitHub CLI, go to github.com/new and create it manually, then:
```bash
git clone https://github.com/YOUR_USERNAME/exxat-rr-insights.git
cd exxat-rr-insights
```

### Step 2 — Initial folder structure
```bash
mkdir -p products/exam-management
mkdir -p products/faas-2
mkdir -p products/course-eval
mkdir -p products/skills-checklist
mkdir -p products/learning-contracts
mkdir -p intelligence/personas
mkdir -p intelligence/themes
mkdir -p intelligence/roadmap
mkdir -p portfolio
mkdir -p stakeholder
mkdir -p granola-sync
```

### Step 3 — Copy the HTML files Claude generated
```bash
cp ~/Downloads/insight_hub.html ./index.html
cp ~/Downloads/exam_management_deep_dive.html ./products/exam-management/index.html
```

### Step 4 — Initial commit and push
```bash
git add .
git commit -m "feat: initial Insight Hub platform — overview + Exam Management deep dive"
git push origin main
```

### Step 5 — Enable GitHub Pages (makes it a live URL)
1. Go to repo Settings → Pages
2. Source: Deploy from branch → main → / (root)
3. Save. In ~60 seconds your platform is live at:
   https://YOUR_USERNAME.github.io/exxat-rr-insights/

---

## Auto-sync workflow: Claude → GitHub

Since there's no GitHub MCP connector available yet, here's the workflow to keep the repo updated when Claude generates new artifacts:

### Option A — Manual (current, 30 seconds per update)
When Claude generates a new or updated HTML file:
```bash
# From your exxat-rr-insights folder
cp ~/Downloads/FILENAME.html ./products/PRODUCT_NAME/index.html
git add .
git commit -m "update: PRODUCT_NAME — DESCRIPTION"
git push
```

### Option B — GitHub Actions auto-deploy (set and forget)
Create `.github/workflows/deploy.yml`:
```yaml
name: Deploy Insight Hub
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./
```
Now every `git push` auto-deploys to GitHub Pages.

### Option C — Connect GitHub MCP (coming soon)
The GitHub MCP connector (https://github.com/github/github-mcp-server) can be self-hosted.
Once connected to Claude.ai, Claude can push files directly without any manual steps.

Setup (requires Node.js):
```bash
npx @modelcontextprotocol/github-mcp-server
```
Then add to Claude.ai → Settings → Connected Apps → Custom MCP → paste the URL.

---

## File naming convention

| Context | File path in repo |
|---|---|
| Platform overview | `/index.html` |
| Exam Management deep dive | `/products/exam-management/index.html` |
| FaaS 2.0 deep dive | `/products/faas-2/index.html` |
| Course Eval deep dive | `/products/course-eval/index.html` |
| Skills Checklist deep dive | `/products/skills-checklist/index.html` |
| Learning Contracts deep dive | `/products/learning-contracts/index.html` |
| Cross-product persona map | `/intelligence/personas/index.html` |
| Granola session notes (raw) | `/granola-sync/YYYY-MM-DD-TITLE.md` |
| Stakeholder decks | `/stakeholder/YYYY-MM-DD-AUDIENCE.html` |

---

## How Claude updates the repo going forward

Tell Claude any of these and the update will be scoped to the right file:

- "Update Exam Management with today's Granola notes" → `/products/exam-management/index.html`
- "Add FaaS insights from today's meeting" → `/products/faas-2/index.html`
- "Rebuild the persona map" → `/intelligence/personas/index.html`
- "New stakeholder deck for Arun" → `/stakeholder/2026-03-XX-arun.html`

Each Claude session generates a fresh downloadable HTML. Download → copy to folder → `git push`.
The GitHub Action deploys it in under 60 seconds.
