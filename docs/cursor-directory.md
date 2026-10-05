# cursor.directory submission checklist

**Plugin repo (target):** https://github.com/kern-services/ironbrain-mcp  
**Submit form:** https://cursor.directory/plugins/new  
**Pattern reference (live):** https://cursor.directory/plugins/ironbrain-mailcal  
**Product overview:** https://www.ironbrain.de/mcp/ _(marketing; sibling track — don’t block listing)_

Track review/status here (or open a GitHub issue titled `cursor.directory listing` and link it).

## Ready to submit

- [ ] Public GitHub repo live at https://github.com/kern-services/ironbrain-mcp (**PM:** create repo — see below)
- [ ] MIT LICENSE
- [ ] Root README (HTTP MCP preferred, stdio optional, no classic NuGet API keys)
- [ ] `.cursor-plugin/plugin.json` + root `plugin.json`
- [ ] `mcp.json` wires **URL** `https://app.ironbrain.de/api/mcp` + OAuth `CLIENT_ID` `c086ddde-1812-41e1-8781-656e0f72ea0e`
- [ ] Logo at `assets/logo.svg`
- [ ] Submitted at https://cursor.directory/plugins/new
- [ ] Listing review / status noted (publicly live / searchable)

### Submission status

- **Listing URL:** _(pending)_
- **Status:** not submitted
- **Preferred MCP transport:** HTTP / URL (not stdio command)
- **Auto-detect expectation:** server name `ironbrain` with `url` + `auth.CLIENT_ID`

## PM — create public repo (agent lacked org create permission)

Cloud agent `gh` (GitHub App `cursor`) returned:

- GraphQL `createRepository` → `Resource not accessible by integration`
- REST `POST /orgs/kern-services/repos` → **403** `Resource not accessible by integration`

A maintainer with org admin (or repo-create) should:

```bash
# From a machine with org create rights (GitHub UI also fine):
gh repo create kern-services/ironbrain-mcp \
  --public \
  --description "Ironbrain platform MCP — Cursor community plugin (HTTP URL to app.ironbrain.de/api/mcp)" \
  --homepage "https://www.ironbrain.de/" \
  --disable-wiki \
  --clone=false

# Then push the prepared tree:
cd /tmp && tar -xzf /path/to/ironbrain-mcp-listing.tar.gz && cd ironbrain-mcp
git init -b main
git add .
git commit -m "feat: public Cursor plugin listing for Ironbrain HTTP MCP"
git remote add origin https://github.com/kern-services/ironbrain-mcp.git
git push -u origin main
```

GitHub UI alternative: **kern-services** → New repository → name `ironbrain-mcp` → **Public** → no README/license (files already prepared) → create → push as above.

Do **not** make private `kern-services/ironbrain` public.

Optional: open a tracking issue `cursor.directory listing` after the repo exists (MailCal used [issue #1](https://github.com/kern-services/ironbrain-mailcal/issues/1)).

## Submit steps (after repo is public)

1. Confirm `mcp.json` is HTTP/URL (not command-only):
   ```bash
   curl -sS https://raw.githubusercontent.com/kern-services/ironbrain-mcp/main/mcp.json | jq .
   curl -sS https://raw.githubusercontent.com/kern-services/ironbrain-mcp/main/plugin.json | jq .
   ```
2. Open https://cursor.directory/plugins/new
3. Sign in, paste `https://github.com/kern-services/ironbrain-mcp`
4. Confirm auto-detected MCP / plugin components (expect URL MCP `ironbrain`)
5. Click **Submit**
6. Record submission date and listing URL in this file (and the tracking issue)

## Notes

- **Do not** put classic NuGet API keys anywhere. Stdio install uses nuget.org packages published via **Trusted Publishing (OIDC)** only.
- Prefer listing HTTP MCP; stdio remains optional (`mcp.stdio.example.json`).
- Do not change the MailCal product repo for this listing.
- Marketing page at `/mcp/` is owned by a separate worker — listing does not wait on it.
