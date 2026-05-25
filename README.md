# rootnotes — security writeup blog & portfolio

A custom Jekyll theme for publishing Vulnerable-VM / Target-machine writeups,
hosted free on GitHub Pages. Drop a Markdown file in `_posts/`, push, and the
site rebuilds itself.

---

## 1. Make it yours (2 minutes)

Open **`_config.yml`** and edit the values at the top — your name, tagline,
GitHub/LinkedIn/email links. That's the only file you must touch to personalise
the whole site.

If you want a resume button, drop a PDF at `assets/resume.pdf`.

---

## 2. Put it on GitHub Pages

You have two URL options. Pick one.

### Option A — a personal site at `https://YOURNAME.github.io` (recommended)

1. Create a new GitHub repository named **exactly** `YOURNAME.github.io`
   (replace `YOURNAME` with your real GitHub username, all lowercase).
2. In `_config.yml` set:
   ```yaml
   url: "https://YOURNAME.github.io"
   baseurl: ""
   ```
3. Upload these files — either via the GitHub website
   ("Add file → Upload files", then drag everything in) or with git:
   ```bash
   git init
   git add .
   git commit -m "Initial site"
   git branch -M main
   git remote add origin https://github.com/YOURNAME/YOURNAME.github.io.git
   git push -u origin main
   ```
4. In the repo: **Settings → Pages → Build and deployment → Source:
   "Deploy from a branch" → Branch: `main` / `(root)` → Save.**
5. Wait ~1 minute, then visit `https://YOURNAME.github.io`.

### Option B — a project site at `https://YOURNAME.github.io/REPO`

Same as above, but name the repo anything (e.g. `writeups`) and set:
```yaml
url: "https://YOURNAME.github.io"
baseurl: "/writeups"     # must match the repo name, with a leading slash
```

> If your links look broken (no CSS), it's almost always a wrong `baseurl`.
> Match it to the rule above exactly.

---

## 3. Publish a new writeup

1. Create a file in **`_posts/`** named `YYYY-MM-DD-short-title.md`
   (the date prefix is required — Jekyll uses it for ordering).
2. Start it with this front matter, then write the body in Markdown:

   ```yaml
   ---
   title: "Box Name — One-line summary"
   date: 2025-05-25
   platform: HackTheBox      # HackTheBox / TryHackMe / VulnHub / Proving Grounds…
   difficulty: Medium        # Easy / Medium / Hard / Insane  (controls the colour pill)
   os: Linux                 # Linux / Windows / Other
   ip: 10.10.11.99           # optional — shows in the recon card
   tools: [nmap, ffuf, linpeas]
   tags: [web, ssti, privilege-escalation]
   description: One or two sentences shown on the card and at the top of the post.
   ---

   ## Reconnaissance
   Your writeup goes here...
   ```
3. Commit and push. GitHub rebuilds automatically in under a minute.

The two `example-*` files in `_posts/` are working templates — **duplicate one,
rename it, and overwrite the content.** Delete them when you're ready.

### Handy Markdown bits

````markdown
```bash
nmap -sCV 10.10.11.99      # fenced code block = syntax-highlighted terminal
```

`inline code`   **bold**   > blockquote

![alt text](/assets/img/screenshot.png)   <!-- put images in assets/img/ -->
````

Coloured callout boxes (paste the raw HTML inside your Markdown):

```html
<div class="callout tip"><span class="ic">TIP</span><p>Helpful aside.</p></div>
<div class="callout warn"><span class="ic">NOTE</span><p>Something to watch for.</p></div>
<div class="callout crit"><span class="ic">ROOT</span><p>Critical / compromise step.</p></div>
```

---

## 4. (Optional) Preview locally before pushing

You do **not** need this — GitHub builds the site for you. But if you want to see
changes instantly on your own machine:

```bash
# one-time setup (requires Ruby)
gem install bundler
bundle install

# run it
bundle exec jekyll serve
# open http://localhost:4000
```

**No Ruby? No problem.** Open **`preview.html`** in any browser by double-clicking
it. It's a static snapshot that shows the exact look and feel of the homepage and
a sample writeup — handy for confirming the design before you deploy.

---

## File map

```
_config.yml            ← edit your name/links here
index.html             ← landing page (hero + latest writeups)
writeups.html          ← archive with platform/difficulty filter
about.md               ← your portfolio / CV page
_posts/                ← one Markdown file per writeup  ← YOU ADD FILES HERE
_layouts/              ← page templates (rarely touched)
_includes/             ← nav, footer, head
assets/css/style.css   ← the design system (colours, fonts)
assets/img/            ← screenshots for your writeups
preview.html           ← open locally to preview the design (not deployed)
```

To re-theme, edit the CSS variables at the very top of `assets/css/style.css`
(`--amber` is the accent colour, `--bg` the background).
