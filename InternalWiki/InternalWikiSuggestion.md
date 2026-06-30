# Internal Wiki Platform — Discussion Summary

## Goal

Create a company-internal wiki platform with three core capabilities:

1. **Google-quality search** — full-text search with good relevance ranking across all wiki content
2. **Knowledge gap workflow** — when a search yields no results, the user can submit a request/question that becomes a ticket for someone else to pick up and document
3. **Inline reader feedback** — readers can highlight incorrect or lacking text, and that feedback is aggregated monthly into a digest email sent to the document owner

---

## Feature 1: Wiki with Good Search

The wiki should support creating and linking information, with a search field that provides Google-like relevance ranking.

### Off-the-shelf options

| Option | Pros | Cons |
|--------|------|------|
| **Confluence + Jira** | Mature wiki + built-in ticketing | Search quality is mediocre; no native inline feedback |
| **Notion + ticketing layer** | Good search, easy editing, nice linking | No built-in request flow or annotation system |
| **Wiki.js** (open source, self-hosted) | Excellent search (Elasticsearch/Postgres FTS), free | Needs custom work for the request and feedback features |
| **BookStack** (open source, self-hosted) | Full-text search, good UI | Similar custom work needed |

### Custom-built search stack

| Layer | Suggested Tech |
|-------|----------------|
| Frontend | React / Next.js or similar |
| Wiki content | Markdown or rich text stored in a database |
| Search | Elasticsearch or OpenSearch (gives Google-like relevance) |
| Auth | SSO / LDAP for company access |

OpenSearch/Elasticsearch allows tuning relevance, adding synonyms, and eventually layering in AI/semantic search.

---

## Feature 2: Knowledge Gap Workflow

When a person searches and finds no results (or poor results), the system should allow them to submit a request so that someone else can create the missing documentation.

### User experience

1. User searches for something
2. Search returns 0 or poor results
3. UI shows a prompt: *"Can't find what you're looking for? [Submit a request] so someone can document it."*
4. The search query is pre-filled as context in the submission form
5. Submission creates a ticket/request with the query and any additional context the user provides

### Implementation options

- **Integrated with existing ticketing:** Route requests to Jira, GitLab Issues, GitHub Issues, or Linear
- **Lightweight internal queue:** A simple database table or even a dedicated channel in Teams/Slack
- **Custom request board:** A built-in queue in the wiki where knowledge contributors can pick up requests

---

## Feature 3: Inline Reader Feedback with Monthly Digest

When a reader finds text that is wrong or lacking, they should be able to mark it directly in the document. This feedback is collected over time and sent as a summarized monthly email to the document owner.

### Reader experience

1. Reader selects/highlights text on a wiki page
2. A small popup appears with options: "Flag as incorrect" / "Missing information"
3. Optional free-text comment explaining what's wrong or what's missing
4. Submit — stored quietly, no disruption to the reading experience
5. The reader doesn't need to do anything else

### Backend storage

Each annotation is stored with:
- Page ID
- Highlighted/selected text
- Type (incorrect / lacking)
- Free-text comment
- Reporter (who flagged it)
- Timestamp

Each wiki page has an assigned document owner (metadata field).

### Monthly digest job

A scheduled job (cron / cloud scheduler) runs once a month and:

1. Groups all annotations by document
2. Summarizes them (can use AI to condense many similar complaints into clear bullet points)
3. Sends a templated email to the document responsible person

#### Example email output

> *"Your page 'Deployment Guide' received 7 pieces of feedback this month:*
> - *3 readers flagged the rollback section as outdated*
> - *2 readers noted missing info about the new staging environment*
> - *2 readers marked the prerequisites list as incomplete"*

### Implementation details

| Concern | Approach |
|---------|----------|
| Inline text selection | JavaScript Selection API + floating toolbar (similar to Medium's highlight UX) |
| Storage | Annotations table in database |
| Document ownership | Metadata field on each wiki page (assignable/changeable) |
| Summarization | Simple grouping by similarity, or AI-powered summarization for high volume |
| Email delivery | Scheduled job + templated email (e.g., via SendGrid, SES, or internal SMTP) |

---

## Architecture Overview (Custom-Built)

| Layer | Tech |
|-------|------|
| Frontend | React / Next.js |
| Wiki content | Markdown / rich text in DB |
| Search | Elasticsearch or OpenSearch |
| "No results" request flow | Form → ticket system or internal queue |
| Inline feedback | JS Selection API + floating toolbar |
| Feedback storage | Annotations table (page, selected text, type, comment, reporter, timestamp) |
| Monthly digest | Scheduled job + AI summarization + templated email |
| Auth | SSO / LDAP |

---

## Recommendations

### Fast path (least effort)

**Wiki.js + a simple request queue + a custom annotation plugin**

- Wiki.js handles content, linking, and search out of the box
- Add a request queue (could be a simple DB table, Jira integration, or Slack channel)
- Build a lightweight annotation plugin for the inline feedback

### Best search quality & full control

**Custom-built with OpenSearch**

- Full control over search relevance tuning, synonyms, and future AI/semantic search
- All three features built natively into the platform
- More development effort upfront, but no limitations from third-party tools

---

## Key Design Principles

- **Low friction for readers** — highlighting and flagging should be effortless (just select and click)
- **Actionable feedback for owners** — consolidated monthly digests, not a noisy stream of individual reports
- **Knowledge gaps become visible** — failed searches turn into trackable requests instead of dead ends
- **Document accountability** — every page has a responsible owner who receives feedback
