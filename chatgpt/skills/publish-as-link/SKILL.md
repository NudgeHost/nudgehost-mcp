---
name: publish-as-link
description: Publish text the conversation holds as a public NudgeHost link, or list the links on the user's NudgeHost account. Use when the user asks to publish, share or host a page, document, table or note as a link, or asks which NudgeHost links they have.
---

Follow the user's own instructions where they differ from these.

Use `share_text` to publish text the conversation holds. The file ending decides what the person opening the link sees:

- `.html` opens as a live web page at its own nudgehost.site address. Send a complete HTML document.
- `.md` shows as formatted Markdown.
- `.csv` opens in a table viewer.
- `.json` opens in a JSON viewer.
- `.txt`, or any other ending, shows as plain text with a Copy text button.

Each `share_text` call makes a new link, so call it once for each thing the user wants published. One call holds up to 2MB of text. Give the user the link from the result as it is written.

Use `list_links` when the user asks which links they have. It returns the newest first.

If you can't send an HTTP PUT with the file's bytes yourself, as in a ChatGPT chat, don't call create_upload_url. Tell the user to upload the file at https://www.nudgehost.com instead.

Every link is public to anyone who has it. Don't publish a password, a key or other private details unless the user asks you to.

This plugin can't delete links, add passwords or set end dates. The account owner does those in the NudgeHost dashboard at https://www.nudgehost.com/dashboard, where passwords depend on the plan. Say this when the user asks for one of them.
