# VideoFormation Blueprint Schema v1.1

A JSON Schema for validating video production pipeline specifications in the VideoFormation system.

## Quick Start

```bash
# Validate your blueprint
npx ajv validate -s blueprint.schema.json -d your-project/blueprint.json

# Or use our local validation scripts
node validate.cjs ../../PROJECTS/YOUR_PROJECT
node test-all-projects.cjs  # Test all projects
```

## What This Schema Does

The VideoFormation Blueprint Schema validates JSON-driven video production specifications, ensuring:

- **Type Safety** - All asset types have proper enum constraints
- **Structure Validation** - Required fields and proper nesting
- **Production Ready** - Rejects template/placeholder values
- **IDE Support** - Autocomplete and validation in VS Code, WebStorm, etc.

## Key Features

### Asset Type Specialization
Instead of generic `AssetEntry`, the schema provides specialized types:

```json
"models": {
  "type": "array",
  "items": { "$ref": "#/$defs/ModelAsset" }
},
"audio": {
  "type": "array", 
  "items": { "$ref": "#/$defs/AudioAsset" }
}
// etc.
```

### Supported Asset Types

| Asset Type | Format Examples | Key Properties |
|------------|----------------|----------------|
| **ModelAsset** | glb, fbx, vrm, usd, obj, blend | lod_levels, poly_count |
| **AudioAsset** | bgm, score, sfx, ambience, voiceover, dialogue | format, sample_rate, loop |
| **VfxAsset** | particle, simulation, compositing, shader | intensity, duration_frames |
| **TextureAsset** | image, procedural, video | channels, scale, tiling |
| **EnvironmentAsset** | hdri, skybox, procedural, plate | exposure, rotation_deg |
| **ShaderAsset** | pbr, unlit, toon, custom | albedo_color, metallic, roughness |
| **TextStyleAsset** | - | font_ref, size_px, color, stroke |
| **GraphicsAsset** | image, svg, video, mogrt, vector | rights management |
| **CaptionFileAsset** | srt, vtt, scc, ass, ssa | language (ISO format) |

### Production Sequence Structure

```json
"production_sequence": {
  "sequences": [
    {
      "sequence_id": "SEQ1",
      "scenes": [
        {
          "scene_id": "S1",
          "shots": [
            {
              "shot_id": "SEQ1.S1.SH1",
              "duration_seconds": 5.0,
              "camera": { ... },
              "captions": { ... }
            }
          ]
        }
      ]
    }
  ]
}
```

## Minimal Valid Blueprint

```json
{
  "$schema": "https://github.com/socialawy/VideoFormation-Schema/blob/main/schemas/blueprint/v1.1.0.json",
  "project_metadata": {
    "system_codename": "videoformation",
    "project_id": "MINIMAL_01",
    "title": "Minimal Video",
    "description": "A simple video project",
    "content_type": "shortfilm",
    "created_date": "2026-02-10",
    "owner": "Your Name"
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

## Validation Examples

### Valid Asset Entry
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

### Invalid Asset Entry
```json
{
  "id": "ENV_SKY_01", 
  "type": "hdri|skybox",  // Pipe-separated not allowed
  "source": "assets/environments/sky.hdr"
}
```

## Using with IDEs

### VS Code
Add to your `.vscode/settings.json`:
```json
{
  "json.schemas": [
    {
      "fileMatch": ["*/blueprint.json"],
      "schema": "https://github.com/socialawy/VideoFormation-Schema/blob/main/schemas/blueprint/v1.1.0.json"
    }
  ]
}
```

### WebStorm/IntelliJ
1. Go to Settings → Languages & Frameworks → Schemas and DTDs
2. Add JSON Schema: `https://github.com/socialawy/VideoFormation-Schema/blob/main/schemas/blueprint/v1.1.0.json`
3. Set file pattern: `*/blueprint.json`

## Integration Examples

### Node.js
```javascript
const Ajv = require('ajv');
const addFormats = require('ajv-formats');

const ajv = new Ajv();
addFormats(ajv);

const validate = ajv.compile(require('./blueprint.schema.json'));
const valid = validate(require('./blueprint.json'));

if (!valid) {
  console.log('Validation errors:', validate.errors);
}
```

### Python
```python
import jsonschema

with open('blueprint.schema.json') as f:
    schema = json.load(f)
with open('blueprint.json') as f:
    data = json.load(f)

try:
    jsonschema.validate(data, schema)
    print("Valid blueprint")
except jsonschema.ValidationError as e:
    print(f"Validation error: {e}")