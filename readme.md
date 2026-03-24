# V3 SDK Sample Project

demo link: https://v3-samples.web.app/

This sample project demonstrates the core capabilities of the VisionThree V3 SDK for rendering and customizing 3D product models in web applications.

## Table of Contents

- [Getting Started](#getting-started)
- [Core Concepts](#core-concepts)
- [Sample Files](#sample-files)
- [API Reference](#api-reference)

---

## Getting Started

### Loading the V3 Bundle

The V3 SDK is loaded via a CDN script. Add this to your HTML `<head>`:

```html
<script
  src="https://dev.admin.visionthree.io/scripts/v3/bundle.min.js"
  type="text/javascript"
></script>
```

**Note:** There are different bundle URLs for different environments:

- Staging: `https://dev.admin.visionthree.io/scripts/v3/bundle.min.js`
- Production: `https://view.visionthree.io/scripts/v3/bundle.min.js`

---

## Core Concepts

### 1. v3-viewer Component

The `<v3-viewer>` custom HTML element renders interactive 3D models. It's the primary component for displaying products.

**Basic Usage:**

```html
<v3-viewer
  id="v3id"
  name="main"
  subdomain="simba"
  shadow="true"
  setting="{
        model: 'Didsbury Bed No Storage',
        background: '#fcfcfc',
        materials: {
            UPHOLSTERY: {code: 'SILVER GREY'},
            BEDDING: {code: 'BASEBEDDING'},
            LEGS: {code: 'CHROME'},
            HEADBOARDBACK: {code: 'BASE'},
            PLASTICPARTS: {code: 'BASE'}
        },
        pan: 1000,
        camera: {angle_x: 65.57, angle_y: 84, distance: 2.5}
    }"
  style="width: 100%; height: 600px;"
>
</v3-viewer>
```

**Key Attributes:**

- `name`: Identifier for the viewer instance (e.g., "main") - used to reference it via `v3.main`
- `subdomain`: Organization/project subdomain (e.g., "simba")
- `shadow`: Enable/disable shadows on the model
- `setting`: JSON configuration object containing:
  - `model`: Name of the 3D model to load
  - `background`: Background color (hex code)
  - `materials`: Object mapping material slots to material codes
  - `pan`: Pan speed/sensitivity (if omitted, panning will be disabled for the model)
  - `camera`: Initial camera position (angle_x, angle_y, distance)

### 2. Accessing the Viewer via JavaScript

Once the viewer is initialized, you can access it through the global `v3` object:

```javascript
// Access by name attribute
v3.main; // References the viewer with name="main"
```

**Important Methods:**

#### `changeMaterial(materialSlot, materialCode)`

Changes a specific material on the model in real-time.

```javascript
v3.main.changeMaterial("UPHOLSTERY", "VELVET CHOCOLATE");
v3.main.changeMaterial("LEGS", "WOODEN");
```

#### `setModel(configObject)`

Loads a completely different model configuration.

```javascript
v3.main.setModel({
  model: "Didsbury Bed 4 Drawer",
  materials: {
    UPHOLSTERY: { code: "SILVER GREY" },
    BEDDING: { code: "BASEBEDDING" },
    LEGS: { code: "CHROME" },
    HEADBOARDBACK: { code: "BASE" },
    PLASTICPARTS: { code: "BASE" },
  },
});
```

#### `switchToViewer(viewerName)` and `switchToMain()`

Switch between different viewer instances (useful for popup previews).

```javascript
v3.main.switchToViewer("VIEW1"); // Switch to alternate view
v3.main.switchToMain(); // Return to main view
```

### 3. Virtual Photography System

The V3 SDK includes a powerful Virtual Photography System that generates static product images on-demand using URL parameters.

**Base URL Format:**

```
https://vphoto.img.haze.tools/{subdomain}_{model_name}?[parameters]
```

**Parameters:**

- `zoom`: Zoom level (e.g., 1)
- `output_size`: Output dimensions (e.g., "300x250")
- `size`: Render resolution (e.g., "1024x1024")
- `background`: Background color in hex without # (e.g., "fcfcfc")
- `pan`: Pan offset (e.g., "0.x0.0x0.0")
- `angle_x`: Horizontal rotation angle (0-360)
- `angle_y`: Vertical rotation angle (0-360)
- `distance`: Camera distance from model
- `materials`: Comma-separated list of material assignments (e.g., "UPHOLSTERY:SILVER GREY,LEGS:CHROME")

**Example:**

```javascript
function add_image(size, data) {
  var el = document.createElement("img");
  el.src =
    "https://vphoto.img.haze.tools/simba_" +
    data.model +
    " " +
    data.type +
    "?zoom=" +
    data.zoom +
    "&output_size=" +
    size +
    "&size=1024x1024" +
    "&background=fcfcfc" +
    "&pan=0.x0.0x0.0" +
    "&angle_x=" +
    data.rotate_x +
    "&angle_y=" +
    data.rotate_y +
    "&distance=2.5" +
    "&materials=UPHOLSTERY:" +
    data.color +
    ",BEDDING:BASEBEDDING,LEGS:" +
    data.leg;
  document.body.appendChild(el);
}

// Generate thumbnails from different angles
add_image("300x250", {
  model: "Didsbury Bed",
  type: "No Storage",
  color: "SILVER GREY",
  leg: "CHROME",
  rotate_x: 50,
  rotate_y: 80,
  zoom: 1,
});
```

This system allows you to:

- Generate product thumbnails dynamically
- Create image galleries without storing static files
- Update images when materials change
- Show different angles of the same product

---

## Sample Files

### 1. product_detail_main.html

**Purpose:** Basic product detail page with a static 3D viewer.

**Features:**

- Single v3-viewer instance
- Pre-configured model with fixed materials
- Interactive 3D rotation (user can rotate the model with mouse/touch)
- Shadow rendering enabled

**Use Case:** Simple product display page where no customization is needed.

**Key Code:**

```html
<v3-viewer
  id="v3id"
  setting="{
        model:'Didsbury Bed No Storage',
        background:'#fcfcfc',
        materials:{
            UPHOLSTERY :{code:'SILVER GREY' },
            BEDDING:{ code :'BASEBEDDING'},
            LEGS:{ code : 'CHROME'},
            HEADBOARDBACK : { code:'BASE'},
            PLASTICPARTS : { code:'BASE'}
        },
        pan:1000,
        camera:{angle_x:65.57,angle_y:84,distance:2.5 }
    }"
  shadow="true"
  subdomain="simba"
  name="main"
  style="width: 100%; height: 600px;"
>
</v3-viewer>
```

---

### 2. product_detail_thumbnail.html

**Purpose:** Demonstrates the Virtual Photography System for generating static product images.

**Features:**

- Interactive 3D viewer
- Dynamically generated thumbnail images using the Virtual Photography API
- Multiple camera angles in thumbnail gallery
- No additional asset storage required

**Use Case:** Product pages that need both an interactive 3D viewer and static image gallery/thumbnails.

**Key Code:**

```javascript
// Function to generate virtual photographs
function add_image(size, data) {
  var el = document.createElement("img");
  el.className = "thumbnail";
  el.src =
    "https://vphoto.img.haze.tools/simba_" +
    data.model +
    " " +
    data.type +
    "?zoom=" +
    data.zoom +
    "&output_size=" +
    size +
    "&size=1024x1024&background=fcfcfc&pan=0.x0.0x0.0" +
    "&angle_x=" +
    data.rotate_x +
    "&angle_y=" +
    data.rotate_y +
    "&distance=2.5" +
    "&materials=UPHOLSTERY:" +
    data.color +
    ",BEDDING:BASEBEDDING,LEGS:" +
    data.leg;
  par.appendChild(el);
}

// Generate thumbnails from different angles
add_image("300x250", {
  model: "Didsbury Bed",
  type: "No Storage",
  color: "SILVER GREY",
  leg: "CHROME",
  rotate_x: 50,
  rotate_y: 80,
  zoom: 1,
});
add_image("250x250", {
  model: "Didsbury Bed",
  type: "No Storage",
  color: "SILVER GREY",
  leg: "CHROME",
  rotate_x: 0,
  rotate_y: 70,
  zoom: 1,
});
add_image("350x250", {
  model: "Didsbury Bed",
  type: "No Storage",
  color: "SILVER GREY",
  leg: "CHROME",
  rotate_x: 90,
  rotate_y: 85,
  zoom: 1,
});
```

**How it Works:**

1. The `add_image()` function constructs URLs dynamically based on model configuration
2. Each URL points to the Virtual Photography service with specific parameters
3. The service renders the 3D model server-side and returns a static image
4. Images are created on-demand - no pre-rendering or storage needed

---

### 3. product_detail_popup.html

**Purpose:** Full-featured product configurator with material selection, model variants, and dynamic thumbnails.

**Features:**

- Interactive 3D viewer with real-time material changes
- Material selection popup with preview
- Model variant switching (No Storage, 2 Drawer, 4 Drawer, Ottoman)
- Leg material selection
- Dynamic thumbnail regeneration when configuration changes
- Secondary viewer (`v3-image`) for material preview
- State management for tracking current configuration

**Use Case:** E-commerce product configurator where users can customize products before purchase.

**Key Components:**

#### A. Global State Management

```javascript
var global_model_status = {
  model: "Didsbury Bed",
  type: "No Storage",
  color: "SILVER GREY",
  leg: "CHROME",
};
```

#### B. Model Update Function

```javascript
global_model_status.update_model = function () {
  var model = {
    model: "Didsbury Bed " + global_model_status.type,
    materials: {
      UPHOLSTERY: { code: global_model_status.color },
      BEDDING: { code: "BASEBEDDING" },
      LEGS: { code: "CHROME" },
      HEADBOARDBACK: { code: "BASE" },
      PLASTICPARTS: { code: "BASE" },
    },
  };
  v3.main.setModel(model);
  global_model_status.update_thumbnail();
};
```

#### C. Dynamic Thumbnail Updates

```javascript
global_model_status.update_thumbnail = function () {
  global_model_status.thumbnail_parent.innerHTML = "";

  // Regenerate thumbnails with current configuration
  add_image("300x250", {
    model: "Didsbury Bed",
    type: global_model_status.type,
    color: global_model_status.color,
    leg: global_model_status.leg,
    rotate_x: 50,
    rotate_y: 80,
    zoom: 1,
  });
  add_image("300x250", {
    model: "Didsbury Bed",
    type: global_model_status.type,
    color: global_model_status.color,
    leg: global_model_status.leg,
    rotate_x: 0,
    rotate_y: 70,
    zoom: 1,
  });
  add_image("300x250", {
    model: "Didsbury Bed",
    type: global_model_status.type,
    color: global_model_status.color,
    leg: global_model_status.leg,
    rotate_x: 90,
    rotate_y: 80,
    zoom: 1,
  });
};
```

#### D. Material Change with Group Visibility

The leg selection demonstrates advanced usage by combining group visibility control with material changes:

```html
<!-- Control Panel -->
<input
  onclick="global_model_status.leg = this.value;
           v3.main.groupVisibility('METALLEGS','WOODLEGS');
           v3.main.changeMaterial('METALLEGS',this.value);"
  value="CHROME"
/>
<input
  onclick="global_model_status.leg = this.value;
           v3.main.groupVisibility('WOODLEGS','METALLEGS');
           v3.main.changeMaterial('WOODLEGS',this.value);"
  value="WOODEN"
/>
```

This pattern:

1. Updates the application state
2. Shows the appropriate leg group (metal or wood) while hiding the other
3. Applies the material to the visible group

#### E. Material Preview with Real-time Preview

<!-- Popup with Preview -->
<div id="colorpopup" style="display: none;">
  <input
    type="button"
    value="VELVET CHOCOLATE"
    onclick="v3.main.changeMaterial('UPHOLSTERY',this.value)"
  />
  <input
    type="button"
    value="SILVER GREY"
    onclick="v3.main.changeMaterial('UPHOLSTERY',this.value);
                    global_model_status.color = this.value;"
  />

  <!-- Preview viewer -->

<v3-image
name="VIEW1"
viewer="main"
style="width: 400px;height: 300px;"

> </v3-image>

</div>
```

#### F. v3-image Component

The `<v3-image>` component creates a linked view of the main viewer:

```html
<v3-image
  name="VIEW1"
  viewer="main"
  style="width: 400px;height: 300px;"
></v3-image>
```

- `viewer="main"`: Links to the main viewer instance
- Shares the same 3D model but can show different camera angles
- Changes made in the popup immediately reflect in both viewers

**Workflow:**

1. User clicks "Colors" button → popup opens and switches to VIEW1
2. User clicks material options → `changeMaterial()` updates the model in real-time
3. User clicks "Apply" → updates global state, regenerates thumbnails, closes popup
4. User clicks "Cancel" → reverts to previous material, closes popup

---

## API Reference

### Global Object: `v3`

Access viewer instances through the global `v3` object using their name attribute:

```javascript
v3.main; // Viewer with name="main"
v3.VIEW1; // Viewer with name="VIEW1"
v3.anyName; // Viewer with name="anyName"
```

### Methods

#### `v3.[name].changeMaterial(materialSlot, materialCode)`

Changes a specific material slot on the model.

**Parameters:**

- `materialSlot` (string): The material slot identifier (e.g., "UPHOLSTERY", "LEGS")
- `materialCode` (string): The material code to apply (e.g., "SILVER GREY", "CHROME")

**Example:**

```javascript
v3.main.changeMaterial("UPHOLSTERY", "VELVET CHOCOLATE");
```

---

#### `v3.[name].setModel(config)`

Loads a new model configuration (useful for variant switching).

**Parameters:**

- `config` (object): Configuration object with:
  - `model` (string): Model name
  - `materials` (object): Material slot assignments

**Example:**

```javascript
v3.main.setModel({
  model: "Didsbury Bed 4 Drawer",
  materials: {
    UPHOLSTERY: { code: "SILVER GREY" },
    BEDDING: { code: "BASEBEDDING" },
    LEGS: { code: "CHROME" },
    HEADBOARDBACK: { code: "BASE" },
    PLASTICPARTS: { code: "BASE" },
  },
});
```

---

#### `v3.[name].switchToViewer(viewerName)`

Switches the rendering focus to a different viewer instance.

**Parameters:**

- `viewerName` (string): Name of the viewer to switch to

**Example:**

```javascript
v3.main.switchToViewer("VIEW1");
```

---

#### `v3.[name].switchToMain()`

Returns the rendering focus to the main viewer.

**Example:**

```javascript
v3.main.switchToMain();
```

---

#### `v3.[name].groupVisibility(showGroup, hideGroup)`

Controls the visibility of model groups/parts. This is useful when you need to show different physical components based on user selection (e.g., metal legs vs wooden legs).

**Parameters:**

- `showGroup` (string): Name of the group to make visible
- `hideGroup` (string): Name of the group to hide

**Example:**

```javascript
// Show metal legs and hide wooden legs
v3.main.groupVisibility("METALLEGS", "WOODLEGS");

// Show wooden legs and hide metal legs
v3.main.groupVisibility("WOODLEGS", "METALLEGS");
```

**Common Usage Pattern:**
Often combined with `changeMaterial()` to both switch visible parts and apply materials:

```javascript
// Switch to metal legs with chrome finish
v3.main.groupVisibility("METALLEGS", "WOODLEGS");
v3.main.changeMaterial("METALLEGS", "CHROME");

// Switch to wooden legs with wooden finish
v3.main.groupVisibility("WOODLEGS", "METALLEGS");
v3.main.changeMaterial("WOODLEGS", "WOODEN");
```

---

## Loading Materials from V3 Admin Configurator

### The `initElement()` Function

The V3 SDK provides a way to dynamically load available materials directly from the V3 Admin Configurator instead of hardcoding them. This ensures your material options stay synchronized with the admin panel configuration.

**Implementation Example:**

```javascript
var initElement = function () {
  // Listen for the model ready event
  document
    .getElementById("v3id")
    .addEventListener(v3.EVENT.READY_MODEL, async (e) => {
      // Function to create a material button
      function add_color_button(name, code) {
        var el = document.createElement("input");
        el.type = "button";
        el.value = name;

        el.onclick = function () {
          v3.main.changeMaterial("UPHOLSTERY", code);
          global_model_status.color = code;
        };

        document.getElementById("color_options").appendChild(el);
      }

      // Get materials from the V3 Admin Configurator for 'StepOne' group
      var stepOneMaterials = v3.main.getGroupMaterials("StepOne");

      console.log(stepOneMaterials);
      // Returns array of material objects:
      // [
      //   { name: 'SILVER GREY', code: 'SILVER_GREY' },
      //   { name: 'VELVET CHOCOLATE', code: 'VELVET_CHOCOLATE' },
      //   ...
      // ]

      // Dynamically create buttons for each material
      for (var i in stepOneMaterials) {
        add_color_button(stepOneMaterials[i].name, stepOneMaterials[i].code);
      }

      // Optionally get all model configuration options
      console.log(v3.main.getModelOptions());

      // Example output:
      // body: {group_name: 'UPHOLSTERY'}
      // config: "setting"
      // legs: {group_name: "LEGS"
      // options: [ "METALLEGS","WOODLEGS"]
      //     });
    });
};
```

**Usage in HTML:**

```html
<v3-viewer
  id="v3id"
  name="main"
  subdomain="simba"
  onready="initElement()"
  setting="{...}"
  style="width: 100%; height: 600px;"
>
</v3-viewer>

<!-- Container for dynamically generated material buttons -->
<div id="color_options"></div>
```

### Key Methods

#### `v3.[name].getGroupMaterials(groupName)`

Retrieves all available materials for a specific material group from the V3 Admin Configurator.

**Parameters:**

- `groupName` (string): The name of the material group (e.g., 'StepOne', 'StepTwo')

**Returns:**
Array of material objects with:

- `name` (string): Display name of the material
- `code` (string): Material code used with `changeMaterial()`

**Example:**

```javascript
var materials = v3.main.getGroupMaterials("StepOne");
// [
//   { name: 'SILVER GREY', code: 'SILVER_GREY' },
//   { name: 'VELVET CHOCOLATE', code: 'VELVET_CHOCOLATE' }
// ]
```

---

#### `v3.[name].getModelOptions()`

Retrieves the complete configuration options for the current model.

**Returns:**
Object containing model configuration including available materials, groups, and settings.

**Example:**

```javascript
var options = v3.main.getModelOptions();
console.log(options);
```

### Event: `v3.EVENT.READY_MODEL`

This event fires when the 3D model is fully loaded and ready for interaction.

**Usage:**

```javascript
document
  .getElementById("v3id")
  .addEventListener(v3.EVENT.READY_MODEL, async (e) => {
    // Model is now loaded and ready
    // Safe to call getGroupMaterials(), getModelOptions(), etc.
  });
```

**Why This Matters:**

- Material data is only available after the model loads
- Attempting to call `getGroupMaterials()` before this event will fail
- Always wrap material loading logic inside this event listener

### Benefits of Dynamic Material Loading

1. **Automatic Synchronization**: Changes in the V3 Admin Configurator automatically reflect in your application
2. **No Hardcoding**: No need to manually maintain material lists in your code
3. **Flexibility**: Support for multiple material groups and configurations
4. **Centralized Management**: Material options managed in one place (V3 Admin)
5. **Future-Proof**: New materials added to the configurator appear automatically

### Complete Workflow Example

```javascript
// 1. Define initialization function
var initElement = function () {
  document
    .getElementById("v3id")
    .addEventListener(v3.EVENT.READY_MODEL, async (e) => {
      // 2. Get materials from configurator
      var fabricMaterials = v3.main.getGroupMaterials("StepOne");
      var legMaterials = v3.main.getGroupMaterials("StepTwo");

      // 3. Create UI elements dynamically
      fabricMaterials.forEach((material) => {
        createMaterialButton(material.name, material.code, "UPHOLSTERY");
      });

      legMaterials.forEach((material) => {
        createMaterialButton(material.name, material.code, "LEGS");
      });
    });
};

// 4. Attach to viewer via onready attribute
// <v3-viewer onready="initElement()" ...>
```

---

## Material Change System Explained

The material change system works by assigning material codes to material slots on the 3D model.

### Material Slots

Each 3D model has predefined material slots that can be customized:

- `UPHOLSTERY`: Fabric covering
- `LEGS`: Leg material/finish (generic slot)
- `METALLEGS`: Metal leg components (when using group visibility)
- `WOODLEGS`: Wooden leg components (when using group visibility)
- `BEDDING`: Bedding material
- `HEADBOARDBACK`: Headboard back panel
- `PLASTICPARTS`: Plastic components

**Note:** Some models use separate groups for different physical parts (e.g., `METALLEGS` vs `WOODLEGS`). Use the `groupVisibility()` method to show/hide these groups.

### Material Codes

Each slot accepts specific material codes defined in the VisionThree system:

- Upholstery: "SILVER GREY", "VELVET CHOCOLATE", "VENICE CREAM", etc.
- Legs: "CHROME", "WOODEN", etc.

### How Changes Work

**Method 1: Real-time Change (Individual Material)**

```javascript
v3.main.changeMaterial("UPHOLSTERY", "VELVET CHOCOLATE");
```

- Updates only the specified material slot
- Changes appear immediately in the viewer
- Preserves all other materials

**Method 2: Complete Model Reload (Variant Switch)**

```javascript
v3.main.setModel({
  model: "Didsbury Bed 4 Drawer", // Different variant
  materials: {
    UPHOLSTERY: { code: "SILVER GREY" },
    BEDDING: { code: "BASEBEDDING" },
    LEGS: { code: "CHROME" },
    HEADBOARDBACK: { code: "BASE" },
    PLASTICPARTS: { code: "BASE" },
  },
});
```

- Loads a completely different model (variant)
- Reapplies all materials to the new model
- Used when switching between product variants (e.g., storage options)

### Best Practices

1. **Track State**: Maintain a global state object to track current configuration
2. **Update Thumbnails**: Regenerate Virtual Photography thumbnails after material changes
3. **Preview Before Apply**: Use secondary viewers (v3-image) for material previews
4. **Validate Materials**: Ensure material codes match those available in the VisionThree system
5. **User Feedback**: Provide visual feedback during material loading

---

## Summary

The V3 SDK provides:

- **Easy Integration**: Simple script tag and custom HTML elements
- **Real-time 3D**: Interactive product visualization
- **Material Customization**: Live material changes without page reload
- **Virtual Photography**: Dynamic image generation without asset storage
- **Multiple Views**: Support for primary and preview viewers
- **Production Ready**: Used in e-commerce applications for product configuration

For questions or support, please contact the VisionThree team.
