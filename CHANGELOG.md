# Changelog

All notable changes to `qalainau/filament-warp-quicklook` are documented here.

## [Unreleased]

## [1.0.2] - 2026-11-28

### Fixed

- **The slide-over could stay open and empty on a busy server, covering the table.** When Livewire dropped or cancelled the answer to opening a record (it does this under load, when you move away, or when you press next/previous quickly), the slide-over stayed open without its content and the page behind it could not be clicked. Opening is now asked once more, and if there is still no answer the slide-over closes. Hover cards and the pinned card are also asked once more instead of showing "not found". Previous/next and back are never sent twice (that would move two records), and none of these show an error in the browser console any more.

## [1.0.1] - 2026-11-19

### Fixed

- **`warpQuickLook is not defined` in the console** when a page re-renders with Livewire before Quick Look's script has arrived (for example when moving from another page to a list in a panel with other Warp plugins; Quick Look worked after a moment). Filament's `x-load` holds back the Alpine component until its script is downloaded, but a Livewire re-render inside another Alpine component evaluates `warpQuickLook()` earlier. Quick Look now registers an empty `warpQuickLook` in the panel's scripts, which the real one replaces when it arrives.

## [1.0.0] - 2026-11-19

### Added

- `->quickLook()` on every table column and infolist entry: a hover card on the related record, and a slide-over with its details on click. It follows the relationship in the column name (`customer.name`), a relationship you name (`->quickLook('order.customer')`), a closure that returns the record, or the row itself. `BelongsTo`, `HasOne`, `MorphOne`, `HasOneThrough` and `MorphTo` are supported; `card: false` and `slideOver: false` give only the click or only the card.
- Works without configuration for a model that has a resource in the panel: the card uses the record title and the global search details, the slide-over uses the infolist of the resource (or its form, read-only, or the record's attributes when the resource has neither).
- `QuickLook`, the description of how a model looks, written once per model: `title()`, `description()`, `image()`, `icon()`, `badge()`, `details()`, `schema()`, `actions()`, `openUrl()`, `width()`, `label()`. Put it in a static `quickLook()` method on the resource.
- `FilamentWarpQuickLookPlugin::look()` and `QuickLook::register()` for models without a resource (with `query()`, `authorize()` and `scopedToTenant()`), also outside a panel with `<x-warp-quicklook::host />`.
- Select eye buttons show a compact preview on hover or keyboard focus, with a **View details** button for the full slide-over. Candidates in the select's dropdown are previewed on hover or keyboard navigation without changing the selected value.
- `Select::quickLook()`: an eye button at the end of a select that opens the record currently chosen, even before the form is saved, without losing what was typed.
- `QuickLookAction`: a table row action that opens the slide-over of the row's record.
- The slide-over: previous and next through the records of the same table column (buttons, ↑ ↓ and J K, “3 of 25”), a related record inside it replaces the content with a Back link, footer actions and “Open page”, Ctrl or ⌘ + click to open the page in a new tab, focus returns to where it came from (after previous/next, to the record that was shown). It opens on top of other modals and Esc closes only it, takes the full width on a phone and a tap on touch screens. A record that is deleted or forbidden while it is open shows “not found” on the next request.
- A hover card with a loading state, a keyboard-focus trigger, Esc to close, a short browser cache that is dropped when another Livewire component finishes a request or the slide-over closes, and a reply that arrives late is ignored. Dates are written the way the locale writes them, in the Filament timezone.
- Keyboard in tables whose rows are links (`recordUrl()`): the cell link stays the tab stop, its card says “Press Space to view details”, and Space opens the details while Enter still follows the link.
- `<x-warp-quicklook::link>` and `QuickLook::attributes()` to use the same behavior in your own Blade views and in the output of other plugins.
- Panel settings: `hoverDelay()`, `cacheFor()`, `hoverCards()`, `indicator()`, `slideOverWidth()`.
- Permissions: a record the viewer may not see is not marked at all. Resources use `canView()`; other models use `authorize()` or the policy's `view`.
- Filament multi-tenancy: records are found with the resource's tenant-scoped query, or the ownership relationship of the panel for models without a resource; a model that cannot be scoped throws instead of showing everyone's records.
- Signed tokens (panel and tenant included) in the page, and a fresh lookup and permission check on every request. A wrong signature, a missing record, another tenant's record and a forbidden record all give the same “not found”. A key that does not fit the key column (integer, UUID, ULID, or anything the database rejects) is also “not found”, on SQLite, MySQL, MariaDB and PostgreSQL.
- Cards are drawn from plain text, never HTML; only `http(s)` and relative URLs are used.
- Ten languages: English, Japanese, German, French, Spanish, Italian, Brazilian Portuguese, Korean, simplified and traditional Chinese.
- Works on the canvas table of Warp Table (`->warp()`): the dotted underline, the card, the details on click or Enter, previous and next, and the focus back on the viewed cell. Nothing to set up.
- **Static analysis**: the methods added with `macro()` (`->quickLook()` on columns, entries and selects) are known to Larastan: the plugin ships `extension.neon`, a PHPStan extension that reads Filament's macros (loaded automatically with `phpstan/extension-installer`; otherwise add it to `includes`). See “Static analysis” in the README.
- Hover cards sit right beside the visible text by default; `->quickLook(cardAnchor: 'column')` places them outside the column when scanning vertically. On narrow screens they use the space above or below.
- Pin a hover preview while browsing other records, with collapse, expand, and unpin controls. Pins are cleared when leaving the page.
- Compare a pinned preview with another record of the same model in a Filament modal. Preview fields are aligned, differing values highlighted, and both records are fetched again before display.
- One-click copying of preview descriptions and detail values, including pinned previews and comparison cells, using Filament’s standard clipboard component and feedback.
