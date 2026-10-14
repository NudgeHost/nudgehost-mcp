# Publishing a ZIP as a site from Cursor

Cursor is the client NudgeHost tested end to end with a ZIP. On 13 October 2026 it published a ZIP through these tools and got back a working site link. This walks through the same three steps.

You need the Cursor setup from the [README](../README.md#connecting-a-client) and `NUDGEHOST_API_KEY` set to a key from a free account.

## What you ask for

Put the site's files in a folder with an `index.html` at the top, then ask Cursor something like this.

> Zip the `dist` folder and publish it to NudgeHost as a site. Tell me the link.

Cursor then takes three steps, two of them tool calls you can follow in its tool-call log.

## 1. `create_upload_url`

Cursor zips the folder, measures the archive, and starts the upload.

```json
{
  "filename": "site.zip",
  "content_type": "application/zip",
  "size_bytes": 48213
}
```

`size_bytes` has to be the archive's exact size in bytes, because it is signed into the upload URL and storage refuses a body of any other length. The number above stands in for whatever your archive measures.

The reply gives the upload URL, the two headers to send with the bytes, and an id for step 3. The URL stops working after 300 seconds. It carries its own credential, so it is a secret and the API key does not go with it.

## 2. Send the bytes

Cursor sends the archive to that URL from its terminal, with the headers the reply named.

```bash
curl -X PUT "<the upload URL from step 1>" \
  -H "Content-Type: application/zip" \
  -H "Content-Length: 48213" \
  --data-binary @site.zip
```

## 3. `complete_upload`

Cursor finishes the upload with the id from step 1.

```json
{
  "id": "<the id from step 1>"
}
```

A ZIP holding an index file becomes a hosted site at its own address, so the URL this call returns is the final one, and it can differ from the address step 1 predicted. That returned URL is the link to share.

If the URL from step 1 lapses before the bytes arrive, Cursor calls `create_upload_url` again for a fresh one.

## Smaller jobs

For a single HTML page, `share_text` publishes it in one call, with no upload steps. Give the file a name ending in `.html` and it opens as a page in the browser.
