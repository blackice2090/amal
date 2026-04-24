# أ. أمل المطيري — Portfolio

A personal portfolio website for a Computer Science teacher, showcasing programs, statistics, and a Google Drive folder explorer.

## Getting Started

No installation required. Open `index.html` directly in any browser.

## Customizing Content

All editable data is at the top of the `<script>` block inside `index.html`:

| Constant | What to edit |
|---|---|
| `teacherInfo` | Name, specialization, degree, years of experience, phone, email, LinkedIn, GitHub, Twitter |
| `statsData` | The 4 statistic cards — label, number, and icon |
| `foldersData` | Google Drive folder tree — add your folder's ID to `driveId` to enable the direct Drive link |

### Linking a Google Drive folder

1. Open the folder in Google Drive
2. Copy the ID from the URL: `drive.google.com/drive/folders/`**`FOLDER_ID`**
3. Paste it into the matching entry in `foldersData`:

```js
{ id: 1, name: "ابداع", driveId: "FOLDER_ID", children: [...] }
```

Leave `driveId: null` for folders not yet linked.

## Features

- **Hero section** — typewriter name animation, orbiting avatar, social links
- **Statistics** — animated count-up numbers on scroll
- **Drive Explorer** — interactive folder tree; linked folders open in a popup window
- **Dark / Light mode** — toggle in the nav bar, preference saved in `localStorage`
- **Fully Arabic / RTL**
- **Responsive** — mobile, tablet, and desktop

## Fonts

The site uses three Thmanyah typefaces loaded from the `thmanyah typeface/` folder. Keep that folder next to `index.html`, otherwise text falls back to system fonts.

## Files

| File | Purpose |
|---|---|
| `index.html` | The complete single-file app |
| `thmanyah typeface/` | Local Arabic font files (woff2) |
| `digram1.md` | Reference React component for the folder-tree UI |
| `portfolio-code.md` | Original design requirements document |
