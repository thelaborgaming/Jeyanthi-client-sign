# Jeya Client Sign

A temporary e-signing portal for Jeyanthi's clients. Each client gets a private link, checks their details, previews their insurer forms, signs once, and the signed forms are filed in Google Drive.

This repository holds the front end only. No client data, PDFs or secrets are stored here.

## Files

| File | Purpose |
| --- | --- |
| `sign.html` | Client signing page, a single self-contained file. Opens with a personal link (`sign.html?t=<token>`). |
| `dashboard.html` | Adviser dashboard. Encrypted; open it and enter the dashboard password. |

## Client flow

1. Check and update personal details (name, NRIC/passport, mobile, email, address, occupation).
2. Answer the source-of-funds question for the PIAS transfer form.
3. Upload ID (front and back) when required.
4. Preview every form. Expand or collapse all with one button. Confirm the three PIAS statements after previewing.
5. Draw a signature and tick the consent box.
6. Manulife clients get a short guide and upload a confirmation screenshot.

## What happens on submit

- Each form is filled and signed, then saved to its insurer's folder in Drive, with a backup copy in a backup folder.
- The signature image is saved separately.
- A folder is created for the client under "Client folders", named with the client's name. It holds shortcuts to the signed forms, the signature and the Manulife screenshot. Backup forms are shortcuts inside an "Others" subfolder. Shortcuts follow the originals, so edits on the insurer side show up there; deleting an original breaks its shortcut. Folders are created at first signing only.
- Everything is logged to an audit table.

## How it works

- **Front end:** static pages on GitHub Pages. The sign page has a strict Content Security Policy based on script and style hashes. After editing its script or styles, recompute both hashes in the CSP meta tag.
- **Back end:** n8n webhooks on a self-hosted instance (sign load, preview, submit, log, Manulife proof, admin list/link, sheet sync, daily sweeper). Records live in n8n data tables.
- **PDF processing:** a small Flask + PyMuPDF service next to n8n fills forms and renders previews.
- **Links:** each link carries a random token. Only its hash is stored. Links can be revoked and expire automatically.
- **Source of truth:** the Google Sheet. A sync (every 15 minutes, or on demand from the dashboard) updates clients and policies.

## Notes

- This is a one-off project. There are no recurring features.
- Keep real client data and PDFs out of this repository.
- Do not commit the unencrypted dashboard or any admin key.
