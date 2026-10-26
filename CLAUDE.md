# tmpmkscouts

## What this is
A one-page holding site for mkscouts.org, Milton Keynes District Scouts. It went up on 21 Oct 2026 while the WordPress host patched a PHP security issue, and came down on 22 Oct once the server was fixed. It's kept so the page can go back up in minutes next time. It carries joining, volunteering, The Quarries, volunteer tools and emergency contacts, all taken from the live site.

## Stack
- One static file, `index.html`. `404.html` is an exact copy so old WordPress links land on the holding page.
- No scripts and no external requests. System fonts, Scouts logo inlined as SVG, no links back to WordPress.
- Served by GitHub Pages from `main`, only while it's needed.
- mkscouts.org DNS is on Cloudflare. The live site normally runs on WordPress at 34sp (46.183.8.214).

## How to deploy
Bring it up:
1. Refresh the "Updated" date and any facts in `index.html`, then `cp index.html 404.html` and merge to `main`.
2. `gh api -X POST repos/stuartridout/tmpmkscouts/pages -f 'source[branch]=main' -f 'source[path]=/'`
3. `gh api -X PUT repos/stuartridout/tmpmkscouts/pages -f cname=mkscouts.org`
4. Cloudflare DNS, all DNS only (grey cloud): delete the root A (46.183.8.214) and the `www` CNAME (mkscouts.org). Add root A 185.199.108.153, 185.199.109.153, 185.199.110.153, 185.199.111.153, root AAAA 2606:50c0:8000::153 to 2606:50c0:8003::153, and `www` CNAME stuartridout.github.io.
5. `gangshownew`, `quarries` and `staging` also point at 34sp. Leave them, or point them at 192.0.2.1 proxied with a Cloudflare Redirect Rule to https://mkscouts.org/. Without the rule they show a Cloudflare error.
6. Once GitHub has issued the certificate: `gh api -X PUT repos/stuartridout/tmpmkscouts/pages -F https_enforced=true`

Take it down:
1. Cloudflare: delete the temporary records first, because import only adds. Then restore root A 46.183.8.214, `www` CNAME mkscouts.org, and `gangshownew`, `quarries`, `staging` A 46.183.8.214, all DNS only.
2. `gh api -X PUT repos/stuartridout/tmpmkscouts/pages -F cname=null`
3. `gh api -X DELETE repos/stuartridout/tmpmkscouts/pages`
4. `git pull`. Setting and clearing the domain each commit to `main`.

Throughout: leave MX, autodiscover, DKIM, SPF, DMARC and the `_github-pages-challenge-stuartridout` TXT alone.

## Conventions
- Land changes by PR. `main` is what Pages serves when it's on.
- `index.html` is the only source. Copy it to `404.html` after every change.
- Copy is short and plain: no em dashes, no semicolons, contractions.
- End sessions with /wrap.

## Status
Last: 22 Oct 2026 - Holding page built from mkscouts.org's own content and served on mkscouts.org through GitHub Pages for the PHP patch window from 21 Oct. Rolled back on 22 Oct: DNS matches the original zone, WordPress checked on every name, custom domain cleared and Pages off.
Next: Nothing live. Before reuse, refresh the page's date and facts. Owed on the live site: fix the groups page's joiners email (no .org) and confirm the IT address on Volunteer Resources (emailit@ or it@).

## Out of scope
Mirroring the WordPress site. Anything that needs a server, forms or cookies.
