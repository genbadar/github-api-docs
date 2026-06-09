# Get a Repository

## Overview

This endpoint returns detailed information about a single GitHub repository. Unlike the [List Repositories](./list-user-repos.md) endpoint which returns a summary array, this endpoint returns a full repository object — including merge settings, security analysis status, and fork relationship data.

Use this endpoint when you need complete details about a specific repository by name.

**Endpoint:** `GET /repos/{owner}/{repo}`  
**Authentication:** Required for private repositories. Public repositories can be fetched without a token.  
**GitHub Docs:** https://docs.github.com/en/rest/repos/repos#get-a-repository

---

## Prerequisites

- A GitHub account
- `curl` installed on your system
- A personal access token or the GitHub CLI (`gh`) authenticated

To check your authentication status:

```bash
gh auth status
```

---

## Path Parameters

This endpoint uses path parameters instead of query parameters. Both are required.

| Parameter | Type   | Required | Description                                      |
|-----------|--------|----------|--------------------------------------------------|
| `owner`   | string | Yes      | The GitHub username or organization that owns the repository |
| `repo`    | string | Yes      | The repository name                              |

---

## Request

### HTTP Method and URL

```
GET https://api.github.com/repos/{owner}/{repo}
```

Replace `{owner}` and `{repo}` with real values. For example:

```
GET https://api.github.com/repos/genbadar/4515339-building-php-applications-ai
```

### Required Headers

| Header          | Value                         | Description                       |
|-----------------|-------------------------------|-----------------------------------|
| `Authorization` | `Bearer YOUR_TOKEN`           | Authenticates the request         |
| `Accept`        | `application/vnd.github+json` | Specifies the GitHub API version  |

---

## Example Request

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai"
```

To fetch a public repository without authentication:

```bash
curl -H "Accept: application/vnd.github+json" \
     "https://api.github.com/repos/torvalds/linux"
