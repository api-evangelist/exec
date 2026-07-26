---
name: Manage Knowledge Hub pages and sources
description: Create and publish Knowledge Hub pages through the draft to publish flow, and attach URL-based sources, polling until extraction is ready.
api: openapi/exec-openapi-original.yml
operations: [listKnowledgeHubFolders, getKnowledgeHubFolder, listKnowledgeHubPages, createKnowledgeHubPage, getKnowledgeHubPage, updateKnowledgeHubPage, listKnowledgeHubPageVersions, createKnowledgeHubSource, getKnowledgeHubSource, listKnowledgeHubSources, deleteKnowledgeHubSource]
---

# Manage Knowledge Hub content

## Auth
`Authorization: Bearer exec_live_...` (full workspace admin).

## Steps
1. **Locate a folder** — `GET /knowledge-hub/folders` (`listKnowledgeHubFolders`); fetch one with `GET /knowledge-hub/folders/{folder_id}` (`getKnowledgeHubFolder`). Folders are also called Spaces/Hubs.
2. **Create a page** — `POST /knowledge-hub/pages` (`createKnowledgeHubPage`). New pages use the draft to publish flow: set `status: published` to publish immediately, or leave `draft` to save without publishing.
3. **Read / list** — `GET /knowledge-hub/pages` (`listKnowledgeHubPages`) returns metadata only (no body); `GET /knowledge-hub/pages/{page_id}` (`getKnowledgeHubPage`) returns the published markdown body, sources, skills, and version info.
4. **Update / version** — `PATCH /knowledge-hub/pages/{page_id}` (`updateKnowledgeHubPage`) changes only the fields you send; editing `content` creates a new draft — set `status: published` to publish it. Review history with `GET /knowledge-hub/pages/{page_id}/versions` (`listKnowledgeHubPageVersions`).
5. **Add a source** — `POST /knowledge-hub/sources` (`createKnowledgeHubSource`) from a URL. Extraction runs asynchronously; the source returns `status: pending`/`processing` — poll `GET /knowledge-hub/sources/{source_id}` (`getKnowledgeHubSource`) until `status: ready`. Use `?include=content` to fetch extracted text. Remove with `DELETE /knowledge-hub/sources/{source_id}` (`deleteKnowledgeHubSource`).

## Rules
- List rows are metadata only — fetch a single page/source for the body/content.
- Page-number pagination throughout; 429 carries `retry_after`; errors use the `{ "error": {...} }` envelope.
