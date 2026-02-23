# new_request_page.hbs

A customized Zendesk Help Center template for the **New Request (ticket submission) page**. Built on top of the standard Zendesk `new-request-form` component, this template extends default behavior with tag-based field filtering and form-specific subject text injection.



---

## Overview

This Handlebars (`.hbs`) template replaces the default Zendesk new request page. It renders the standard ticket submission form while applying two custom use cases before rendering:

1. **Tag-Based Dropdown Filtering** — Hides specific dropdown options from ticket fields based on the current user's tags.
2. **Form-Specific Subject Text** — Pre-fills the subject field with custom text depending on which ticket form is active.
3. More uses cases are coming.


---

## Requirements

- Zendesk Guide (Help Center) with a customizable theme
- The built-in `new-request-form` ES module provided by Zendesk

---

## Template Structure

### JavaScript (ES Module)

The `<script type="module">` block handles all custom logic before the form renders.

#### Data Sources

| Variable | Description |
|---|---|
| `window.HelpCenter.user.organizations` | List of organizations the current user belongs to |
| `window.HelpCenter.user.tags` | Tags assigned to the current user |
| `new_request_form` | Zendesk Handlebars object containing ticket fields, form info, etc. |
| `settings` | Custom theme settings from `manifest.json` |

---

## Use Cases

### Use Case 1: Hide Dropdown Options by User Tag

**Function:** `applyTagBasedOptionFilter(request_form_clone, userTags, rulesByTag)`

Removes specific options from a dropdown ticket field if the current user has a matching tag.

**Configuration object (`remove_options_by_tag`):**

```js
const remove_options_by_tag = {
    'user_tag': {
        fieldId: '46560480244884',  // Target ticket field ID
        options: ['Duda']           // Option label(s) to remove
    }
};
```

To add more rules, extend the object with additional tag keys. Each tag can map to a single rule object or an array of rule objects.

---

### Use Case 2: Pre-fill Subject by Ticket Form ID

**Function:** `applySubjectTextChange(request_form_clone, subjectTextByFormId)`

Automatically sets the value of the `request[subject]` field when a specific ticket form is loaded.

**Configuration object (`subjectTextByFormId`):**

```js
const subjectTextByFormId = {
    '46560545585812': 'Estas en el formulario de Demo Plantilla',
};
```

Add additional form ID / subject text pairs as needed.

---

## Customization

### Changing the Target Field ID

Update `tipo_caso_field_id` with the ID of your custom ticket field:

```js
const tipo_caso_field_id = 'YOUR_FIELD_ID_HERE';
```

### Adding More Tag Rules

```js
const remove_options_by_tag = {
    'tag_one': {
        fieldId: 'FIELD_ID',
        options: ['Option A', 'Option B']
    },
    'tag_two': [
        { fieldId: 'FIELD_ID_1', options: ['Option C'] },
        { fieldId: 'FIELD_ID_2', options: ['Option D'] }
    ]
};
```

### Adding More Subject Overrides

```js
const subjectTextByFormId = {
    'FORM_ID_1': 'Subject text for form 1',
    'FORM_ID_2': 'Subject text for form 2',
};
```

---

## Debugging

The following values are logged to the browser console on page load to assist with development:

- `userTags` — tags on the current user
- `orgs` — organizations the user belongs to
- `request_form_clone` — full form object before modification
- `request_form_clone.ticket_form_field.value` — the active ticket form ID

Remove or guard these `console.log` calls before deploying to production.

---

## References

- [Zendesk Help Center Templates – new_request_page](https://developer.zendesk.com/api-reference/help_center/help-center-templates/new_request_page/)
- [Customizing the Settings panel (manifest.json)](https://support.zendesk.com/hc/en-us/articles/4408846524954)
