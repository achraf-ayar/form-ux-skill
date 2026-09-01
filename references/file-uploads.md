# File and image uploads

File inputs require client UX and server protection. Browser metadata is advisory.

## Input design

Use a real `<input type="file">` with an explicit label, even when visually enhanced. A drag-and-drop zone must also support click/tap selection and keyboard operation. Do not rely on drag alone.

Set `accept` to help selection:

```html
<input type="file" accept="image/png,image/jpeg,.png,.jpg,.jpeg">
```

`accept` is not validation. State allowed types, maximum per-file/total size, file count, and image dimensions when relevant in visible help text. Use `multiple` only when the workflow accepts more than one file.

## Client experience

- Validate obvious count/size/type issues early, but allow the server to make the authoritative decision.
- List selected filenames and sizes; provide accessible remove/retry actions.
- For previews, manage object URL lifetime and provide meaningful alt text or mark decorative previews appropriately.
- Show upload progress only when it is real and useful; distinguish queued, uploading, processing, failed, and complete states.
- Handle partial failure and retry without silently re-uploading successful files or clearing unrelated form fields.
- Warn before navigation if abandoning a consequential upload.

## Server validation and storage

Never trust filename extension, browser MIME type, image dimensions, or client-reported size alone. Enforce count and byte limits while streaming, inspect file signatures/content with established safe tooling, decode media defensively, and reject mismatches. Protect against decompression bombs and parser vulnerabilities as relevant.

Generate storage identifiers; do not use raw filenames as paths. Prevent traversal and overwrite, separate executable content, apply least-privilege storage access, and serve downloads with safe content disposition/type headers. Scan/quarantine when the risk model requires it. Re-encode images only with a trusted pipeline and remember metadata may contain location/private information.

Authorization must cover upload, association with a record, replacement/removal, and later download. Validate again in asynchronous processing; the upload handler and worker are both trust boundaries.

## Error messages

Say what failed and the remedy: “`portrait.tiff` is not supported. Choose a JPG or PNG up to 5 MB.” Localize units/messages and do not disclose scanner internals. Keep the rest of the form intact.

