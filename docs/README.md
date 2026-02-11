# VideoFormation Blueprint Schema v1.1

A JSON Schema for validating video production pipeline specifications.

## Overview

The VideoFormation Blueprint Schema provides a standard way to describe video productions: which assets to use, in what order, with what timing, transitions, text overlays, camera moves, and more.

**Key benefits:**
- **Type Safety** — All asset types have proper enum constraints
- **Structure Validation** — Required fields and proper nesting
- **Production Ready** — Rejects template/placeholder values
- **IDE Support** — Autocomplete and validation in VS Code, WebStorm, etc.

## Schema URLs

| Version | URL | Status |
|---------|-----|--------|
| Latest | `https://socialawy.github.io/VideoFormation-Schema/schemas/blueprint/latest.json` | Recommended |
| v1.1.0 | `https://socialawy.github.io/VideoFormation-Schema/schemas/blueprint/v1.1.0.json` | Stable |

---

## Minimal Valid Blueprint

```json
{
  "$schema": "https://socialawy.github.io/VideoFormation-Schema/schemas/blueprint/latest.json",
  "project_metadata": {
    "project_id": "MINIMAL_01",
    "title": "Minimal Video",
    "content_type": "shortfilm"
  },
  "global_settings": {
    "fps": 30,
    "resolution_master": { "width": 1920, "height": 1080 }
  },
  "production_sequence": {
    "sequences": []
  }
}
```

## Schema Structure
```
blueprint.json
├── $schema (string, uri)
│
├── project_metadata (required)
│   ├── project_id (required)
│   ├── title (required)
│   ├── description
│   ├── content_type (enum)
│   ├── created_date
│   ├── owner
│   └── versioning
│
├── global_settings (required)
│   ├── fps (required)
│   ├── resolution_master (required)
│   ├── timebase
│   ├── pixel_aspect_ratio
│   ├── color_pipeline
│   ├── audio_defaults
│   └── naming_conventions
│
├── asset_manifest
│   ├── models[]
│   ├── audio[]
│   ├── textures[]
│   ├── environments[]
│   ├── shaders[]
│   ├── vfx[]
│   ├── graphics[]
│   ├── text_styles[]
│   ├── caption_files[]
│   └── ...
│
├── production_sequence (required)
│   └── sequences[]
│       └── scenes[]
│           └── shots[]
│               ├── shot_id (required)
│               ├── duration_seconds (required)
│               ├── camera
│               ├── dialogue[]
│               ├── audio_timeline[]
│               └── vfx_timeline[]
│
├── delivery_profiles[]
├── render_instructions
└── localization
```
## Asset Types
The schema provides specialized types for different asset categories:

| Asset Type | Formats | Key Properties |
| ---------- | ------- | -------------- |
| ModelAsset | glb, fbx, vrm, usd, obj, blend | lod_levels, poly_count |
| AudioAsset | bgm, score, sfx, ambience, voiceover, dialogue | format, sample_rate, loop |
| VfxAsset | particle, simulation, compositing, shader | intensity, duration_frames |
| TextureAsset | image, procedural, video | channels, scale, tiling |
| EnvironmentAsset | hdri, skybox, procedural, plate | exposure, rotation_deg |
| ShaderAsset | pbr, unlit, toon, custom | albedo_color, metallic, roughness |
| TextStyleAsset | - | font_ref, size_px, color, stroke |
| GraphicsAsset | image, svg, video, mogrt, vector | rights management |
| CaptionFileAsset | srt, vtt, scc, ass, ssa | language (ISO format) |


### Example: Audio Asset
```json
{
  "id": "BGM_MAIN",
  "type": "bgm",
  "source": "assets/audio/main-theme.mp3",
  "properties": {
    "format": "mp3",
    "sample_rate": 48000,
    "channels": 2,
    "loop": true
  }
}
```
### Example: Environment Asset
```json
{
  "id": "ENV_SKY_01",
  "type": "hdri",
  "source": "assets/environments/sky.hdr",
  "preload": true,
  "properties": {
    "exposure": 0.5,
    "rotation_deg": 45
  }
}
```

