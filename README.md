# UI metadata standardisation proposal

Draft 1

## Motivation

User interfaces are important for usability and enforcing conventions across DCCs goes a long way towards makintg artists' work easier.

Currently, OSL defines only a few metadata keywords, representing a subset of Katana's UI capabilities. Then, there are all the other DCCs like Houdini, Maya, Cinema4D, Blender, etc, which support either none, a subset or an entirely different set of UI metadata keywords.

This area is ripe for standardization and having implemented many of these keywords in RenderMan for Maya, I am confident this would greatly improve user experience at a reasonnable cost.

The emphasis of this proposal is to define a somewhat minimal set of UI hints that may be supported by most DCC applications.

## Standard keywords

### Common keywords

These keywords are supported by all widgets.

| Keyword | Type | Description | |
| - | - | - | - |
| `label` | string | A user-friendly name used in the UI | ![std](img/std.svg) |
| `help` | string | A description of the parameter that may appear in the UI | ![std](img/std.svg) |

### `page` ![std](img/std.svg)

Assign the parameter to a page defined as a dot-separated path, i.e. "Specular.Advanced".

| Config Keyword | Type | Description | |
| - | - | - | - |
| `open` | int | If 1, the page UI is expanded by default | ![new](img/new.svg) |

### `widget` ![std](img/std.svg)

Defines which widget type will control the parameter. All parameter types default to a sensible widget if un-defined in the metadata block.

#### `null` ![std](img/std.svg)

---

Parameters using a `null` widget are invisible in the UI.

<details>
<summary>Sample code</summary>

```c
string asset_version = "2.3.0"
[[
    string widget = "null"
]],
```

</details>

#### `number` ![std](img/std.svg)

---

A widget for editable numeric values. This is the default widget used for number parameters.

| Config Keyword | Type | Description | |
| - | - | - | - |
| `min` | float / int | An absolute minimum value for the parameter. | ![std](img/std.svg) |
| `max` | float / int | An absolute maximum value for the parameter. | ![std](img/std.svg) |
| `digits` | int | Number of digits displayed after the decimal point. | ![std](img/std.svg) |
| `slider` | int | If non-zero, display a slider to edit the value. | ![std](img/std.svg) |
| `slidermin` | float / int | Minimum value of the slider. | ![std](img/std.svg) |
| `slidermax` | float / int | Maximum value of the slider. | ![std](img/std.svg) |

<details>
<summary>Sample code</summary>

```c
float ior = 1.5
[[
    string widget = "number",
    float min = 0.0,
    float max = 2.5,
    int slider = 1,
    string help = "The Substrate's Index Of Refraction"
]],
```

</details>

#### `string` ![std](img/std.svg)

---

Default widget type used for string parameters.

<details>
<summary>Sample code</summary>

```c
string variant = "default"
[[
    string widget = "string",
    string label = "Variant",
]],
```

</details>

#### `checkBox` ![std](img/std.svg)

---

An int parameter displayed as a boolean check box.

<details>
<summary>Sample code</summary>

```c
int invert = 0
[[
    string widget = "checkBox",
    string label = "Invert Output"
]],
```

</details>

#### `color` ![std](img/std.svg)

---

A widget used to edit color parameters.

| Config Keyword | Type | Description | |
| - | - | - | - |
| `color_enableFilmlookVis` | int | Enable color-managed UI. | ![new](img/new.svg) |
| `color_restrictComponents` | int | Limit components to [0:1] | ![new](img/new.svg) |

<details>
<summary>Sample code</summary>

```c
color albedo = "default"
[[
    string widget = "color",
    int color_enableFilmlookVis = 1,
    string label = "Albedo"
]],
```

</details>

#### `popup` ![std](img/std.svg)

---

Display a pop-up menu or combox box with literal choices for a string parameter.

| Config Keyword | Type | Description | |
| - | - | - | - |
| `options` | string | A pipe-delimited list of menu items, i.e. `"One\|Two\|Three"` | ![std](img/std.svg) |
| `editable` | int | If non-zero, present an editable field with a side menu | ![new](img/new.svg) |

<details>
<summary>Sample code</summary>

```c
string sss_mode = "default"
[[
    string widget = "popup",
    string options = "burley|random walk"
]],
```

</details>

#### `mapper` ![std](img/std.svg)

---

An menu presenting associative choices (like enums) for int, float and string parameters.

| Config Keyword | Type | Description | |
| - | - | - | - |
| `options` | string | A pipe-delimited list of menu items : value pairs, i.e. `"Add:0\|Over:1\|Multiply:2"` | ![std](img/std.svg) |

<details>
<summary>Sample code</summary>

```c
int compositingMode = 0
[[
    string widget = "mapper",
    string options = "Over:0|Add:1|Screen:3|Mult:2|Overlay:4",
    string label = "Compositing Mode"
]],
```

</details>

### `fileInput` ![new](img/new.svg)

---

