# Tencent RTC LLM Docs

This directory provides LLM-oriented documentation entry files.

## How to use

If you want an LLM to read Tencent RTC docs, provide the **raw text URL** instead of a repository web page URL.

Raw text URLs are usually better for LLM retrieval because they:

- contain less navigation and page chrome noise
- are easier to parse as plain text
- produce cleaner context with lower token waste

## Recommended entry files

### Chinese

- Index: `zh/llms.txt`
- Full product docs: `zh/llms-full-*.txt`

Recommended raw URL pattern:

- `https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt`
- `https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms-full-<product>.txt`

### English

- Index: `en/llms.txt`
- Full product docs: `en/llms-full-*.txt`

Recommended raw URL pattern:

- `https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt`
- `https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms-full-<product>.txt`

## Which file should I give to the LLM?

- Use `llms.txt` when you want the LLM to discover the right document first.
- Use `llms-full-*.txt` when you already know the product and want deeper retrieval.

## Quick choice

- If you do not know which document to use, give the LLM `llms.txt`.
- If you already know the product, give the LLM `llms-full-<product>.txt`.
- If you already know the exact page, give the LLM the raw URL of that Markdown file.

## Single-page Markdown

If you only want the LLM to read one document, provide the raw URL of that Markdown file directly.

Examples:

- Chinese page:
  `https://gitee.com/Tencent-RTC/docs/raw/main/zh/webrtc/plugins/watermark.md`
- English page:
  `https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/webrtc/plugins/watermark.md`

Use a single-page Markdown URL when the question is already focused on one feature, one API, or one guide.

Do not use the rendered repository page URL when a raw Markdown URL is available.

## Notes

- Prefer raw text URLs over rendered repository pages.
- `zh/**` and `en/**` contain the source Markdown files.
- `llms.txt` is the lightweight index.
- `llms-full-*.txt` contains aggregated product-level retrieval docs.
