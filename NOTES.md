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