# Get Repository Contents

## Overview

This endpoint returns the contents of a file or directory in a repository. File contents are returned as a Base64-encoded string — not raw text — so you must decode the `content` field to read the actual file.

Use this endpoint when you want to read a file from a repository programmatically, check if a file exists, or retrieve its SHA for a future update or delete operation.

**Endpoint:** `GET /repos/{owner}/{repo}/contents/{path}`  
**Authentication:** Required for private repositories. Public repositories can be fetched without a token.  
**GitHub Docs:** https://docs.github.com/en/rest/repos/contents#get-repository-content

---

## Prerequisites

- A GitHub account
- `curl` installed on your system
- `python3` installed (used to decode Base64 content)
- A personal access token or the GitHub CLI (`gh`) authenticated

---

## Path Parameters

| Parameter | Type   | Required | Description                                              |
|-----------|--------|----------|----------------------------------------------------------|
| `owner`   | string | Yes      | The GitHub username or organization owning the repo      |
| `repo`    | string | Yes      | The repository name                                      |
| `path`    | string | Yes      | The path to the file or directory within the repository  |

For a file at the root of the repository (like `README.md`), the path is just the filename. For a nested file, use the full relative path — for example, `src/index.php`.

---

## Query Parameters

| Parameter | Type   | Default        | Description                                                         |
|-----------|--------|----------------|---------------------------------------------------------------------|
| `ref`     | string | default branch | The branch name, tag, or commit SHA to retrieve the file from      |

---

## Request

### HTTP Method and URL

```
GET https://api.github.com/repos/{owner}/{repo}/contents/{path}
```

### Required Headers

| Header          | Value                         | Description                      |
|-----------------|-------------------------------|----------------------------------|
| `Authorization` | `Bearer YOUR_TOKEN`           | Authenticates the request        |
| `Accept`        | `application/vnd.github+json` | Specifies the GitHub API version |

---

## Example Requests

### Get a file from the default branch

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/contents/README.md"
```

### Get a file from a specific branch

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/contents/README.md?ref=main"
```

### Get a file and decode its content in one command

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/contents/README.md" \
     | python3 -c "import sys,json,base64; d=json.load(sys.stdin); print(base64.b64decode(d['content']).decode())"
```

### List the contents of a directory

Omit the filename and pass a directory path instead:

```bash
curl -H "Authorization: Bearer $(gh auth token)" \
     -H "Accept: application/vnd.github+json" \
     "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/contents/"
```

When the path points to a directory, the response is a JSON array of file and subdirectory objects instead of a single file object.

---

## Example Response

A successful request returns a `200 OK` status and a single JSON object for files, or an array for directories. Below is a real response for `README.md`:

```json
{
  "name": "README.md",
  "path": "README.md",
  "sha": "7e71f34f7fada9ed71f5cbfd9357e52d226ef001",
  "size": 1403,
  "url": "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/contents/README.md?ref=main",
  "html_url": "https://github.com/genbadar/4515339-building-php-applications-ai/blob/main/README.md",
  "git_url": "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/git/blobs/7e71f34f7fada9ed71f5cbfd9357e52d226ef001",
  "download_url": "https://raw.githubusercontent.com/genbadar/4515339-building-php-applications-ai/main/README.md",
  "type": "file",
  "content": "IyBCdWlsZGluZyBQSFAgQXBwbGljYXRpb25zIHdpdGggR2VuZXJhdGl2ZSBB\nSQo...",
  "encoding": "base64",
  "_links": {
    "self": "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/contents/README.md?ref=main",
    "git": "https://api.github.com/repos/genbadar/4515339-building-php-applications-ai/git/blobs/7e71f34f7fada9ed71f5cbfd9357e52d226ef001",
    "html": "https://github.com/genbadar/4515339-building-php-applications-ai/blob/main/README.md"
  }
}
```

The decoded `content` field for this file is the full text of the README:

```
# Building PHP Applications with Generative AI

