# V3 Planner Guide

This guide documents the planner experience in this folder.

The planner is used to build custom sofas and connect different modular sofa sections into one composition.

## What The Planner Is For

Use the planner when you need to:

- Build custom sofa layouts from modular units
- Connect modules into sectional configurations
- Change materials across the composed sofa
- Capture the current section/code state from planner events

## File In This Folder

- `index.html`: Planner sample page using `<v3-planner>`

## Quick Start

Load the V3 SDK bundle:

```html
<script
  src="https://dev.view.visionthree.io/scripts/v3/bundle.min.js"
  type="text/javascript"
></script>
```

Add the planner element:

```html
<v3-planner
  id="v3id"
  custom="{}"
  setting="{
    model:'TRUMAN LARGE PLANNER',
    background:'#f4f4f4',
    materials:{'UPHOLSTERY':{code:'ROCDE'}},
    pan:1000,
    camera:{angle_x:65.57,angle_y:84,distance:2.5}
  }"
  shadow="true"
  subdomain="andrewmartin"
  name="main"
  onready="initElement()"
  style="width: 1440px; height: 650px;"
></v3-planner>
```

## Planner Initialization

In this sample, `initElement()` attaches a listener for planner updates:

```javascript
function initElement() {
  document.getElementById("v3id").addEventListener("changed", function (event) {
    console.log(event);
    document.getElementById("Sections").innerHTML = event.data.sections_code;
  });
}
```

### Why This Event Matters

The `changed` event gives you the latest planner state, including section codes (`event.data.sections_code`).

This is useful for:

- Showing selected modules in the UI
- Syncing a summary panel
- Storing a modular sofa configuration for cart/order flows

## Updating Sofa Materials Across Sections

Use `changeSectionsMaterial()` on the planner instance:

```javascript
await v3.main.changeSectionsMaterial({
  UPHOLSTERY: { code: "ROCAU" },
});
```

Revert example:

```javascript
await v3.main.changeSectionsMaterial({
  UPHOLSTERY: { code: "ROCDE" },
});
```

## Suggested Modular Sofa Workflow

1. Start with a planner-ready model in the `setting.model` field.
2. Let users connect sofa modules in the planner canvas.
3. Listen to `changed` events and store `sections_code`.
4. Apply fabric/material updates with `changeSectionsMaterial()`.
5. Persist selected modules and materials to your backend/cart.

## Notes

- Access the planner instance through `v3.main` because `name="main"` is set.
- Keep your event listeners inside `onready` setup to ensure the planner is loaded.
- Keep material codes aligned with the catalog configured for your subdomain.

## Troubleshooting

- If no planner appears, verify the V3 bundle URL and network access.
- If events do not fire, confirm the listener is attached to the planner element with id `v3id`.
- If material changes fail, verify the material key/value pair (for example `UPHOLSTERY` and `ROCAU`) is valid for the configured model.
