# VideoFormation Blueprint Reference (v1.1)

This document provides a human-readable reference for the VideoFormation Blueprint Schema v1.1. It describes the structure, required fields, and data types used to define a video production project.

## Top-Level Structure

The root of the blueprint JSON file contains high-level configuration and containers for assets and sequences.

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `project_metadata` | Object | **Yes** | Project identification, versioning, and core metadata. |
| `global_settings` | Object | **Yes** | Technical specifications like FPS, resolution, and color pipeline. |
| `production_sequence` | Object | **Yes** | The core timeline hierarchy: Sequences → Scenes → Shots. |
| `asset_manifest` | Object | No | Registry of all media assets (models, audio, environments) used. |
| `localization` | Object | No | Language settings and text style definitions for multi-language support. |
| `render_instructions` | Object | No | Settings for preview and master render outputs. |
| `delivery_profiles` | Array | No | Definitions for output formats (e.g., YouTube, Archive). |
| `production_tracking_light` | Object | No | Simple status tracking, priorities, and approvals. |
| `rights_and_credits` | Object | No | Copyright notices and music cue sheet data. |

---

## Project Metadata (`project_metadata`)

Identity and classification information for the project.

| Property | Type | Required | Description | Example |
|----------|------|----------|-------------|---------|
| `project_id` | String | **Yes** | Unique alphanumeric ID. | `"CAMPAIGN_2026_Q3"` |
| `title` | String | **Yes** | Human-readable title. | `"Product Launch v2"` |
| `content_type` | Enum | No | Type of content. | `"commercial"`, `"shortfilm"`, `"social"` |
| `created_date` | Date | No | ISO 8601 date. | `"2026-03-15"` |
| `owner` | String | No | Creator or department. | `"Creative Team"` |
| `versioning` | Object | No | Version tracking. | `{ "project_version": "1.0" }` |

---

## Global Settings (`global_settings`)

Technical constraints and defaults applied to the entire project.

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `fps` | Integer | **Yes** | Frames per second (e.g., 24, 30, 60). |
| `resolution_master` | Object | **Yes** | `{ "width": 1920, "height": 1080 }` |
| `color_pipeline` | Object | No | Color space settings (ACEScg, Rec.709). |
| `audio_defaults` | Object | No | Sample rate and loudness targets (LUFS). |
| `editorial_policy` | Object | No | Handle lengths for editing (e.g., head/tail frames). |

---

## Asset Manifest (`asset_manifest`)

The `asset_manifest` object organizes assets by category. Each category is an array of asset objects.

### Common Asset Properties
All assets share these base fields:

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `id` | String | **Yes** | Unique reference ID (e.g., `MOD_HERO`). |
| `source` | String | No | Path or URI to the file. |
| `rights` | Object | No | License info, attribution, source URL. |

### Specific Asset Types

#### Models & Characters
Used in `models`, `characters`, `props`.

| Property | Type | Description |
|----------|------|-------------|
| `type` | Enum | `glb`, `fbx`, `metahuman`, `obj`, `usd`. |
| `lod_levels` | Array | `["high", "medium", "low"]`. |
| `properties` | Object | `{ "poly_count": 50000, "cast_shadows": true }`. |
| `wardrobe_refs` | Array | List of costume model IDs (Characters only). |

#### Audio
Used in `audio`.

| Property | Type | Description |
|----------|------|-------------|
| `type` | Enum | `bgm`, `sfx`, `voiceover`, `dialogue`. |
| `format` | Enum | `stereo`, `5.1`, `mono`. |
| `properties` | Object | `{ "default_volume_db": -6.0, "loop": true }`. |

#### Environments & Locations
Used in `environments`, `locations`.

| Property | Type | Description |
|----------|------|-------------|
| `type` | Enum | `hdri`, `skybox`, `plate`. |
| `properties` | Object | `{ "exposure": 0.5, "rotation_deg": 180 }`. |

#### Text & Styles
Used in `fonts`, `text_styles`, `caption_files`.

| Property | Type | Description |
|----------|------|-------------|
| `font_ref` | String | ID of the font asset. |
| `size_px` | Integer | Font size in pixels. |
| `color` | Hex | Text color (e.g., `#FFFFFF`). |
| `safe_margins` | Object | `{ "bottom_px": 100 }`. |

---

## Production Sequence (`production_sequence`)

The narrative structure of the video.

`Sequence` → `Scene` → `Shot`

### Sequence
| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `sequence_id` | String | **Yes** | ID (e.g., `SEQ01`). |
| `scenes` | Array | **Yes** | List of Scene objects. |
| `style_guide` | Object | No | Palette and contrast settings for this sequence. |

### Scene
| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `scene_id` | String | **Yes** | ID (e.g., `S01`). |
| `shots` | Array | **Yes** | List of Shot objects. |
| `stage_setup` | Object | No | Initial placement of actors, props, and lights. |
| `captions` | Object | No | Inline or external caption configuration. |

### Shot
The most detailed unit of the blueprint.

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `shot_id` | String | **Yes** | ID (e.g., `SEQ01.S01.SH01`). |
| `duration_seconds` | Number | **Yes** | Exact duration of the shot. |
| `camera` | Object | No | Camera definition (lens, path, DoF). |
| `dialogue` | Array | No | Spoken lines by characters. |
| `action_notes` | String | No | Director's notes. |
| `edit` | Object | No | Transitions (`cut`, `fade`, `wipe`). |
| `audio_timeline` | Array | No | Triggers for SFX/Music. |
| `vfx_timeline` | Array | No | Triggers for visual effects. |
| `graphics_timeline` | Array | No | Overlays for images/text. |
| `on_screen_text` | Array | No | Simple text overlays. |

---

## Detailed Components

### Camera Object
| Property | Description |
|----------|-------------|
| `lens_mm` | Focal length (e.g., `50`). |
| `path` | Array of keyframes: `{ "time": 0, "position": [x,y,z], "target": [x,y,z] }`. |
| `depth_of_field`| `{ "enabled": true, "focal_distance": 5.0, "aperture": 2.8 }`. |

### Stage Setup (Scene Level)
Defines the "stage" before shots begin.

| Property | Description |
|----------|-------------|
| `environment_ref` | ID of the environment asset (HDRI/Plate). |
| `actors` | Array of `{ "asset_ref": "CHAR_ID", "initial_state": { "position": [...] } }`. |
| `props` | Array of `{ "asset_ref": "PROP_ID", "initial_state": { "position": [...] } }`. |
| `lights` | Array of light definitions (directional, spot, point). |

### Timelines (Audio / VFX / Graphics)
Event-based arrays attached to Shots.

**Audio Event:**
```json
{
  "timestamp": 0.5,
  "event_type": "music_start",
  "asset_ref": "AUD_BGM",
  "params": { "volume_db": -6.0, "fade_in": 1.0 }
}
```

**VFX Event:**
```json
{
  "timestamp": 2.0,
  "event_type": "particle_shimmer",
  "asset_ref": "VFX_SPARKS",
  "params": { "intensity": 5.0, "duration_frames": 30 }
}
```

**Graphics Event:**
```json
{
  "timestamp": 0.0,
  "event_type": "graphic_show",
  "asset_ref": "SLIDE_1",
  "params": { "position_norm": [0.5, 0.5], "scale": 1.0 }
}
```