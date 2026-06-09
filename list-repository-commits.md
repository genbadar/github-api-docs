# List Repository Commits

## Overview

This endpoint returns a list of commits for a specified repository, ordered from most recent to oldest. Each commit object includes the commit message, author details, parent SHA references, and cryptographic verification status.

Use this endpoint to build changelog tools, audit who changed what and when, or trace the history of a branch.

**Endpoint:** `GET /repos/{owner}/{repo}/commits`  
**Authentication:** Required for private repositories. Public repositories can be fetched without a token.  
**GitHub Docs:** https://docs.github.com/en/rest/commits/commits#list-commits

---

## Prerequisites

- A GitHub account
- `curl` installed on your system
- A personal access token or the GitHub CLI (`gh`) authenticated

---

## Path Parameters

| Parameter | Type   | Required | Description                                         |
|-----------|--------|----------|-----------------------------------------------------|
| `owner`   | string | Yes      | The GitHub username or organization owning the repo |
| `repo`    | string | Yes      | The repository name                                 |

---

## Query Parameters

All parameters are optional.

| Parameter  | Type    | Default         | Description                                                          |
|------------|---------|-----------------|----------------------------------------------------------------------|
| `sha`      | string  | default branch  | SHA or branch name to start listing commits from                    |
| `path`     | string  | —               | Only return commits that changed this file path                     |
| `author`   | string  | —               | Filter by GitHub username, name, or email of the commit author      |
| `committer`| string  | —               | Filter by GitHub username, name, or email of the committer          |
| `since`    | string  | —               | Only commits after this ISO 8601 timestamp (e.g. `2024-01-01T00:00:00Z`) |
| `until`    | string  | —               | Only commits before this ISO 8601 timestamp                         |
| `per_page` | integer | `30`            | Number of results per page. Max: `100`                              |
| `page`     | integer | `1`             | Page number for paginated results                                    |

---

## Request

### HTTP Method and URL

```
GET https://api.github.com/repos/{owner}/{repo}/commits
```

### Required Headers

| Header          | Value                         | Description                      |
|-----------------|-------------------------------|----------------------------------|
| `Authorization` | `Bearer YOUR_TOKEN`           | Authenticates the request        |
| `Accept`        | `application/vnd.github+json` | Specifies the GitHub API version |

---

## Example Requests

### List the 3 most recent commits

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/commits?per_page=3"
```

### List commits on a specific branch

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/commits?sha=main"
```

### List commits by a specific author

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/commits?author=genbadar"
```

### List commits that changed a specific file

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/commits?path=README.md"
```