## Production Sequence
> The production hierarchy: Sequences → Scenes → Shots
```json
{
  "production_sequence": {
    "sequences": [
      {
        "sequence_id": "SEQ01",
        "title": "Opening",
        "scenes": [
          {
            "scene_id": "S01",
            "title": "Intro Scene",
            "shots": [
              {
                "shot_id": "SEQ01.S01.SH01",
                "duration_seconds": 5.0,
                "camera": {
                  "type": "static",
                  "lens_mm": 35,
                  "position": { "x": 0, "y": 1.6, "z": 5 }
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```
## Content Types
Valid values for project_metadata.content_type:

- shortfilm
- commercial
- music_video
- documentary
- social
- tutorial
- trailer
- corporate
- animation
- other

## Validation
- Invalid: Pipe-separated values
```json
{
  "type": "hdri|skybox"
}
```
❌ Schema rejects template placeholders


- Valid: Single enum value
```json
{
  "type": "hdri"
}
```
✅ Passes validation

## IDE Integration

### VS Code: Add to .vscode/settings.json:
```json
{
  "json.schemas": [
    {
      "fileMatch": ["**/blueprint.json"],
      "url": "https://socialawy.github.io/VideoFormation-Schema/schemas/blueprint/latest.json"
    }
  ]
}
```
### JetBrains (WebStorm, IntelliJ)
1. Settings → Languages & Frameworks → Schemas and DTDs → JSON Schema Mappings
2. Add new mapping:
    - Name: VideoFormation Blueprint
    - Schema URL: https://socialawy.github.io/VideoFormation-Schema/schemas/blueprint/latest.json
    - File path pattern: */blueprint.json

## Programmatic Validation

### Node.js
```javascript
const Ajv = require('ajv');
const addFormats = require('ajv-formats');

async function validateBlueprint(blueprint) {
  const ajv = new Ajv({ strictSchema: false });
  addFormats(ajv);

  const response = await fetch(
    'https://socialawy.github.io/VideoFormation-Schema/schemas/blueprint/latest.json'
  );
  const schema = await response.json();
  const validate = ajv.compile(schema);

  const valid = validate(blueprint);
  return { valid, errors: validate.errors };
}
```
### Python
```python
import json
import urllib.request
from jsonschema import validate, ValidationError

schema_url = "https://socialawy.github.io/VideoFormation-Schema/schemas/blueprint/latest.json"

with urllib.request.urlopen(schema_url) as response:
    schema = json.loads(response.read())

with open("blueprint.json") as f:
    blueprint = json.load(f)

try:
    validate(blueprint, schema)
    print("✅ Valid blueprint")
except ValidationError as e:
    print(f"❌ Validation error: {e.message}")
```
### CLI (using ajv-cli)
```bash
npm install -g ajv-cli ajv-formats

ajv validate \
  -s https://socialawy.github.io/VideoFormation-Schema/schemas/blueprint/latest.json \
  -d blueprint.json \
  --spec=draft2020 \
  -c ajv-formats
```

## Examples

### 📁 Blueprint Examples

| Example | Description | Use Case |
|---------|-------------|----------|
| **[minimal.json](../examples/minimal.json)** | Bare minimum required fields - perfect starting template | Learning the basics |
| **[full.json](../examples/full.json)** | Comprehensive example showcasing all available schema features | Reference implementation |
| **[1_talking_head.json](../examples/1_talking_head.json)** | Simple single-camera interview or presentation setup with minimal editing | Talking head videos |
| **[2_interview.json](../examples/2_interview.json)** | Two-person interview with multiple camera angles and b-roll cutaways | Multi-camera interviews |
| **[3_explainer_slides.json](../examples/3_explainer_slides.json)** | Educational content combining narration with presentation slides and graphics | Educational content |
| **[4_product_showcase.json](../examples/4_product_showcase.json)** | Product demonstration with multiple angles, text overlays, and call-to-action | Product demonstrations |
| **[5_cinematic_trailer.json](../examples/5_cinematic_trailer.json)** | High-production trailer with advanced transitions, color grading, and sound design | Cinematic content |
| **[6_multi_language.json](../examples/6_multi_language.json)** | Content prepared for localization with multiple subtitle tracks and audio versions | International content |

**Interactive Viewer**: View these examples in the interactive [viewer.html](../viewer.html) or browse them in the [examples gallery](../examples.html).

Changelog
See [CHANGELOG.md](https://github.com/SocialAwy/VideoFormation-Schema/blob/master/CHANGELOG.md) for version history.

License
[Apache 2.0](https://github.com/SocialAwy/VideoFormation-Schema/blob/master/LICENSE)