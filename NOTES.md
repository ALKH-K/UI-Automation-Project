# Notes

## Locators
- Selector preference: data-test > id > class
- `#foo` = id, `.foo` = class, `[data-test="foo"]` = any attribute
- Avoid selectors containing record ids (e.g. item-4-title-link) — they break when data changes
- Find selectors with Pick locator in UI mode, or DevTools Inspect

## Commands
- `npx playwright test --ui` — interactive mode
- `npx playwright codegen URL` — record clicks into code
- npx playwright codegen https://myurl - opens the codegen window to check locators


## Notes on the cart test:
item-4-title-link is the link wrapping the product name, and the 4 is SauceDemo's internal id for the backpack
but it ties the test to a database id, if the database is changed test will fail
await expect(page.locator('[data-test="inventory-item-name"]')).toHaveText('Sauce Labs Backpack');
describes what the element is rather than which record it happens to be, more reliable and doesnt depend on the database

## Refactoring: beforeEach

**Problem:** the same four login lines were repeated in every test.

**Fix:** `test.beforeEach()` at the top of the file, running before each test.

```typescript
test.beforeEach(async ({ page }) => {
  // login steps here
});
```

**Key points**
- Runs fresh for every test, not once per file
- Each test still gets its own browser and clean state, so tests stay independent
- Independent tests can run in parallel and don't cascade failures
- Only use it for setup that's *identical* across tests — if tests need different setup (e.g. different users), keep it inline
- `afterEach` exists too, for cleanup after each test

## Codegen

`npx playwright codegen https://www.saucedemo.com` opens a real browser and writes
code as you click. Good for finding selectors on an unfamiliar app.

**Always clean up the output:**
- Removes needed: `.click()` before `.fill()` — fill focuses the field itself
- Codegen records stray clicks (e.g. clicking a heading to select text)
- It never writes assertions — add those yourself
- Clicking an element proves nothing; only `expect()` verifies

## Debugging failures

**"Timeout waiting for element"** usually means the wrong page, not a bad selector.
Check the snapshot in UI mode to see where the test actually got to.

Example: adding an item to the cart leaves you on the inventory page. The Checkout
button only exists on the cart page, so the test must navigate there first.

## Test structure

- One file per feature area: login, cart, checkout
- `beforeEach` for setup that's identical across tests in the file
- Share the setup, not the subject — login belongs in the hook for cart tests,
  but stays inline in login tests where credentials are what's being verified
- Assert on values (totals, names) where possible, not just on navigation