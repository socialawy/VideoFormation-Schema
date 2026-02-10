# @videoformation/blueprint-schema

JSON Schema and validation utilities for VideoFormation blueprint files.

## 🚀 Installation

```bash
npm install @videoformation/blueprint-schema
```

## 📋 Usage

### JavaScript/TypeScript
```javascript
import { validateBlueprint } from '@videoformation/blueprint-schema';

const blueprint = {
  "project_metadata": {
    "project_id": "MY_PROJECT",
    "title": "My Video"
  },
  // ... rest of blueprint
};

const result = validateBlueprint(blueprint);
if (result.valid) {
  console.log('✅ Blueprint is valid');
} else {
  console.log('❌ Validation errors:', result.errors);
}
```

### CLI
```bash
# Validate a blueprint file
npx @videoformation/blueprint-schema validate my-blueprint.json

# Generate validation report
npx @videoformation/blueprint-schema validate my-blueprint.json --report validation-report.json
```

## 📖 Documentation

- **Complete Schema Reference**
- **Examples**: Minimal and complete blueprint examples
- **API Reference**: Detailed validation options and error handling

## 🔗 Links

- **npm Package**: https://www.npmjs.com/package/@videoformation/blueprint-schema
- **GitHub Repository**: https://github.com/videoformation/blueprint-schema
- **Issues**: https://github.com/videoformation/blueprint-schema/issues

## 📄 License

MIT License - see [LICENSE](./LICENSE) file for details.

---

**Maintained by**: VideoFormation Team
