# NEWRISK — County Risk Explorer

A planner's tool for the ECHRRA-K health facility climate risk assessment.
The user picks a county, gets an overview, then explores by hazard, by sub-county
or by overall score. Single self-contained page; no build step, no server code.

## Files
    index.html                        the whole tool
    logos/newrisk.png                 project mark
    logos/nihr.png                    funder lockup
    logos/kemri.png  logos/oxford.png partner marks
    data/ECHRRA-K_..._REDCap_export.csv   assessments in the live REDCap layout (690 cols)
    data/ECHRRA-K_..._derived_scores.json derived scores the tool reads

Keep the folder structure — index.html references `logos/` and `data/` by relative path.

## Deploying

### Option A — subdomain of newrisk.org (recommended)
Your Google Site stays exactly as it is; this gets its own address.

1. Create a free site on Cloudflare Pages, Netlify or GitHub Pages and upload this
   whole folder (all three accept a drag-and-drop).
2. In Squarespace → Settings → Domains → newrisk.org → DNS Settings, add a CNAME:
       Host:  risk                      (giving risk.newrisk.org)
       Value: the hostname the platform gives you, e.g. newrisk.pages.dev
3. Add risk.newrisk.org as a custom domain in the hosting platform so it issues the
   TLS certificate. Allow up to an hour for DNS to propagate.
4. On the Google Site, add a nav link to https://risk.newrisk.org

### Option B — embed inside the existing Google Site
Do steps 1–3 first; the tool still needs to live at a real URL. Then in Google Sites:
Insert → Embed → By URL. Note that Google Sites' "Embed code" option will NOT work —
it caps pasted HTML far below this page's size.

## Adding a county
Counties are declared near the top of the script block:

    const COUNTIES = [
      {name: "Kirinyaga", live: false, note: "Assessment not yet scheduled"},
      {name: "Kilifi",    live: true,  note: "Assessed March–May 2026"},
      ...

Set `live: true` once a county has data. The tool currently holds one county's
facilities in its `DATA` object; for a second county, add a `county` field to each
facility record and filter on it in `shown()`.

## Updating the data
The page reads nothing but the `DATA` object embedded in its script block. To publish
a real assessment round, regenerate that object with the same shape as
data/ECHRRA-K_Kilifi_derived_scores.json and replace it.

## Before publishing real assessments
- Remove the "Demonstration data" notice on the county screen.
- Replace the schematic map with surveyed sub-county boundaries if you have them.
- Confirm facility MFL codes against the Kenya Master Health Facility List.
- Add the NIHR award number to the funding statement in the footer.
- Partner logos were captured from newrisk.org; swap in the originals from each
  organisation's brand pack before going live.

## Publishing on GitHub Pages

This folder is ready to be a Pages site as-is (`.nojekyll` is included so Pages
serves the files verbatim rather than running Jekyll over them).

    cd newrisk-site
    git init -b main
    git add .
    git commit -m "NEWRISK county risk explorer"
    gh repo create newrisk-risk-explorer --public --source=. --push

Then turn Pages on — either in the repo's Settings → Pages (Source: "Deploy from a
branch", Branch: `main`, folder `/ (root)`), or from the command line:

    gh api -X POST repos/:owner/newrisk-risk-explorer/pages \
      -f 'source[branch]=main' -f 'source[path]=/'

The site appears at `https://<your-github-username>.github.io/newrisk-risk-explorer/`
within a minute or two.

### Putting it on risk.newrisk.org instead
1. Add a file called `CNAME` at the repo root containing one line: `risk.newrisk.org`
2. In Squarespace → Settings → Domains → newrisk.org → DNS Settings, add a CNAME
   record: Host `risk`, Value `<your-github-username>.github.io`
3. In the repo's Settings → Pages, set the Custom domain to `risk.newrisk.org` and
   tick "Enforce HTTPS" once the certificate is issued.

Note that the repo must be **public** for Pages on a free GitHub account.