### List commits within a date range

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/commits?since=2024-01-01T00:00:00Z&until=2024-12-31T23:59:59Z"
```

---

## Example Response

A successful request returns a `200 OK` status and a JSON array. Each object represents one commit. Below is a real response with 3 commits, trimmed to the most relevant fields:

```json
[
  {
    "sha": "ff6b25350d4b59f36c305e7e257ec7d58c2ccf0f",
    "commit": {
      "author": {
        "name": "Badar Tagar",
        "email": "genbadar@gmail.com",
        "date": "2024-12-06T05:30:43Z"
      },
      "committer": {
        "name": "Badar Tagar",
        "email": "genbadar@gmail.com",
        "date": "2024-12-06T05:30:43Z"
      },
      "message": "updated readme.md",
      "tree": {
        "sha": "c7ab68965df76dc0bf5e917a1d55395b58459e82",
        "url": "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/git/trees/c7ab68965df76dc0bf5e917a1d55395b58459e82"
      },
      "comment_count": 0,
      "verification": {
        "verified": false,
        "reason": "unsigned",
        "signature": null,
        "payload": null,
        "verified_at": null
      }
    },
    "author": {
      "login": "genbadar",
      "id": 10846346,
      "type": "User"
    },
    "committer": {
      "login": "genbadar",
      "id": 10846346,
      "type": "User"
    },
    "parents": [
      {
        "sha": "25f4994bc2225fc420238555ba8ac3e93d323722",
        "url": "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/commits/25f4994bc2225fc420238555ba8ac3e93d323722"
      }
    ]
  },
  {
    "sha": "25f4994bc2225fc420238555ba8ac3e93d323722",
    "commit": {
      "author": {
        "name": "Steven Moser",
        "email": "mellotronplayer@gmail.com",
        "date": "2024-04-05T20:10:40Z"
      },
      "committer": {
        "name": "GitHub",
        "email": "noreply@github.com",
        "date": "2024-04-05T20:10:40Z"
      },
      "message": "Update README.md",
      "verification": {
        "verified": true,
        "reason": "valid",
        "signature": "-----BEGIN PGP SIGNATURE-----...",
        "verified_at": "2025-02-14T00:57:48Z"
      }
    },
    "author": {
      "login": "smoser-LiL",
      "id": 28540243,
      "type": "User"
    },
    "committer": {
      "login": "web-flow",
      "id": 19864447,
      "type": "User"
    },
    "parents": [
      {
        "sha": "3b845136a5400628ee6041ac5d69d2e71f524b32"
      }
    ]
  }
]
```

---

## Response Fields

### Commit Object (top level)

| Field          | Type   | Description                                                              |
|----------------|--------|--------------------------------------------------------------------------|
| `sha`          | string | The full SHA hash that uniquely identifies this commit                   |
| `commit`       | object | The raw Git commit data (message, author, tree, verification)            |
| `author`       | object | The GitHub user account associated with the commit author                |
| `committer`    | object | The GitHub user account that applied the commit                          |
| `parents`      | array  | SHA references to the parent commit(s). Most commits have one parent. Merge commits have two. |
| `html_url`     | string | URL to view this commit on GitHub                                        |
| `comments_url` | string | API URL to retrieve comments left on this commit                         |

### commit.author and commit.committer

| Field  | Type   | Description                                      |
|--------|--------|--------------------------------------------------|
| `name` | string | Display name from the Git config at commit time  |
| `email`| string | Email address from the Git config at commit time |
| `date` | string | ISO 8601 timestamp of when the action occurred   |

### commit.verification

| Field         | Type    | Description                                                              |
|---------------|---------|--------------------------------------------------------------------------|
| `verified`    | boolean | `true` if the commit was signed with a valid GPG or SSH key              |
| `reason`      | string  | Explanation of the verification result (see table below)                |
| `signature`   | string  | The raw PGP or SSH signature, or `null` if unsigned                     |
| `payload`     | string  | The signed payload data, or `null` if unsigned                          |
| `verified_at` | string  | ISO 8601 timestamp of when GitHub last verified the signature, or `null`|

#### Verification Reason Values

| Value                    | Meaning                                                     |
|--------------------------|-------------------------------------------------------------|
| `valid`                  | Signature is valid and the key is trusted                   |
| `unsigned`               | No signature was attached to the commit                     |
| `unverified_email`       | Signing key email does not match any verified GitHub email  |
| `bad_email`              | Email in the signature is invalid                           |
| `unknown_key`            | Key used to sign is not registered on GitHub                |
| `malformed_signature`    | Signature could not be parsed                               |
| `expired_key`            | Signing key has expired                                     |

---

## Author vs. Committer

Every commit has two identities: an **author** and a **committer**. They are often the same person, but they can differ.

- The **author** is the person who originally wrote the changes.
- The **committer** is the person (or system) that applied the commit to the repository.

The response contains two representations of each:

| Field path       | Format       | Contains                                        |
|------------------|--------------|-------------------------------------------------|
| `commit.author`  | Git identity | Name, email, and timestamp from the Git config |
| `author`         | GitHub user  | GitHub login, user ID, and profile URLs         |
| `commit.committer` | Git identity | Name, email, and timestamp from the Git config |
| `committer`      | GitHub user  | GitHub login, user ID, and profile URLs         |

A common case where they differ is commits made through the GitHub web editor or merged pull requests. In the example response above, commit `25f499...` was authored by `smoser-LiL` but committed by `web-flow` — GitHub's own system account that applies commits made through the browser UI.

---

## Common Errors

### `404 Not Found`

```json
{
  "message": "Not Found",
  "documentation_url": "https://docs.github.com/rest"
}
```

**Cause:** The repository does not exist, the `sha` parameter points to a branch or commit that does not exist, or the repository is private and unauthenticated.  
**Fix:** Verify path parameters and the `sha` value. Authenticate with a token that has the `repo` scope for private repositories.

---

### `409 Conflict`

```json
{
  "message": "Git Repository is empty."
}
```

**Cause:** The repository exists but has no commits yet.  
**Fix:** Push an initial commit before calling this endpoint.

---

### Empty Array `[]`

**Cause:** The `since` / `until` date range or `author` filter matched no commits.  
**Fix:** Widen the date range or remove the filter and retry.

---

## Next Steps

Now that you can list commits, here are related endpoints to explore next:

- **Get a single commit** — `GET /repos/{owner}/{repo}/commits/{ref}`
- **Compare two commits or branches** — `GET /repos/{owner}/{repo}/compare/{base}...{head}`
- **List pull requests associated with a commit** — `GET /repos/{owner}/{repo}/commits/{commit_sha}/pulls`

**Further reading:**
- [GitHub REST API — Commits](https://docs.github.com/en/rest/commits/commits)
- [About commit signature verification](https://docs.github.com/en/authentication/managing-commit-signature-verification/about-commit-signature-verification)
- [Understanding the GitHub web-flow committer](https://docs.github.com/en/authentication/managing-commit-signature-verification/about-commit-signature-verification#signature-verification-for-rebase-and-merge)
