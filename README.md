# JS Analyzer - Burp Suite Extension

A powerful Burp Suite extension for JavaScript static analysis. Extracts API endpoints, URLs, secrets, and email addresses from JavaScript files with intelligent noise filtering.

The goal is reduce noise as much as possible to ensure the accuracy

![Burp Suite](https://img.shields.io/badge/Burp%20Suite-Extension-orange)
![Python](https://img.shields.io/badge/Python-Jython%202.7-blue)
![License](https://img.shields.io/badge/License-MIT-green)

## Features

- **🔍 Endpoint Detection** - Finds API paths, REST endpoints, OAuth URLs, admin routes
- **🌐 URL Extraction** - Extracts full URLs including cloud storage (AWS S3, Azure, GCP)
- **🔐 Secret Scanning** - Detects API keys, tokens, credentials (AWS, Stripe, GitHub, Slack, JWT, etc.)
- **📧 Email Extraction** - Finds email addresses in JS code
- **📄 File Detection** - Detects references to sensitive files (.sql, .csv, .bak, .env, .pdf, etc.)
- **🧹 Smart Filtering** - Removes noise from XML namespaces, module imports, build artifacts
- **📁 Source Tracking** - Shows which JS file each finding came from
- **🔎 Live Search** - Filter results in real-time
- **📋 Copy Function** - Copy individual or all findings to clipboard
- **💾 JSON Export** - Export all findings to JSON file

## Installation

1. Download [Jython standalone JAR](https://www.jython.org/download)
2. In Burp Suite: `Extender > Options > Python Environment`
3. Set the Jython JAR path
4. `Extender > Extensions > Add`
5. Select `Python` and browse to `js_analyzer.py`

## Usage

1. **Browse** websites with your browser proxied through Burp Suite
2. **Right-click** on any response in:
   - Proxy > HTTP history
   - Target > Site map
   - Repeater
3. Select **"Analyze JS with JS Analyzer"**
4. Check the **JS Analyzer** tab for results

## Screenshots

### Main Interface
```
┌──────────────────────────────────────────────────────────────────────┐
│ JS Analyzer | E:15 | U:8 | S:2 | M:3  Search:[____] Source:[All ▼]  │
├──────────────────────────────────────────────────────────────────────┤
│ [Endpoints (15)] [URLs (8)] [Secrets (2)] [Emails (3)] [Files (5)]   │
├──────────────────────────────────────────────────────────────────────┤
│ Value                                          │ Source              │
│ /api/v1/users/profile                          │ app.js              │
│ /api/v2/auth/token                             │ app.js              │
│ /oauth2/authorize                              │ login.js            │
│ /admin/dashboard                               │ main.js             │
│ /.well-known/openid-configuration              │ auth.js             │
└──────────────────────────────────────────────────────────────────────┘
```

## What It Detects

### Endpoints
| Pattern | Example |
|---------|---------|
| API paths | `/api/v1/users`, `/api/v2/auth` |
| REST endpoints | `/rest/data`, `/graphql` |
| OAuth/Auth | `/oauth2/token`, `/auth/login`, `/callback` |
| Admin routes | `/admin`, `/dashboard`, `/internal` |
| Well-known | `/.well-known/openid-configuration` |

### Secrets
| Type | Pattern |
|------|---------|
| AWS Access Key | `AKIA[0-9A-Z]{16}` |
| Google API Key | `AIza[0-9A-Za-z\-_]{35}` |
| Stripe Live Key | `sk_live_[0-9a-zA-Z]{24,}` |
| GitHub PAT | `ghp_[0-9a-zA-Z]{36}` |
| Slack Token | `xox[baprs]-...` |
| JWT | `eyJ...` |
| Private Keys | `-----BEGIN PRIVATE KEY-----` |
| Database URLs | `mongodb://`, `postgres://`, `mysql://` |

### Noise Filtering
The extension automatically filters out:
- XML namespaces (`schemas.openxmlformats.org`, `www.w3.org`)
- Module imports (`./`, `../`, `@angular/`, etc.)
- PDF internal paths (`/Type`, `/Font`, `/Filter`)
- Excel/XML paths (`xl/`, `docProps/`, `worksheets/`)
- Locale files (`en.js`, `fr-ca.js`)
- Crypto library internals (`sha.js`, `aes`, `bn.js`)

### Files
Detects references to sensitive file types:
| Category | Extensions |
|----------|------------|
| Data | `.sql`, `.csv`, `.xlsx`, `.json`, `.xml`, `.yaml` |
| Config | `.env`, `.conf`, `.ini`, `.cfg`, `.config` |
| Backup | `.bak`, `.backup`, `.old`, `.orig` |
| Certs | `.key`, `.pem`, `.crt`, `.p12`, `.pfx` |
| Docs | `.pdf`, `.doc`, `.docx` |
| Archives | `.zip`, `.tar`, `.gz` |
| Scripts | `.sh`, `.bat`, `.ps1`, `.py` |

## Standalone Engine

For use in your own Python projects or APIs:

```python
from js_analyzer_engine import JSAnalyzerEngine

engine = JSAnalyzerEngine()
results = engine.analyze(javascript_content)

print(results["endpoints"])  # ['/api/v1/users', ...]
print(results["urls"])       # ['https://api.example.com', ...]
print(results["secrets"])    # [{'type': 'AWS Key', 'value': '...', 'masked': '...'}, ...]
print(results["emails"])     # ['admin@company.com', ...]
```

### Flask API Example

```python
from flask import Flask, request, jsonify
from js_analyzer_engine import JSAnalyzerEngine

app = Flask(__name__)
engine = JSAnalyzerEngine()

@app.route('/analyze', methods=['POST'])
def analyze():
    content = request.json.get('content', '')
    results = engine.analyze(content)
    return jsonify(results)

if __name__ == '__main__':
    app.run(port=5000)
```

## File Structure

```
JSextension/
├── js_analyzer.py          # Main Burp extension entry point
├── js_analyzer_engine.py   # Standalone analysis engine (for APIs)
├── ui/
│   ├── __init__.py
│   └── results_panel.py    # Burp UI panel
├── README.md
└── LICENSE
```

## Contributing

Contributions are welcome! Feel free to:
- Add new secret patterns
- Improve noise filtering
- Add new endpoint patterns
- Report bugs or issues

## License

MIT License - see [LICENSE](LICENSE) file.

## Credits

Inspired by: 
- [LinkFinder](https://github.com/GerbenJavado/LinkFinder) - Endpoint detection regex
- [TruffleHog](https://github.com/trufflesecurity/trufflehog) - Secret patterns

## Author

Jenish Sojitra (https://x.com/_jensec)

Created with ❤️ for the  community.
