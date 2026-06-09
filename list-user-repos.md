# List Authenticated User's Repositories

## Overview

This endpoint returns a list of repositories belonging to the authenticated GitHub user. It supports filtering, sorting, and pagination, making it useful for building dashboards, automation scripts, or any tool that needs to read a user's repository data.

**Endpoint:** `GET /user/repos`  
**Authentication:** Required  
**GitHub Docs:** https://docs.github.com/en/rest/repos/repos#list-repositories-for-the-authenticated-user

---

## Prerequisites

Before making requests to this endpoint, you need:

- A GitHub account
- `curl` installed on your system
- A personal access token **or** the GitHub CLI (`gh`) installed and authenticated

To verify `curl` is available on Fedora:

```bash
curl --version
```

---

## Authentication

The GitHub REST API requires authentication for this endpoint. Unauthenticated requests will return a `401 Unauthorized` error.

### Option A — Using a Personal Access Token (PAT)

1. Go to **GitHub → Settings → Developer Settings → Personal access tokens → Tokens (classic)**
2. Click **Generate new token**
3. Select the `repo` scope
4. Copy the token and store it securely

Use it in your requests via the `Authorization` header:

```bash
Authorization: Bearer YOUR_TOKEN_HERE
```

### Option B — Using the GitHub CLI (Recommended for local testing)

If you have `gh` installed and authenticated, you can retrieve your token dynamically:

```bash
gh auth token
```

This avoids hardcoding tokens in your commands.

---

## Request

### HTTP Method and URL

```
GET https://api.github.com/user/repos
```

### Required Headers

| Header          | Value                          | Description                        |
|-----------------|--------------------------------|------------------------------------|
| `Authorization` | `Bearer YOUR_TOKEN`            | Authenticates the request          |
| `Accept`        | `application/vnd.github+json`  | Specifies the GitHub API version   |

### Query Parameters

All parameters are optional.

| Parameter    | Type    | Default    | Description                                                                 |
|--------------|---------|------------|-----------------------------------------------------------------------------|
| `visibility` | string  | `all`      | Filter by visibility. Values: `all`, `public`, `private`                   |
| `affiliation`| string  | `owner, collaborator, organization_member` | Comma-separated list of affiliations |
| `type`       | string  | `owner`    | Filter by type. Values: `all`, `owner`, `public`, `private`, `member`      |
| `sort`       | string  | `full_name`| Sort by: `created`, `updated`, `pushed`, `full_name`                       |
| `direction`  | string  | `asc`      | Sort direction. Values: `asc`, `desc`                                       |
| `per_page`   | integer | `30`       | Number of results per page. Max: `100`                                      |
| `page`       | integer | `1`        | Page number for paginated results                                           |

---

## Example Requests

### Basic request — list your 5 most recent repositories

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/user/repos?per_page=5"
```

### Filter by visibility — public repos only

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/user/repos?visibility=public&per_page=5"
```

### Sort by last updated

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/user/repos?sort=updated&direction=desc&per_page=5"
```

---

## Example Response

A successful request returns a `200 OK` status and a JSON array. Each object in the array represents one repository. Below is a single repository object from a real response:

```json
{
  "id": 899350090,
  "node_id": "R_kgDONZr-Sg",
  "name": "4515339-building-php-applications-ai",
  "full_name": "genbadar/4515339-building-php-applications-ai",
  "private": false,
  "owner": {
    "login": "genbadar",
    "id": 10846346,
    "type": "User",
    "site_admin": false
  },
  "html_url": "https://github.com/genbadar/4515339-building-php-applications-ai",
  "description": null,
  "fork": true,
  "created_at": "2024-12-06T04:55:49Z",
  "updated_at": "2024-12-06T05:31:10Z",
  "pushed_at": "2024-12-06T05:31:06Z",
  "size": 4638,
  "stargazers_count": 0,
  "language": "PHP",
  "visibility": "public",
  "default_branch": "main",
  "permissions": {
    "admin": true,
    "maintain": true,
    "push": true,
    "triage": true,
    "pull": true
  }
}
```

### Key Response Fields

| Field              | Type    | Description                                              |
|--------------------|---------|----------------------------------------------------------|
| `id`               | integer | Unique repository ID                                     |
| `name`             | string  | Repository name                                          |
| `full_name`        | string  | Owner and repo name combined (e.g. `owner/repo`)         |
| `private`          | boolean | `true` if the repository is private                     |
| `html_url`         | string  | URL to view the repository on GitHub                     |
| `description`      | string  | Repository description, or `null` if not set            |
| `fork`             | boolean | `true` if this is a forked repository                   |
| `language`         | string  | Primary programming language                             |
| `visibility`       | string  | `public`, `private`, or `internal`                      |
| `default_branch`   | string  | Name of the default branch (commonly `main` or `master`)|
| `permissions`      | object  | Your access level on this repository                     |

---

## Pagination

By default, the API returns up to 30 repositories per page. Use `per_page` and `page` to paginate through larger result sets.

```bash
# Page 1
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/user/repos?per_page=10&page=1"

# Page 2
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/user/repos?per_page=10&page=2"
```

> **Tip:** The response headers include a `Link` header with `next` and `last` URLs when more pages are available.

---

## Common Errors

### `401 Unauthorized`

```json
{
  "message": "Requires authentication",
  "documentation_url": "https://docs.github.com/rest"
}
```

**Cause:** Missing or invalid token.  
**Fix:** Verify your token with `gh auth status` and ensure the `Authorization` header is correctly formatted.

---

### `403 Forbidden`

**Cause:** Your token does not have the required `repo` scope.  
**Fix:** Generate a new token with the `repo` scope enabled under GitHub → Settings → Developer Settings.

---

### `422 Unprocessable Entity`

**Cause:** An invalid value was passed to a query parameter (e.g. `sort=invalid`).  
**Fix:** Check the [Query Parameters](#query-parameters) table above for accepted values.

---

## Next Steps

Now that you can list repositories, here are related endpoints to explore next:

- **Get a single repository** — `GET /repos/{owner}/{repo}`
- **Create a repository** — `POST /user/repos`
- **List repository branches** — `GET /repos/{owner}/{repo}/branches`

**Further reading:**
- [GitHub REST API Overview](https://docs.github.com/en/rest/overview)
- [Authentication guide](https://docs.github.com/en/authentication)
- [GitHub CLI documentation](https://cli.github.com/manual/)

