# Web Responsive compatibility for the April 2026 Odoo 18 checkout

Branch: `fix/web-responsive-odoo18-april-compatibility`.

## Problem

Web Responsive `18.0.1.0.10` fails to render the web client with Odoo core
`6c53f34fef5` (2026-04-13). Its menu extension targets
`//t[@t-if='this.ui.isSmall']`, while that core revision's `web.NavBar.AppsMenu`
uses `env.isSmall`. Owl cannot apply the template extension, destroys the root
component, and leaves a blank page. This failure was reproduced with normal
assets, asset-debug mode, and an isolated database copy.

## Applied change

The complete `web_responsive` folder is restored, without custom source edits,
from repository commit `6cf58da6b37643f26fc57e97920304db321892f2`. Its manifest
version is `18.0.1.0.5`. This is a historical source snapshot, not a newly
packaged upstream release. It precedes the menu-reference change introduced
by `b0dc74d07bddcb55ea82b660314ffd4d1e8b6f6a` on 2026-05-08.

This is a full module rollback, not a two-line patch. It intentionally removes
subsequent module changes, including later translations and UI improvements.
Odoo core and the other add-ons are unchanged. No additional dependencies were
required; `web`, `web_tour`, and `mail` were already installed.

## Validation on 2026-10-30

- A separate PostgreSQL instance held two neutralized copies of the same local
  database, each with its own filestore and all scheduled jobs disabled.
- The current module reproduced the exact XPath/Owl failure in the baseline.
- The unmodified earlier snapshot loaded successfully in the candidate.
- Browser checks covered login, Discuss, the home menu, Accounting dashboard,
  invoice list/form and chatter controls, mobile-width menu search and Contacts,
  and a fresh reload.
- After updating only Web Responsive in the original local database, Discuss,
  menus, Accounting dashboard, invoice list/form, and a mobile-width reload
  passed. The blank-page error was absent.
- Before committing, the module files were checked against the tested snapshot;
  Python/XML syntax and Git whitespace checks passed.

These are focused compatibility checks, not full application regression tests.
A nonfatal invoice-widget warning (`res_partner_many2one`), an unavailable
`sa_invoice_footer_fix` module, and accounting cleanup errors remain separate
issues; this change does not claim to resolve them.

## Deployment and future updates

Use this branch for the tested April 2026 Odoo core revision, restart Odoo, and
update only `web_responsive` in the intended database. Clear/rebuild assets
through Odoo as needed and verify the browser after deployment.

Keep this branch's module snapshot when updating add-ons. Switching back to
the newer `18.0` branch can reintroduce the mismatch. The Git branch records
the compatible code; it is not an automatic dependency/version lock.

Reassess compatibility before updating Odoo core or adopting a newer Web
Responsive revision. Do not assume this older snapshot is appropriate for
newer Odoo 18 checkouts.
