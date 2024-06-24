# UI metadata standardisation proposal

Draft 1

## Motivation

User interfaces are important for usability and enforcing conventions across DCCs goes a long way towards makintg artists' work easier.

Currently, OSL defines only a few metadata keywords, representing a subset of Katana's UI capabilities. Then, there are all the other DCCs like Houdini, Maya, Cinema4D, Blender, etc, which support either none, a subset or an entirely different set of UI metadata keywords.

This area is ripe for standardization and having implemented many of these keywords in RenderMan for Maya, I am confident this would greatly improve user experience at a reasonnable cost.

The emphasis of this proposal is to define a somewhat minimal set of UI hints that may be supported by most DCC applications.

## Common keywords

These keywords are supported by all widgets.

| Keyword | Type | Description | |
| - | - | - | - |
| `label` | string | A user-friendly name used in the UI | ![std](img/std.svg) |
| `help` | string | A description of the parameter that may appear in the UI | ![std](img/std.svg) |

## `page` ![std](img/std.svg)

Assign the parameter to a page defined as a dot-separated path, i.e. "Specular.Advanced".

| Widget options | Type | Description | |
| - | - | - | - |
| `open` | int | If 1, the page UI is expanded by default | ![new](img/new.svg) |

##### Sample code

```c
color specularTint = 1
[[[
    string page = "Specular",
    string label = "Tint",
]]],
float specularIOR = 1.5
[[
    string page = "Specular",
    string label = "Index of Refraction",
    float min = 1.0
]]
int specularModel = 1
[[
    string page = "Specular",
    string label = "Model",
    string widget = "mapper",
    string options = "Beckmann:0|GGX:1"
]]
```

## `widget` ![std](img/std.svg)

Defines which widget type will control the parameter. All parameter types default to a sensible widget if un-defined in the metadata block.

### Widget: `null` ![std](img/std.svg)

Parameters using a `null` widget are invisible in the UI.

##### Sample code

```c
string asset_version = "2.3.0"
[[
    string widget = "null"
]],
```

### Widget: `number` ![std](img/std.svg)

A widget for editable numeric values. This is the default widget used for number parameters.

| Widget options | Type | Description | |
| - | - | - | - |
| `min` | float / int | An absolute minimum value for the parameter. | ![std](img/std.svg) |
| `max` | float / int | An absolute maximum value for the parameter. | ![std](img/std.svg) |
| `digits` | int | Number of digits displayed after the decimal point. | ![std](img/std.svg) |
| `slider` | int | If non-zero, display a slider to edit the value. | ![std](img/std.svg) |
| `slidermin` | float / int | Minimum value of the slider. | ![std](img/std.svg) |
| `slidermax` | float / int | Maximum value of the slider. | ![std](img/std.svg) |

##### Sample code

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

### Widget: `string` ![std](img/std.svg)

Default widget type used for string parameters.

##### Sample code

```c
string variant = "default"
[[
    string widget = "string",
    string label = "Variant",
]],
```

### Widget: `checkBox` ![std](img/std.svg)

An int parameter displayed as a boolean check box.

##### Sample code

```c
int invert = 0
[[
    string widget = "checkBox",
    string label = "Invert Output"
]],
```

### Widget: `color` ![std](img/std.svg)

A widget used to edit color parameters.

| Widget options | Type | Description | |
| - | - | - | - |
| `color_enableFilmlookVis` | int | Enable color-managed UI. | ![new](img/new.svg) |
| `color_restrictComponents` | int | Limit components to [0:1] | ![new](img/new.svg) |

##### Sample code

```c
color albedo = "default"
[[
    string widget = "color",
    int color_enableFilmlookVis = 1,
    string label = "Albedo"
]],
```

### Widget: `popup` ![std](img/std.svg)

Display a pop-up menu or combox box with literal choices for a string parameter.

| Widget options | Type | Description | |
| - | - | - | - |
| `options` | string | A pipe-delimited list of menu items, i.e. `"One\|Two\|Three"` | ![std](img/std.svg) |
| `editable` | int | If non-zero, present an editable field with a side menu | ![new](img/new.svg) |

##### Sample code

```c
string sss_mode = "default"
[[
    string widget = "popup",
    string options = "burley|random walk"
]],
```

### Widget: `mapper` ![std](img/std.svg)

An menu presenting associative choices (like enums) for int, float and string parameters.

| Widget options | Type | Description | |
| - | - | - | - |
| `options` | string | A pipe-delimited list of menu items : value pairs, i.e. `"Add:0\|Over:1\|Multiply:2"` | ![std](img/std.svg) |

##### Sample code

```c
int compositingMode = 0
[[
    string widget = "mapper",
    string options = "Over:0|Add:1|Screen:3|Mult:2|Overlay:4",
    string label = "Compositing Mode"
]],
```

### Widget: `fileInput` ![new](img/new.svg)

A string attributes containing a file path. There should be an associated button to open a file browser and select the file.

##### Sample code

```c
string texture = ""
[[
    string widget = "fileInput",
    string label = "Texture"
]],
```

### Widget: `colorRamp` ![new](img/new.svg)

> [!NOTE]
> OSL's spline interpolation shadeops only work on static arrays when most users actually want dynamic arrays. This forces the shader writer to copy multiple dynamic arrays to static arrays. I don't know the exact cost of that operation but it would be great to get rid of this limitation.

> [!NOTE]
> Start and end knots need to be repeated n-times depending on the interpolation scheme. It would be nice to add an option flag to let the spline shadeop automatically select the correct number of repetitions.

> [!NOTE]
> It would be good to make sure DCC app widgets support all standard OSL interpolation modes. Last time I checked Maya did not.

