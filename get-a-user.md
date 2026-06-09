# Get a User

## Overview

This endpoint returns publicly available profile information for any GitHub user. No authentication is required to fetch a public profile, making it one of the simplest endpoints in the GitHub REST API.

Use this endpoint when you want to look up a user's display name, bio, location, public repository count, or follower statistics by their GitHub username.

**Endpoint:** `GET /users/{username}`  
**Authentication:** Not required for public profiles. Authenticated requests return additional fields such as `email` if the user has made it public.  
**GitHub Docs:** https://docs.github.com/en/rest/users/users#get-a-user

---

## Prerequisites

- `curl` installed on your system
- A GitHub username to look up

Authentication is optional but recommended — unauthenticated requests are subject to a lower rate limit (60 requests per hour vs 5,000 for authenticated requests).

---

## Path Parameters

| Parameter  | Type   | Required | Description                  |
|------------|--------|----------|------------------------------|
| `username` | string | Yes      | The GitHub username to look up |

---

## Request

### HTTP Method and URL

```
GET https://api.github.com/users/{username}
```

### Headers

| Header          | Value                         | Description                      |
|-----------------|-------------------------------|----------------------------------|
| `Authorization` | `Bearer YOUR_TOKEN`           | Optional. Increases rate limit and may return additional fields. |
| `Accept`        | `application/vnd.github+json` | Specifies the GitHub API version |

---

## Example Requests

### Fetch a public profile without authentication

```bash
curl -H "Accept: application/vnd.github+json" \
     "https://api.github.com/users/genbadar"
```

### Fetch a profile with authentication

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/users/genbadar"
```

---

## Example Response

A successful request returns a `200 OK` status and a single JSON object. Below is a real response:

```json
{
  "login": "genbadar",
  "id": 10846346,
  "avatar_url": "https://avatars.githubusercontent.com/u/10846346?v=4",
  "html_url": "https://github.com/genbadar",
  "type": "User",
  "site_admin": false,
  "name": "Badar Tagar",
  "company": null,
  "blog": "",
  "location": "Hyderabad, Pakistan",
  "email": "bd2tagar@gmail.com",
  "hireable": null,
  "bio": "Senior ASP.NET Core Developer | ERP Specialist (Accounting, Sales & Inventory)",
  "twitter_username": null,
  "public_repos": 234,
  "public_gists": 0,
  "followers": 10,
  "following": 34,
  "created_at": "2015-02-04T09:48:00Z",
  "updated_at": "2026-06-05T13:26:37Z"
}
```

---

## Response Fields

The response contains two categories of fields: **profile fields** set by the user, and **navigation fields** that are API URLs pointing to related endpoints.

### Profile Fields

| Field              | Type    | Description                                                              |
|--------------------|---------|--------------------------------------------------------------------------|
| `login`            | string  | The user's GitHub username                                               |
| `id`               | integer | Unique numeric ID assigned by GitHub. Does not change even if the username changes. |
| `avatar_url`       | string  | URL of the user's profile picture                                        |
| `html_url`         | string  | URL to the user's GitHub profile page                                    |
| `type`             | string  | `User` for individual accounts, `Organization` for org accounts         |
| `site_admin`       | boolean | `true` if the user is a GitHub staff member                             |
| `name`             | string  | The user's display name, or `null` if not set                           |
| `company`          | string  | The user's company, or `null` if not set                                |
| `blog`             | string  | The user's website URL, or empty string if not set                      |
| `location`         | string  | The user's location, or `null` if not set                               |
| `email`            | string  | Publicly visible email address, or `null` if not set                    |
| `hireable`         | boolean | Whether the user is open to being hired, or `null` if not set           |
| `bio`              | string  | The user's profile bio, or `null` if not set                            |
| `twitter_username` | string  | Twitter/X username, or `null` if not set                                |
| `public_repos`     | integer | Number of public repositories owned by the user                         |
| `public_gists`     | integer | Number of public gists created by the user                              |
| `followers`        | integer | Number of users following this account                                  |
| `following`        | integer | Number of users this account follows                                    |
| `created_at`       | string  | ISO 8601 timestamp of when the account was created                      |
| `updated_at`       | string  | ISO 8601 timestamp of when the profile was last updated                 |

### Navigation Fields

These fields are API URLs pointing to related endpoints. They appear in the raw response but are not documented individually here — follow the links to explore each resource.

| Field                 | Points to                                      |
|-----------------------|------------------------------------------------|
| `url`                 | This user's API URL                            |
| `followers_url`       | List of users following this account           |
| `following_url`       | List of users this account follows             |
| `repos_url`           | List of public repositories                    |
| `gists_url`           | List of public gists                           |
| `starred_url`         | List of repositories this user has starred     |
| `subscriptions_url`   | List of repositories this user watches         |
| `organizations_url`   | List of organizations this user belongs to     |
| `events_url`          | List of public events performed by this user   |
| `received_events_url` | List of public events received by this user    |

> **Tip:** Navigation fields follow a consistent pattern throughout the GitHub API. When you see a field ending in `_url`, it is always a link to a related resource you can fetch with another API call.

---

## Null Fields

Several profile fields — `company`, `hireable`, `twitter_username`, and others — return `null` when the user has not filled them in. Always handle `null` values in your code rather than assuming a field will be populated.

---

## Common Errors

### `404 Not Found`

```json
{
  "message": "Not Found",
  "documentation_url": "https://docs.github.com/rest"
}
```

**Cause:** The username does not exist or has been deleted.  
**Fix:** Verify the username spelling. GitHub usernames are case-insensitive but must otherwise be exact.

---

### `403 Forbidden` — rate limit exceeded

```json
{
  "message": "API rate limit exceeded for YOUR_IP.",
  "documentation_url": "https://docs.github.com/rest/overview/rate-limits-for-the-rest-api"
}
```

**Cause:** Unauthenticated requests are limited to 60 per hour per IP address.  
**Fix:** Add an `Authorization` header to increase the limit to 5,000 requests per hour.

---

## Next Steps

Now that you can fetch a user profile, here are related endpoints to explore next:

- **Get the authenticated user** — `GET /user` (returns your own profile with private fields)
- **List followers of a user** — `GET /users/{username}/followers`
- **List repositories for a user** — `GET /users/{username}/repos`

**Further reading:**
- [GitHub REST API — Users](https://docs.github.com/en/rest/users/users)
- [Rate limits for the REST API](https://docs.github.com/en/rest/overview/rate-limits-for-the-rest-api)
