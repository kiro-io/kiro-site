# Kiro Website — Hosting Guide

Zero hosting costs beyond your domain. Everything runs on GitHub Pages (free forever, included with all GitHub plans).

---

## What You Need

| Item | Cost | Where |
|---|---|---|
| GitHub account | Free | github.com |
| GitHub repository | Free | already have it |
| GitHub Pages hosting | Free | included |
| Demo form submissions | Free | formsubmit.co |
| Custom domain | ~$10–15/year | any registrar |

---

## Step 1 — Configure the Demo Form Email

Open `website/index.html` and find this line (appears **twice** near the bottom):

```js
const FORM_ENDPOINT = 'https://formsubmit.co/ajax/YOUR_EMAIL_HERE';
```

Replace `YOUR_EMAIL_HERE` with the email address where you want demo requests sent. For example:

```js
const FORM_ENDPOINT = 'https://formsubmit.co/ajax/sales@kiro.io';
```

**First activation:** FormSubmit will send a confirmation email to that address the first time someone submits the form. Click the confirmation link once and all future submissions go straight to your inbox. No account creation needed.

---

## Step 2 — Push the Repository to GitHub

If you haven't already pushed to GitHub:

```bash
# Create the repo at github.com/kiro-io/kiro (empty, no README)
# Then:
git remote add origin https://github.com/kiro-io/kiro.git
git branch -M main
git push -u origin main
```

If the repo already exists, just push:

```bash
git add website/ .github/
git commit -m "Add website and deploy workflow"
git push
```

---

## Step 3 — Enable GitHub Pages

1. Go to your repository on GitHub: `github.com/kiro-io/kiro`
2. Click **Settings** → scroll to **Pages** in the left sidebar
3. Under **Build and deployment → Source**, select **GitHub Actions**
4. Click **Save**

That's it. The workflow at `.github/workflows/deploy-website.yml` will now run automatically on every push to `main` that touches the `website/` folder.

**Check the deployment:** Go to **Actions** tab → click the latest "Deploy Website" run → wait ~60 seconds → the URL appears in the run summary.

Your site will be live at:
```
https://kiro-io.github.io/kiro/
```
(or whatever your GitHub username/org and repo name are)

---

## Step 4 — Connect Your Custom Domain

### 4a. Add domain in GitHub

1. Go to **Settings → Pages**
2. Under **Custom domain**, type your domain: `kiro.io`
3. Click **Save**
4. Check **Enforce HTTPS** (appears once DNS is verified — usually takes a few minutes)

This creates a `CNAME` file in your repo automatically.

### 4b. Configure DNS at your registrar

Log in to wherever you bought the domain (Namecheap, GoDaddy, Cloudflare, etc.) and add these records:

**For an apex domain (`kiro.io`)** — add four A records:

| Type | Name | Value |
|---|---|---|
| A | @ | 185.199.108.153 |
| A | @ | 185.199.109.153 |
| A | @ | 185.199.110.153 |
| A | @ | 185.199.111.153 |

**For `www` redirect** — add a CNAME:

| Type | Name | Value |
|---|---|---|
| CNAME | www | kiro-io.github.io |

DNS changes propagate in 5–60 minutes. Once done, GitHub will automatically issue a free Let's Encrypt TLS certificate.

### 4c. Verify

```bash
# Should return GitHub's IPs
dig kiro.io +short

# Should show your site
curl -I https://kiro.io
```

---

## Step 5 — Auto-Deploy is Now Live

Every time you push changes to `website/` on the `main` branch, GitHub Actions will:
1. Pick up the changed files
2. Upload the `website/` folder as a Pages artifact
3. Deploy it (usually completes in under 90 seconds)

To manually trigger a deploy without any file changes:
- Go to **Actions → Deploy Website to GitHub Pages → Run workflow**

---

## Updating the Website

All website content is in `website/index.html` — a single self-contained file.

Common updates:

| What to change | Where in the file |
|---|---|
| Hero headline | Search `Scale smart` |
| Feature cards | Search `features-grid` |
| Demo form email | Search `YOUR_EMAIL_HERE` |
| Company email/links | Search `kiro.io` in footer |
| Comparison table | Search `compare-table` |
| Stats numbers | Search `data-target` |
| Color scheme | `:root` block at the top |

After editing, just commit and push — the site updates automatically.

---

## Troubleshooting

**"Page not found" after enabling Pages**
→ Make sure Source is set to **GitHub Actions** (not "Deploy from a branch").

**Form submissions not arriving**
→ Check that you replaced `YOUR_EMAIL_HERE` with your real email. Submit the form once — check spam for FormSubmit's activation email and click the link.

**Custom domain shows "not secure"**
→ Wait up to 24 hours for Let's Encrypt to provision the certificate. Make sure "Enforce HTTPS" is checked in GitHub Pages settings.

**GitHub Actions workflow not running**
→ Verify Pages is set to GitHub Actions source. Check the Actions tab for error messages. Make sure the workflow file is at `.github/workflows/deploy-website.yml`.

**Site shows old content**
→ Hard-refresh your browser (`Cmd+Shift+R` / `Ctrl+Shift+R`). GitHub CDN can cache for a few minutes.

---

## Cost Summary

```
GitHub Pages hosting:    $0/month  (free forever)
FormSubmit form relay:   $0/month  (free, no limits for reasonable use)
Let's Encrypt TLS:       $0/month  (auto-renewed)
Custom domain:          ~$10–15/year  (your only cost)
────────────────────────────────────────────────
Total running cost:      ~$10–15/year
```
