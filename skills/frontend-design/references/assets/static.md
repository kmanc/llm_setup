#### Asset organization

- Keep the folloing in `static/`:
 - favicon.svg — primary, referenced first; scales cleanly to any size browsers request
 - favicon.ico — legacy fallback, multi-resolution as above
 - apple-touch-icon.png — 180×180 PNG, used when the site is added to an iOS home screen
- If the app is installable (has a web manifest), also provide 192×192 and 512×512 PNGs referenced from manifest.json's icons array, including at least one with "purpose": "maskable" sized/padded so Android's icon masking doesn't clip important content
