# GitHub REST API Docs

A hands-on reference for the GitHub REST API, written and tested on Fedora Linux using real API responses. Each doc covers a single endpoint with working `curl` examples, full response field references, and common errors.

---

## Getting Started

All examples in this documentation use the [GitHub CLI](https://cli.github.com/) to handle authentication. Install and authenticate it once, then reuse your token across every request.

**Install on Fedora:**

```bash
sudo dnf install gh -y
```

**Authenticate:**

```bash
gh auth login
```

**Verify:**

```bash
gh auth status
```

Once authenticated, every `curl` example in this repo works as-is — no manual token management needed.

---

## Endpoints

### Repositories

| Endpoint | Method | Description |
|----------|--------|-------------|
| [List authenticated user's repositories](./list-user-repos.md) | `GET /user/repos` | Returns all repositories belonging to the authenticated user. Supports filtering, sorting, and pagination. |
| [Get a repository](./get-a-repository.md) | `GET /repos/{owner}/{repo}` | Returns full details for a single repository, including merge settings, fork relationships, and security analysis status. |
| [List repository branches](./list-repository-branches.md) | `GET /repos/{owner}/{repo}/branches` | Returns all branches for a repository with their latest commit SHA and protection status. |
| [List repository commits](./list-repository-commits.md) | `GET /repos/{owner}/{repo}/commits` | Returns the commit history for a repository with author details, parent references, and signature verification. |
| [Get repository contents](./get-repository-contents.md) | `GET /repos/{owner}/{repo}/contents/{path}` | Returns a file's contents Base64-encoded, along with its SHA and metadata. |
| [Get user profile](./get-a-user.md.md) | `GET /users/{user-name}` | Returns a repo profile >
---

## Prerequisites

| Tool | Purpose | Install |
|------|---------|---------|
| `curl` | Make HTTP requests from the terminal | Pre-installed on most Fedora systems |
| `gh` | GitHub CLI for authentication | `sudo dnf install gh` |
| A GitHub account | Required for authenticated endpoints | [github.com](https://github.com) |

---

## Key Concepts

**Authentication** — Most endpoints require a bearer token passed in the `Authorization` header. This documentation uses `$(gh auth token)` to inject the token dynamically rather than hardcoding it.

**Path parameters** — Values like `{owner}` and `{repo}` in the URL are replaced with real values. For example, `GET /repos/{owner}/{repo}` becomes `GET /repos/genbadar/my-repo`.

**Query parameters** — Optional filters appended to the URL with `?`. For example, `?per_page=10&page=2` controls pagination.

**Pagination** — Endpoints that return lists default to 30 results per page. Use `per_page` (max 100) and `page` to navigate through larger result sets.

**SHA** — A 40-character hex string that uniquely identifies a commit, tree, or blob in Git. Used throughout the API to reference specific points in history.

---

## Environment

All examples were written and tested on:

- **OS:** Fedora Linux
- **Shell:** bash
- **Tools:** `curl`, `gh` CLI
- **API version:** `application/vnd.github+json`

---

## Resources

- [GitHub REST API official docs](https://docs.github.com/en/rest)
- [GitHub CLI manual](https://cli.github.com/manual/)
- [GitHub authentication guide](https://docs.github.com/en/authentication)
- [REST API rate limits](https://docs.github.com/en/rest/overview/rate-limits-for-the-rest-api)
