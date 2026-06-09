# List Repository Branches

## Overview

This endpoint returns a list of branches for a specified repository. Each branch object includes the branch name, the SHA of its latest commit, and branch protection status.

Use this endpoint to inspect which branches exist in a repository, check whether protection rules are enabled, or build tools that automate branch management workflows.

**Endpoint:** `GET /repos/{owner}/{repo}/branches`  
**Authentication:** Required for private repositories. Public repositories can be fetched without a token.  
**GitHub Docs:** https://docs.github.com/en/rest/branches/branches#list-branches

---

## Prerequisites

- A GitHub account
- `curl` installed on your system
- A personal access token or the GitHub CLI (`gh`) authenticated

---

## Path Parameters

| Parameter | Type   | Required | Description                                        |
|-----------|--------|----------|----------------------------------------------------|
| `owner`   | string | Yes      | The GitHub username or organization owning the repo |
| `repo`    | string | Yes      | The repository name                                |

---

## Query Parameters

| Parameter   | Type    | Default | Description                                                        |
|-------------|---------|---------|--------------------------------------------------------------------|
| `protected` | boolean | —       | If `true`, return only protected branches. If `false`, return only unprotected branches. Omit to return all branches. |
| `per_page`  | integer | `30`    | Number of results per page. Max: `100`                            |
| `page`      | integer | `1`     | Page number for paginated results                                  |

---

## Request

### HTTP Method and URL

```
GET https://api.github.com/repos/{owner}/{repo}/branches
```

### Required Headers

| Header          | Value                         | Description                      |
|-----------------|-------------------------------|----------------------------------|
| `Authorization` | `Bearer YOUR_TOKEN`           | Authenticates the request        |
| `Accept`        | `application/vnd.github+json` | Specifies the GitHub API version |

---

## Example Requests

### List all branches

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/branches"
```

### List only protected branches

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/branches?protected=true"
```

### List only unprotected branches

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/branches?protected=false"
```

---

## Example Response

A successful request returns a `200 OK` status and a JSON array. Each object represents one branch. Below is a real response from a single-branch repository:

```json
[
  {
    "name": "main",
    "commit": {
      "sha": "ff6b25350d4b59f36c305e7e257ec7d58c2ccf0f",
      "url": "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/commits/ff6b25350d4b59f36c305e7e257ec7d58c2ccf0f"
    },
    "protected": false,
    "protection": {
      "enabled": false,
      "required_status_checks": {
        "enforcement_level": "off",
        "contexts": [],
        "checks": []
      }
    },
    "protection_url": "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/branches/main/protection"
  }
]
```

---

## Response Fields

### Branch Object

| Field            | Type    | Description                                                         |
|------------------|---------|---------------------------------------------------------------------|
| `name`           | string  | The branch name                                                     |
| `commit.sha`     | string  | The SHA hash of the latest commit on this branch                   |
| `commit.url`     | string  | API URL to retrieve full details of the latest commit              |
| `protected`      | boolean | `true` if branch protection rules are enabled on this branch       |
| `protection`     | object  | Details of the protection rules (see below)                        |
| `protection_url` | string  | API URL to manage protection rules for this branch                 |

### Protection Object

The `protection` object describes the current branch protection configuration.

| Field                                       | Type    | Description                                                         |
|---------------------------------------------|---------|---------------------------------------------------------------------|
| `enabled`                                   | boolean | Whether branch protection is active                                |
| `required_status_checks.enforcement_level`  | string  | How status checks are enforced. Values: `off`, `non_admins`, `everyone` |
| `required_status_checks.contexts`           | array   | List of status check names that must pass before merging (legacy format) |
| `required_status_checks.checks`             | array   | List of status check objects that must pass before merging          |

> **Note:** `contexts` and `checks` serve the same purpose but use different formats. `checks` is the newer format and supports specifying a particular `app_id` alongside the check name. Both may appear together for compatibility.

---

## Understanding Branch Protection

Branch protection rules prevent direct pushes, require pull request reviews, or enforce passing status checks before merging. When `protected` is `false` (as in the example above), anyone with write access can push directly to that branch.

To view or manage protection rules for a specific branch, use the `protection_url` returned in the response:

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/branches/main/protection"
```

---

## Common Errors

### `404 Not Found`

```json
{
  "message": "Not Found",
  "documentation_url": "https://docs.github.com/rest"
}
```

**Cause:** The repository does not exist, is private and unauthenticated, or the path parameters are misspelled.  
**Fix:** Verify the `owner` and `repo` values and ensure your token has the `repo` scope for private repositories.

---

### Empty Array `[]`

**Cause:** The repository exists but has no branches. This can happen with a newly created, empty repository.  
**Fix:** This is not an error — it is the expected response for a repository with no commits yet. Push an initial commit to create the default branch.

---

### `401 Unauthorized`

**Cause:** Missing or expired token when accessing a private repository.  
**Fix:** Re-authenticate with `gh auth login` and retry.

---

## Next Steps

Now that you can list branches, here are related endpoints to explore next:

- **Get a specific branch** — `GET /repos/{owner}/{repo}/branches/{branch}`
- **Get branch protection rules** — `GET /repos/{owner}/{repo}/branches/{branch}/protection`
- **List repository commits** — `GET /repos/{owner}/{repo}/commits`
- **Compare two branches** — `GET /repos/{owner}/{repo}/compare/{base}...{head}`

**Further reading:**
- [GitHub REST API — Branches](https://docs.github.com/en/rest/branches/branches)
- [About protected branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches)
- [About commit SHAs](https://docs.github.com/en/pull-requests/committing-changes-to-your-project/creating-and-editing-commits/about-commits)
