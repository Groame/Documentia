# Internal Wiki Platform — Discussion Summary

## Goal

Create a company-internal wiki platform with three core capabilities:

1. **Google-quality search** — full-text search with good relevance ranking across all wiki content
2. **Knowledge gap workflow** — when a search yields no results, the user can submit a request/question that becomes a ticket for someone else to pick up and document
3. **Inline reader feedback** — readers can highlight incorrect or lacking text, and that feedback is aggregated monthly into a digest email sent to the document owner

---

## Suggested Approaches

### Off-the-shelf options

| Option | Pros | Cons |
|--------|------|------|
| **Confluence + Jira** | Mature wiki + built-in ticketing | Search quality is mediocre; no native inline feedback |
| **Notion + ticketing layer** | Good search, easy editing | No built-in request flow or annotation system |
| **Wiki.js** (open source) | Excellent search (Elasticsearch/Postgres FTS), self-hosted, free | Needs custom work for the request and feedback features |

### Custom-built

A fully custom solution gives the most control over all three features:

| Layer | Suggested Tech |
|-------|----------------|
| Frontend | React / Next.js |
| Wiki content | Markdown or rich text in a database |
| Search | Elasticsearch or OpenSearch |
| "No results" request flow | Form → ticket system (Jira, Linear, or internal queue) |
| Inline feedback | JS Selection API + floating toolbar |
| Feedback storage | Annotations table (page, selected text, type, comment, reporter, timestamp) |
| Monthly digest | Scheduled job + AI summarization + templated email |
| Auth | SSO / LDAP |

---

## Feature Details

### Knowledge Gap Workflow

- Search returns no/poor results → UI prompts: "Can't find what you're looking for?"
- User submits a request (search query pre-filled as context)
- Request becomes a ticket assigned to a knowledge team or relevant owner

### Inline Reader Feedback

- Reader highlights text → popup offers "Flag as incorrect" or "Missing information"
- Optional free-text comment
- Feedback is stored and linked to the document's assigned owner
- A monthly scheduled job groups and summarizes feedback per document
- Document owner receives a digest email with consolidated, actionable items

---

## Recommendation

- **Fast path:** Wiki.js + a lightweight request queue + a custom annotation plugin
- **Best search & full control:** Custom-built with OpenSearch, which allows tuning relevance, synonyms, and future AI/semantic search
