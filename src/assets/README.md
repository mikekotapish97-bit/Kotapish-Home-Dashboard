# assets/

This project intentionally ships **no binary assets**.

## fonts/

SF Pro is Apple-licensed and may not be redistributed. The dashboard achieves
an authentic SF Pro look on iPadOS/Safari for free via the system font stack
defined once in [`../theme/kotapish_glass.yaml`](../theme/kotapish_glass.yaml):

```
-apple-system, BlinkMacSystemFont, "SF Pro Display", "SF Pro Text", ...
```

On iPadOS this resolves directly to San Francisco. No font files are needed
or should be added to this folder.

## icons/

All iconography uses Material Design Icons (`mdi:*`) bundled with Home
Assistant core — no icon assets are required here either. If you want true
SF Symbols glyphs, use the HACS **`sf-symbols`** icon set resource instead of
adding files to this folder, then swap `icon:` values in the templates.
