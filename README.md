# Gratuity — Tip Calculator & Bill Splitter

A single-screen tip calculator and bill splitter. Live updates as you type. No calculate button. No dependencies.

## Run Locally

```bash
# Option 1 — open directly (works in all modern browsers)
open index.html

# Option 2 — serve locally (avoids any file:// quirks)
npx serve .
# then visit http://localhost:3000

# Option 3 — Python
python3 -m http.server 8080
# then visit http://localhost:8080
```

**Requirements:** A modern browser (Chrome 90+, Firefox 88+, Safari 14+, Edge 90+). No Node, no build step, no package install needed to just open the file.

## Features

- Bill amount with inline validation
- Tip presets (10% / 15% / 20%) + custom % input
- Live per-person split with rounding policy
- Full keyboard navigation (Tab / Enter)
- Inline error messages — no alerts
- Reset button
- Responsive: works from 360px to 1440px+
