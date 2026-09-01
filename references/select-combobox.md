# Select, radio, checkbox, and combobox

Choose by task and option scale, not visual fashion.

## Control choice

- Small fixed mutually exclusive list: radio group when comparison matters; native `<select>` when compactness matters.
- Independent boolean: checkbox. Use a switch only for an immediate on/off setting, not agreement or “submit this preference later.”
- Multiple independent options: checkbox group. For many searchable choices, use the project's accessible multi-select/combobox if one exists.
- Large or remote dataset: combobox/autocomplete with search, loading, empty, error, and selected states.

Do not render hundreds of options in a giant custom dropdown. Native select has strong browser behavior; do not replace it solely to restyle it.

## Semantics and state

Every control/group needs an accessible label. Use fieldset/legend for radio and checkbox groups. Define whether an empty selection is permitted, what the placeholder option means, and whether “clear” returns to null or another default.

Do not submit display labels as stable identifiers unless they are the domain value. Keep option identity separate from localized display. Treat client-submitted IDs as untrusted and recheck existence, availability, and authorization server-side.

Disabled options should explain why when that matters. Remember disabled controls/options are not authority and may be omitted from submission.

## Combobox requirements

Prefer a library/design-system implementation already tested against the relevant ARIA pattern. Verify rather than assume:

- keyboard opening, arrow navigation, Enter selection, Escape behavior, Tab, and focus return;
- announced label, expanded state, active option, position/count, selection, loading, empty, and error states;
- typed text versus selected value and behavior when focus leaves;
- clear action and removal of selected chips;
- pointer/touch targets and viewport/keyboard positioning;
- async request debounce, cancellation/race handling, pagination, and retry;
- RTL, long translated labels, and Unicode search.

Do not add ARIA roles to a visual dropdown without implementing the full behavior.

## Async values

Preserve selected items even when they are outside the current search page. Handle stale/deleted options explicitly. Cache only where project conventions and data sensitivity allow. Revalidate the final submitted identifier on the server because search results can become outdated.

Country, language, and timezone lists require stable codes and localized names. Do not infer language from country or timezone from locale; allow product/domain defaults while preserving user choice.

