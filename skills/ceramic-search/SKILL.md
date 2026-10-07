---
name: ceramic-search
description: Use this skill when the user needs current or verifiable information from the web — news, recent events, prices, releases, product or API documentation, or fact checks — even if they don't explicitly ask to search. Searches with Ceramic, a keyword-based web search engine.
---

# Ceramic Search

Ceramic is a lexical (keyword-based) web search engine built for AI agents. It matches exact words and phrases — it does not infer intent, synonyms, or missing context from a vague query.

# Usage

1. **Rewrite the question as keyword queries**

   Convert the user's question into keyword queries of **2–8 words**.

   - Extract specific entities, topics, locations, and dates.
   - **Keep every hard constraint** from the question: the entity, city, state or country, year, product, company, team, league, or event. Dropping one returns results about the wrong thing.
   - Replace relative time words such as `latest`, `current`, `recent`, `this year`, or `today` with the specific year or date. Use the current date from your context, or run `date` if you're unsure.
   - Do not include task words or filler such as `find`, `search`, `verify`, `official page`, or publisher names, unless the word is part of what you are looking for.
   - Do not include articles (the, a, an). Avoid prepositions (on, about, in, for, of, at, by, with) unless they are part of an established phrase or name (United States of America, Into the Wild).
   - Add synonyms explicitly when terminology varies. Ceramic will not map them for you.
   - Keep word order meaningful (`house cat` and `cat house` return different results).

   **Never include these in a query:**
   - Slashes. This means no URLs, links, or file paths
   - Numbers with commas, such as `101,342`
   - Non-Latin characters

   | Question | Bad query | Good query |
   |---|---|---|
   | What are United Airlines' carry-on size limits? | `carry-on dimensions` | `United Airlines carry-on size` |
   | Who performed at the Super Bowl halftime show this year? | `Super Bowl halftime show performer` | `2026 Super Bowl halftime performer` (if asked in 2026) |
   | Find the official schedule for Los Angeles bulky item pickup | `find official page schedule Los Angeles` | `Los Angeles bulky item pickup` |
   | How do I start investing? | `how do I start investing` | `beginner investing strategies stocks bonds basics` |

   For questions that aren't a simple lookup, run two or three variants and merge the results: one naming the entity (`FAA drone registration`), one using words likely to appear in the answer (`drone registration 250 grams`), and one with synonyms (`recreational drone weight limit`).

2. **Run the search**

   Both methods accept the same parameters:
   - `query` (required): the keyword query
   - `maxDescriptionLength` (optional): characters per result description, 1000–8000. Omit it to use the default of 3000. Use a higher value only when the user needs more detail.
   - `maxResults` (optional): number of results, 1–20. Omit it to use the default of 10.

   **Default — Ceramic MCP tool.** If a Ceramic MCP tool named `ceramic_search` is available in your tool list, call it with the parameters above using the MCP tool invocation mechanism. Do not attempt to call it as a local function.

   **Fallback — Ceramic Search API.** If no `ceramic_search` MCP tool is available, call the API with `curl`. It reads the API key from the `CERAMIC_API_KEY` environment variable:

   ```bash
   curl -sS https://api.ceramic.ai/search \
     -H "Authorization: Bearer $CERAMIC_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{"query": "2026 Super Bowl halftime performer"}'
   ```

   The API returns the results under `result.results`, ordered by relevance. Each result has `title`, `url`, and `description`.

   If `CERAMIC_API_KEY` is not set, or the API returns `401`, stop and tell the user to create an API key at https://platform.ceramic.ai/keys and set it as the `CERAMIC_API_KEY` environment variable. Never print, echo, or log the API key.

3. **Answer with citations**

   Write a concise answer from the result descriptions, then list the sources you used as numbered references:

   **Sources**
   1. [Title](url)
   2. [Title](url)

   - Only cite URLs that appear in the search results, and only those whose descriptions contributed to the answer. Never add a URL from memory, even for an official source you expect to exist. You can suggest the user check an official site, but don't list it under **Sources**.
   - Say when the evidence is weak, stale, incomplete, or not from an authoritative source, instead of presenting it as fact.
   - If the results aren't useful, refine the query with more specific keywords and try again before giving up.

# When Ceramic alone is not enough

Ceramic returns indexed pages with text descriptions. Use another tool, or tell the user its limits, when the task needs:
- Live structured data, such as weather, stock prices, sports scores, or flight status
- The full content of a page beyond the result description
- Paywalled or non-public content