Color ramps depend on multiple parameters to provide knots position, knots value and knots interpolation.

The main parameter is an int parameter with a `colorRamp` widget. Its value is the number of currently used knots. This representation allows support of fixed-size ramps.

The metadata MUST define 3 additional keywords (`rampKnots`, `rampColors` and `rampInterp`) to be functional. If any of theme is missing, an error should be raised.

> [!NOTE]
> Should we extend oslc to validate metadata ?

| Widget options | Type | Description | |
| - | - | - | - |
| `rampKnots` | string | Name of the parameter storing knot positions. | ![new](img/new.svg) |
| `rampColor` | string | Name of the parameter storing knot colors. | ![new](img/new.svg) |
| `rampInterp` | string | Name of the parameter storing interpolation bases. </br><ul><li>If that parameter is a string array, each span can have a different interpolation basis.</li><li>If that parameter is a string, all spans use the same interpolation basis.</li></ul> | ![new](img/new.svg) |
| `gradientHeight` | int | The height of the gradient widget in pixels. | ![new](img/new.svg) |

##### Sample code

```c
int colorMap = 4
[[
    string label = "Color Map",
    string widget = "colorRamp",
    string rampKnots = "colorMap_Knots",
    string rampColors = "colorMap_Colors",
    string rampInterp = "colorMap_Interpolation",
    int gradientHeight = 25,
]],
float colorMap_Knots[] = {0, 0,
                          1, 1}
[[
    int isDynamicArray = 1,
    string widget = "null",
]],
float colorMap_Knots[] = {color(0), color(0),
                          color(1), color(1)}
[[
    int isDynamicArray = 1,
    string widget = "null",
    int restrictComponents = 1
]],
float colorMap_Interpolation[] = {"catmull-rom", "catmull-rom",
                                  "catmull-rom", "catmull-rom"}
[[
    int isDynamicArray = 1,
    string widget = "null",
]],
```

### Widget: `floatRamp` ![new](img/new.svg)

The main parameter is an int parameter with a `colorRamp` widget.
* Its value is the number of currently used knots. This representation allows support of fixed-size ramps.

The metadata MUST define 3 additional keywords (`rampKnots`, `rampColors` and `rampInterp`) to be functional.
* If any of theme is missing, an error should be raised.

| Widget options | Type | Description | ![new](img/new.svg) |
| - | - | - | - |
| `rampKnots` | string | Name of the parameter storing knot positions. | ![new](img/new.svg) |
| `rampColor` | string | Name of the parameter storing knot colors. | ![new](img/new.svg) |
| `rampInterp` | string | Name of the parameter storing interpolation bases. </br><ul><li>If that parameter is a string array, each span can have a different interpolation basis.</li><li>If that parameter is a string, all spans use the same interpolation basis.</li></ul> | ![new](img/new.svg) |
| `gradientHeight` | int | The height of the gradient widget in pixels. | ![new](img/new.svg) |

##### Sample code

```c
int attenuationCurve = 5
[[
    string label = "Attenuation Curve",
    string widget = "floatRamp",
    string rampKnots = "attenCrv_Knots",
    string rampFloats = "attenCrv_Values",
    string rampInterp = "attenCrv_Interpolation",
    int gradientHeight = 25,
]],
float attenCrv_Knots[] = {0, 0, 0.5, 1, 1}
[[
    int isDynamicArray = 1,
    string widget = "null",
]],
float attenCrv_Values[] = {0, 0, 0.3333, 1, 1}
[[
    int isDynamicArray = 1,
    string widget = "null"
]],
float attenCrv_Interpolation[] = {"catmull-rom", "catmull-rom",
                                  "catmull-rom", "catmull-rom",
                                  "catmull-rom"}
[[
    int isDynamicArray = 1,
    string widget = "null",
]],
```

## Arrays

OSL support array parameters of any types and the metadata allows writers to decide which widget should be used.

* **Dynamic arrays** allow the addition, re-ordering and removal of array elements.
* **UI Structs** are a way to display multiple arrays as if they were a single array of structs, which isn't natively supported by OSL, but useful to group, for example, layer parameters. If the arrays are dynamic, it will be the responsability of the DCC app to keep all participating arrays at the same size at all times.

| Array options | Type | Description | |
| - | - | - | - |
| `size` | int | **Static arrays**: the array size.</br>**Dynamic arrays**: the number of existing members on node creation. Defaults to <kbd>-1</kbd> for empty. | [new](img/new.svg) |
| `isDynamicArray` | int | Specifies if the array can be resized. | [new](img/new.svg) |
| `uiStruct` | string | Associate this array with a named struct-like UI where members of multiple arrays are displayed interlaced. | [new](img/new.svg) |
| `tupleSize` | int | Specifies the tuple size (column count). This is passed to the child widgets. | [new](img/new.svg) |
| `tupleGroupSize` | int | Specifies the number of tuples each child widget should handle. | [new](img/new.svg) |

##### Sample code

```c
int triplanarAxisEnable[3] = {1, 1, 1}
[[
    string label = "Enable Axis"
    string widget = "checkBox",
    int size = 3,
    string uiStruct = "Triplanar Axes",
]],
string triplanarAxisTexture[3] = {"", "", ""}
[[
    string label = "Texture"
    string widget = "fileInput",
    int size = 3,
    string uiStruct = "Triplanar Axes",
]],
float triplanarAxisRepeat[3] = {1.0, 1.0, 1.0}
[[
    string label = "Enable Axis",
    string widget = "number",
    int size = 3,
    string uiStruct = "Triplanar Axes",
    int slider = 1,
    float min = 0.0001,
    float slidermax = 10.0
]],
```