```

---

## Example Response

A successful request returns a `200 OK` status and a single JSON object. Below is a real response, trimmed to the most relevant fields:

```json
{
  "id": 899350090,
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
  "language": "PHP",
  "visibility": "public",
  "default_branch": "main",
  "forks_count": 0,
  "stargazers_count": 0,
  "open_issues_count": 0,
  "allow_squash_merge": true,
  "allow_merge_commit": true,
  "allow_rebase_merge": true,
  "allow_auto_merge": false,
  "delete_branch_on_merge": false,
  "permissions": {
    "admin": true,
    "maintain": true,
    "push": true,
    "triage": true,
    "pull": true
  },
  "parent": {
    "full_name": "LinkedInLearning/4515339-building-php-applications-ai",
    "html_url": "https://github.com/LinkedInLearning/4515339-building-php-applications-ai"
  },
  "security_and_analysis": {
    "secret_scanning": { "status": "enabled" },
    "secret_scanning_push_protection": { "status": "enabled" },
    "dependabot_security_updates": { "status": "disabled" }
  },
  "network_count": 3,
  "subscribers_count": 0
}
```

---

## Response Fields

### Core Fields

| Field              | Type    | Description                                               |
|--------------------|---------|-----------------------------------------------------------|
| `id`               | integer | Unique repository ID                                      |
| `name`             | string  | Repository name                                           |
| `full_name`        | string  | Owner and name combined (e.g. `owner/repo`)               |
| `private`          | boolean | `true` if the repository is private                      |
| `html_url`         | string  | URL to view the repository on GitHub                      |
| `description`      | string  | Repository description, or `null` if not set             |
| `fork`             | boolean | `true` if this is a forked repository                    |
| `language`         | string  | Primary programming language                              |
| `visibility`       | string  | `public`, `private`, or `internal`                       |
| `default_branch`   | string  | Name of the default branch                               |
| `created_at`       | string  | ISO 8601 timestamp of when the repo was created          |
| `updated_at`       | string  | ISO 8601 timestamp of the last metadata update           |
| `pushed_at`        | string  | ISO 8601 timestamp of the last push to the repo          |

### Stats Fields

| Field                | Type    | Description                              |
|----------------------|---------|------------------------------------------|
| `stargazers_count`   | integer | Number of users who starred the repo     |
| `forks_count`        | integer | Number of forks                          |
| `open_issues_count`  | integer | Number of open issues and pull requests  |
| `watchers_count`     | integer | Number of users watching the repo        |
| `size`               | integer | Repository size in kilobytes             |
| `network_count`      | integer | Total forks across the entire fork network |
| `subscribers_count`  | integer | Number of users subscribed to notifications |

### Merge Strategy Fields

These fields only appear on the single-repo endpoint, not in list responses. They describe which merge strategies are enabled on the repository.

| Field                    | Type    | Description                                               |
|--------------------------|---------|-----------------------------------------------------------|
| `allow_squash_merge`     | boolean | Squash merge is enabled                                   |
| `allow_merge_commit`     | boolean | Merge commits are enabled                                 |
| `allow_rebase_merge`     | boolean | Rebase merge is enabled                                   |
| `allow_auto_merge`       | boolean | Auto-merge is enabled for pull requests                   |
| `delete_branch_on_merge` | boolean | Source branch is deleted automatically after a merge     |

### Fork Relationship Fields

These fields are only present when `fork` is `true`. They describe the relationship between your fork and the original repository.

| Field    | Type   | Description                                                                 |
|----------|--------|-----------------------------------------------------------------------------|
| `parent` | object | The repository this was directly forked from                               |
| `source` | object | The root repository in the fork chain (may differ from `parent` in deep fork chains) |

### Security Fields

| Field                              | Description                                                   |
|------------------------------------|---------------------------------------------------------------|
| `secret_scanning.status`           | Whether GitHub scans commits for exposed secrets              |
| `secret_scanning_push_protection`  | Whether pushes containing secrets are blocked                 |
| `dependabot_security_updates`      | Whether Dependabot automatically opens PRs for vulnerabilities |

---

## Difference from List Repositories

The `GET /user/repos` endpoint returns a summary of each repository. The `GET /repos/{owner}/{repo}` endpoint returns the full object. The table below highlights what's only available on this endpoint:

| Field                    | List `/user/repos` | Single `/repos/{owner}/{repo}` |
|--------------------------|:------------------:|:------------------------------:|
| `allow_squash_merge`     | ✗                  | ✓                              |
| `allow_merge_commit`     | ✗                  | ✓                              |
| `delete_branch_on_merge` | ✗                  | ✓                              |
| `parent`                 | ✗                  | ✓ (forks only)                 |
| `source`                 | ✗                  | ✓ (forks only)                 |
| `security_and_analysis`  | ✗                  | ✓                              |
| `network_count`          | ✗                  | ✓                              |
| `subscribers_count`      | ✗                  | ✓                              |

---

## Common Errors

### `404 Not Found`

```json
{
  "message": "Not Found",
  "documentation_url": "https://docs.github.com/rest"
}
```

**Cause:** The repository does not exist, is private, or the `owner`/`repo` values are misspelled.  
**Fix:** Double-check the spelling of both path parameters. For private repositories, ensure your token has the `repo` scope.

---

### `301 Moved Permanently`

**Cause:** The repository has been renamed or transferred to a different owner.  
**Fix:** Update your URL to use the new `owner/repo` path. GitHub will redirect most clients automatically, but it is better to use the current name.

---

### `401 Unauthorized`

**Cause:** Missing or expired token when accessing a private repository.  
**Fix:** Re-authenticate with `gh auth login` and retry.

---

## Next Steps

Now that you can read a single repository, here are related endpoints to explore next:

- **Update a repository** — `PATCH /repos/{owner}/{repo}`
- **Delete a repository** — `DELETE /repos/{owner}/{repo}`
- **List repository branches** — `GET /repos/{owner}/{repo}/branches`
- **List repository contributors** — `GET /repos/{owner}/{repo}/contributors`

**Further reading:**
- [GitHub REST API — Repositories](https://docs.github.com/en/rest/repos/repos)
- [About forks](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/about-forks)
- [GitHub merge strategies](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges)
