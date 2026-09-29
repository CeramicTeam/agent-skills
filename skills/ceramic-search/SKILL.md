---
name: ceramic-search
description: Web search for AI agents using Ceramic. Use for accurate current information — news, prices, recent events, documentation, general fact checking. Trigger this skill for keywords like "latest", "recent", "look up", "search for", "find online", "what's happening".
---

# Ceramic Search

Lexical (keyword-based) search engine built for AI agents.

# Usage

1. **Rewrite the natural language query**

   Ceramic matches exact keywords — it does not interpret natural language or synonyms automatically. Convert the user's natural language query into a keyword query of **2–8 words**.

   Rules:
   - Extract specific entities, topics, locations, and dates
   - Replace conversational phrasing with concrete keywords
   - Do not include uninformative words such as articles (the, a, an). Avoid prepositions (on, about, in, for, of, at, by, with) unless they are within established phrases or names (United States of America, Into the Wild).
   - Include relevant synonyms explicitly when terminology is ambiguous
   - Keep word order meaningful (`house cat` and `cat house` return different results)
   - Good keyword query examples:
     - "2026 Super Bowl halftime performer"
     - "climate change effects global warming impact"
     - "beginner investing strategies stocks bonds basics"

2. **Run the search**

   Both methods accept the same parameters:
   - `query` (required): the rewritten keyword query
   - `maxDescriptionLength` (optional): characters per result description, 1000–8000. Omit it to use the default of 3000. Use a higher value only when the user needs more detail.
   - `maxResults` (optional): number of results, 1–20. Omit it to use the default of 10.

   **Option A — Ceramic MCP tool (preferred when available).** If a Ceramic MCP tool named `ceramic_search` is available in your tool list, call it with the parameters above using the MCP tool invocation mechanism. Do not attempt to call it as a local function.

   ```json
   {
     "query": "2026 Super Bowl halftime performer"
   }
   ```

   **Option B — Ceramic Search API.** If no `ceramic_search` MCP tool is available, call the API with `curl`. It reads the API key from the `CERAMIC_API_KEY` environment variable:

   ```bash
   curl -sS https://api.ceramic.ai/search \
     -H "Authorization: Bearer $CERAMIC_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{"query": "2026 Super Bowl halftime performer"}'
   ```

   If `CERAMIC_API_KEY` is not set, or the API returns `401`, stop and tell the user to create an API key at https://platform.ceramic.ai/keys and set it as the `CERAMIC_API_KEY` environment variable. Never print, echo, or log the API key.

3. **Retrieve the top sources from the response**

   Results are ordered by relevance, with the first result being the strongest match. Each result includes `title`, `url`, and `description`.

   - The MCP tool returns a top-level `results` array. Each result also includes a `rank`.
   - The API nests the array under `result.results`:

     ```json
     {
       "requestId": "request id",
       "result": {
         "results": [
           {
             "title": "search result title",
             "url": "search result url",
             "description": "search result description"
           }
         ],
         "totalResults": 10
       }
     }
     ```

4. **Summarize with citations**

   Write a concise answer drawing from the search result descriptions, and then list sources as numbered references:

   **Sources**
   1. [Title](url)
   2. [Title](url)

   Only cite sources whose descriptions contributed to the answer. If the search returns no useful results, refine the query with more specific keywords and try again before giving up.
