# Table Plan: WordPress Media

## Overview

| Property | Value |
|---|---|
| **Display Name** | WordPress Media |
| **Plural Name** | WordPress Media Items |
| **Schema Name** | `aidevme_wordpressmedia` |
| **Description** | Mirrors the WordPress attachment (media) post type, synced via the WordPress REST API `/wp/v2/media`. |
| **Source** | [WordPress REST API – Media](https://developer.wordpress.org/rest-api/reference/media/) |
| **Sync Endpoint** | `https://aidevme.com/wp/v2/media` |

---

## Columns

Columns are grouped by origin. All columns use the publisher prefix `aidevme_`.

### WordPress Core Identity

| Display Name | Schema Name | Type | Max Length | Notes |
|---|---|---|---|---|
| WordPress ID | `aidevme_wpid` | Whole Number | — | WP `id`. Used as the sync key. Unique. Read-only from WP. |
| GUID | `aidevme_guid` | URL | 500 | `guid.rendered`. The globally unique identifier URL. |
| Link | `aidevme_link` | URL | 500 | `link`. Frontend permalink for the attachment. |
| Slug | `aidevme_slug` | Text | 200 | `slug`. URL-safe identifier, unique per type. |
| Type | `aidevme_type` | Text | 50 | `type`. Always `"attachment"` for media items. |

---

### Publication & Dates

| Display Name | Schema Name | Type | Notes |
|---|---|---|---|
| Published Date (Site TZ) | `aidevme_date` | Date and Time | `date`. ISO 8601, site-local timezone. |
| Published Date (GMT) | `aidevme_dategmt` | Date and Time | `date_gmt`. ISO 8601, UTC. |
| Modified Date (Site TZ) | `aidevme_modified` | Date and Time | `modified`. Read-only from WP. |
| Modified Date (GMT) | `aidevme_modifiedgmt` | Date and Time | `modified_gmt`. Read-only from WP. |

---

### Status & Authorship

| Display Name | Schema Name | Type | Options | Notes |
|---|---|---|---|---|
| Status | `aidevme_status` | Choice | `inherit`, `publish`, `future`, `draft`, `pending`, `private` | `status`. Default for uploads is `inherit`. |
| WordPress Author ID | `aidevme_authorid` | Whole Number | — | `author`. WP user ID of the uploader. |
| Associated Post ID | `aidevme_postid` | Whole Number | — | `post`. WP post ID this media is attached to. 0 = unattached. |

---

### Metadata & Display

| Display Name | Schema Name | Type | Max Length | Notes |
|---|---|---|---|---|
| Title | `aidevme_title` | Text | 500 | `title.rendered`. Decoded HTML title. |
| Alt Text | `aidevme_alttext` | Text | 500 | `alt_text`. Accessibility alt text. |
| Caption | `aidevme_caption` | Multiline Text | — | `caption.rendered`. HTML-rendered caption. |
| Description | `aidevme_description` | Multiline Text | — | `description.rendered`. HTML-rendered description. |
| Comment Status | `aidevme_commentstatus` | Choice | `open`, `closed` | `comment_status`. |
| Ping Status | `aidevme_pingstatus` | Choice | `open`, `closed` | `ping_status`. |
| Template | `aidevme_template` | Text | 200 | `template`. Theme template override (usually empty). |

---

### File Information

| Display Name | Schema Name | Type | Max Length | Notes |
|---|---|---|---|---|
| Media Type | `aidevme_mediatype` | Choice | `image`, `file` | `media_type`. Broad category. |
| MIME Type | `aidevme_mimetype` | Text | 100 | `mime_type`. e.g. `image/jpeg`, `video/mp4`. |
| Source URL | `aidevme_sourceurl` | URL | 2000 | `source_url`. Direct URL to the original uploaded file. |
| File Path | `aidevme_filepath` | Text | 500 | `media_details.file`. Relative path on the server (e.g. `2024/05/photo.jpg`). |
| File Size (bytes) | `aidevme_filesize` | Whole Number | — | `media_details.filesize`. Original file size in bytes. |
| Width (px) | `aidevme_width` | Whole Number | — | `media_details.width`. Pixels. Images only. |
| Height (px) | `aidevme_height` | Whole Number | — | `media_details.height`. Pixels. Images only. |

---

### Image Metadata (EXIF / IPTC)

Stored as individual columns for querying; also preserved as JSON for full fidelity.

| Display Name | Schema Name | Type | Max Length | Notes |
|---|---|---|---|---|
| Camera | `aidevme_camera` | Text | 200 | `media_details.image_meta.camera`. |
| Aperture | `aidevme_aperture` | Text | 50 | `media_details.image_meta.aperture`. Stored as string (e.g. `"2.8"`). |
| Focal Length | `aidevme_focallength` | Text | 50 | `media_details.image_meta.focal_length`. |
| ISO | `aidevme_iso` | Whole Number | — | `media_details.image_meta.iso`. |
| Shutter Speed | `aidevme_shutterspeed` | Text | 50 | `media_details.image_meta.shutter_speed`. Stored as string. |
| Image Orientation | `aidevme_orientation` | Whole Number | — | `media_details.image_meta.orientation`. EXIF orientation value (1–8). |
| Photo Credit | `aidevme_credit` | Text | 200 | `media_details.image_meta.credit`. |
| Copyright | `aidevme_copyright` | Text | 500 | `media_details.image_meta.copyright`. |
| Date Captured | `aidevme_datecaptured` | Date and Time | — | `media_details.image_meta.created_timestamp`. Unix timestamp converted to DateTime. |
| Image Keywords | `aidevme_imagekeywords` | Text | 1000 | `media_details.image_meta.keywords`. Comma-joined array. |

---

### JSON Blobs (full fidelity)

| Display Name | Schema Name | Type | Notes |
|---|---|---|---|
| Media Details (JSON) | `aidevme_mediadetailsjson` | Multiline Text | Full `media_details` object as JSON. Preserves all image sizes, sub-sizes, and any plugin-added fields. |
| Image Meta (JSON) | `aidevme_imagemetajson` | Multiline Text | Full `media_details.image_meta` object as JSON. |
| Meta Fields (JSON) | `aidevme_metajson` | Multiline Text | `meta`. WordPress post meta fields as JSON. |

---

### Sync Tracking

| Display Name | Schema Name | Type | Notes |
|---|---|---|---|
| Last Synced | `aidevme_lastsynced` | Date and Time | Timestamp of the last successful sync from WP REST API. Set by the sync process, not WP. |
| Sync Status | `aidevme_syncstatus` | Choice | `Pending`, `Synced`, `Error` | Tracks the result of the most recent sync attempt. |
| Sync Error | `aidevme_syncerror` | Multiline Text | Error message if `Sync Status` is `Error`. |

---

## Primary Key & Alternate Keys

| Key | Columns | Purpose |
|---|---|---|
| Primary Key | `aidevme_wordpressmediaid` (auto, GUID) | Dataverse record identity |
| Alternate Key | `aidevme_wpid` | Enables upsert by WordPress post ID during sync |

---

## Image Sizes (Sub-table or JSON)

WordPress generates multiple derivative image sizes (e.g. `thumbnail`, `medium`, `medium_large`, `large`, `full`). These are stored inside `media_details.sizes` in the REST API response.

**Recommendation:** Store the full `media_details` JSON in `aidevme_mediadetailsjson` for round-trip fidelity. If individual size URLs need to be queried or displayed in Power Apps / Power Pages, extract the most important sizes as computed columns or a related child table.

Commonly needed sizes to optionally promote to flat columns:

| Display Name | Schema Name | Type | Notes |
|---|---|---|---|
| Thumbnail URL | `aidevme_thumbnailurl` | URL | `media_details.sizes.thumbnail.source_url` |
| Medium URL | `aidevme_mediumurl` | URL | `media_details.sizes.medium.source_url` |
| Large URL | `aidevme_largeurl` | URL | `media_details.sizes.large.source_url` |
| Full URL | `aidevme_fullurl` | URL | `media_details.sizes.full.source_url` — same as `source_url` |

---

## Sync Strategy

```
GET https://aidevme.com/wp/v2/media
  ?per_page=100
  &page={n}
  &modified_after={last_sync_timestamp}
  &orderby=modified
  &order=asc
```

1. **Initial load** — page through all media items (100 per page).
2. **Incremental sync** — pass `modified_after` with the timestamp of the last successful sync to fetch only changed records.
3. **Upsert** — match on alternate key `aidevme_wpid` to create-or-update Dataverse records.
4. **Deletion detection** — WordPress soft-deletes (trash) are reflected in `status`. Hard-deletes require a separate reconciliation pass comparing WP IDs against Dataverse.
5. **Rate limiting** — WordPress REST API does not enforce strict rate limits by default, but keep per-page at 100 and add delays between pages if needed.

---

## Relationships

| Relationship | Target Table | Type | Notes |
|---|---|---|---|
| Attached To Post | `aidevme_wordpresspost` (future) | Many-to-One | `aidevme_postid` → WP post record. Optional; requires posts table. |

---

## Notes

- `guid.rendered` and `source_url` often resolve to the same URL for images; `guid` is the canonical WP identifier while `source_url` is preferred for display.
- `caption` and `description` are returned as objects with a `rendered` key; store only the rendered HTML.
- `media_details` structure varies by media type — images have `width`, `height`, `sizes`, and `image_meta`; other file types (PDF, video) may only have partial data.
- The `missing_image_sizes` field is only available in `edit` context; omit unless the sync uses authenticated edit-context requests.
- If the WP REST API is password-protected (the live endpoint returned an error), the sync process must supply HTTP Basic Auth or Application Password credentials.