This is the repository for the LinkedIn Learning course Building PHP Applications
with Generative AI...
```

---

## Response Fields

| Field          | Type   | Description                                                                  |
|----------------|--------|------------------------------------------------------------------------------|
| `name`         | string | The filename, including extension                                            |
| `path`         | string | The full path to the file from the repository root                           |
| `sha`          | string | The blob SHA of this file. Required if you want to update or delete the file later. |
| `size`         | integer| File size in bytes                                                           |
| `type`         | string | `file` for a file, `dir` for a directory, `symlink` for a symbolic link     |
| `content`      | string | The file contents encoded in Base64. Always present for files under 1 MB.   |
| `encoding`     | string | The encoding used for `content`. Currently always `base64`.                 |
| `url`          | string | API URL for this file at the current ref                                     |
| `html_url`     | string | URL to view the file on GitHub                                               |
| `git_url`      | string | API URL to the underlying Git blob object                                    |
| `download_url` | string | Direct URL to download the raw file contents without authentication          |
| `_links`       | object | Convenience links to `self`, `git`, and `html` representations of the file  |

---

## Understanding Base64 Encoding

GitHub returns file contents as Base64 rather than raw text because the API uses JSON, and JSON cannot safely represent binary data (images, PDFs, executables). Base64 is a way of encoding any binary data as plain ASCII text.

The `content` field also contains newline characters (`\n`) every 60 characters. These are line breaks added for readability and must be stripped or ignored when decoding.

### Decode on the command line

```bash
echo "IyBCdWlsZGluZyBQSFAgQXBwbGljYXRpb25zIHdpdGggR2VuZXJhdGl2ZSBB" | base64 --decode
```

Output:
```
# Building PHP Applications with Generative AI
```

### Decode a full API response with Python

```python
import json
import base64

with open('response.json') as f:
    data = json.load(f)

content = base64.b64decode(data['content']).decode('utf-8')
print(content)
```

---

## File Size Limit

This endpoint only returns `content` for files **under 1 MB** in size. For files between 1 MB and 100 MB, the `content` field is empty and you must use the `download_url` or the Git Blobs API instead.

| File size     | Behavior                                                       |
|---------------|----------------------------------------------------------------|
| Under 1 MB    | `content` field is populated with Base64-encoded data          |
| 1 MB – 100 MB | `content` is empty. Use `download_url` or Git Blobs API        |
| Over 100 MB   | Request fails. Use Git LFS or the Git Blobs API                |

---

## Common Errors

### `404 Not Found`

```json
{
  "message": "Not Found",
  "documentation_url": "https://docs.github.com/rest"
}
```

**Cause:** The file path does not exist, the `ref` points to a branch or commit that does not exist, or the repository is private and unauthenticated.  
**Fix:** Double-check the `path` value — it is case-sensitive. Verify the `ref` branch name with the [List Branches](./list-repository-branches.md) endpoint.

---

### `403 Forbidden` on large files

**Cause:** The file exceeds 1 MB and the `content` field cannot be returned inline.  
**Fix:** Use the `download_url` from the response to fetch the raw file directly:

```bash
curl -L "DOWNLOAD_URL_FROM_RESPONSE"
```

---

### Garbled output when decoding

**Cause:** The `content` field contains newline characters (`\n`) that some Base64 decoders treat as errors.  
**Fix:** Strip newlines before decoding:

```bash
echo "CONTENT_VALUE" | tr -d '\n' | base64 --decode
```

---

## Next Steps

Now that you can read file contents, here are related endpoints to explore next:

- **Create or update a file** — `PUT /repos/{owner}/{repo}/contents/{path}`
- **Delete a file** — `DELETE /repos/{owner}/{repo}/contents/{path}`
- **Get a Git blob directly** — `GET /repos/{owner}/{repo}/git/blobs/{file_sha}`

**Further reading:**
- [GitHub REST API — Contents](https://docs.github.com/en/rest/repos/contents)
- [Base64 encoding explained](https://developer.mozilla.org/en-US/docs/Glossary/Base64)
- [Git Blobs API](https://docs.github.com/en/rest/git/blobs)
