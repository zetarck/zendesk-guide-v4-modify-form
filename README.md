# new_request_page.hbs

A customized Zendesk Help Center template for the **New Request (ticket submission) page**. Built on top of the standard Zendesk `new-request-form` module, this template adds client-side logic to control form behavior based on user tags, ticket form IDs, and field values — without requiring backend changes.

Created by **Pablo Zarricueta** — CX Consultant.

---

## Features

- **Tag-based dropdown filtering** — Removes specific dropdown options from ticket fields depending on the logged-in user's tags.
- **Dynamic subject text** — Pre-fills or overrides the subject field based on the active ticket form ID.
- **Custom field descriptions** — Overrides the hint/helper text of specific custom fields at render time.
- **Custom field pre-fill (text)** — Sets a default text value for one or more custom text fields.
- **Custom field pre-fill (dropdown)** — Sets a default selected value for one or more custom dropdown fields.
- **Attachment validation** — Blocks form submission and shows an inline error if a required attachment is missing for certain dropdown values.
- **DDOM-hiding utilities** — Observers for hiding the ticket form selector and specific custom fields via the DOM (available for use when needed).

---

## Configuration

All customization is done by editing the constants near the top of the `<script>` block.

### Field IDs

```js
const tipo_caso_field_id      = '46560480244884'; // First dropdown field
const tipo_solicitud_field_id = '39445201654164'; // Second dropdown field
const numero_orden_field_id   = '39445254638612'; // Text field
```

Replace these values with your own Zendesk custom ticket field IDs.

---

### Tag-Based Option Filtering (`remove_options_by_tag`)

Removes dropdown options from a field when the user has a specific tag.

```js
const remove_options_by_tag = {
    'user_tag': {
        fieldId: tipo_caso_field_id,
        options: ['Duda']  // Option label(s) to remove
    }
};
```

Multiple rules can be added by uncommenting and extending the object.

---

### Subject Text by Form ID (`subjectTextByFormId`)

Pre-fills the subject field when a specific ticket form is active.

```js
const subjectTextByFormId = {
    '46560545585812': 'Demo Custom text',
};
```

---

### Custom Field Descriptions (`customFieldDescriptionById`)

Overrides the helper/hint text shown below a field.

```js
const customFieldDescriptionById = {
    '46560480244884': 'Description set by code',
};
```

---

### Custom Text Field Values (`customFieldTextValue`)

Pre-fills a text field with a specific value.

```js
const customFieldTextValue = {
    '39445254638612': 'Custom text value',
};
```

---

### Custom Dropdown Field Values (`customFieldDropdownValue`)

Pre-selects a dropdown value for a field.

```js
const customFieldDropdownValue = {
    '46560480244884': 'demo_reclamo',
    '39445201654164': 'acceso_a_cuenta',
};
```

---

### Attachment Validation (`attachmentRequiredRules`)

Blocks form submission if no file is attached when a specific dropdown value is selected.

```js
const attachmentRequiredRules = [
    {
        fieldId: tipo_caso_field_id,
        triggerValues: ['acceso_a_cuenta'],
        alertMessage: 'Debes adjuntar un archivo antes de enviar el formulario para este tipo de caso.',
    },
];
```

Multiple rules can be added to the array. Each rule defines which field, which value(s) trigger the requirement, and what error message to display.

---

## How It Works

1. On page load, the script clones the `new_request_form` object provided by Zendesk.
2. Each transformation function (`applyTagBasedOptionFilter`, `applySubjectTextChange`, etc.) receives the clone and returns a modified version.
3. The modified clone is passed to `renderNewRequestForm()`, which renders the form into `#new-request-form`.
4. After render, a `MutationObserver` waits for the submit button to appear, then attaches a capture-phase click listener to enforce attachment validation before the form submits.

---

## References

- [Zendesk New Request Page Template Docs](https://developer.zendesk.com/api-reference/help_center/help-center-templates/new_request_page/)
- [Zendesk Theme manifest.json / Settings](https://support.zendesk.com/hc/en-us/articles/4408846524954)
