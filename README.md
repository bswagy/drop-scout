# Drop Scout

**Taste intelligence for small streetwear brands.** Describe your brand, and Drop Scout's agent queries Qloo's taste graph to find the music artists, brands and cities your audience already loves, then turns that evidence into what to drop next and where to sell it.

Built for the Qloo Agentic Hackathon (2026).

## The problem

Small merch brands guess. They don't have a data team, so they guess which artists to collab with, which cities to run a pop-up in, and what to make next. An ordinary chatbot just guesses too, with confidence. Drop Scout grounds every recommendation in Qloo's real cross-domain taste data and shows the evidence.

## How it works

1. You describe the brand in a sentence or two.
2. A Gemini agent receives Qloo's tools (through the official `qloo mcp` server) and plans its own queries:
   - **Resolve:** `qloo_find_tags` / `qloo_describe` turn words like "West Coast hip-hop" or "streetwear" into Qloo tags and entities.
   - **Discover:** `qloo_recommend` finds artists, brands and other entities fans of those signals also like.
   - **Locate:** `qloo_where_popular` finds where that taste is concentrated, for pop-ups.
   - **Understand:** `qloo_audience_demographics` and `qloo_trends` describe the aggregate audience and whether it's rising.
3. The agent writes a short brief (audience, artists, brands, pop-up cities, tour and event tie-ins, next drop, marketing moves). Each idea is tied to a Qloo finding and comes with a result to measure.
4. The **evidence trail** lists every Qloo call the agent made, with its status and inputs, so you can check where each claim came from.

The agent is told never to name an artist, brand or city that didn't come back from a Qloo result in that session.

## Run it locally

Requirements: Node.js 22.19 or newer, a Qloo event key, and a Google Gemini API key.

```sh
git clone https://github.com/<you>/drop-scout.git
cd drop-scout
npm install
export QLOO_API_KEY=...      # never commit this
export GEMINI_API_KEY=...    # never commit this
npm run check                # connects to Qloo and lists tools
npm start                    # http://localhost:3000
```

Optional: `GEMINI_MODEL` (default `gemini-2.5-flash`), `PORT`, and `QLOO_BASE_URL` / `QLOO_TRUSTED_BASE_URL` (both default to the hackathon API, `https://hackathon.api.qloo.com`).

## Deploy

Run it as a long-running Node web service, not serverless, because `qloo mcp` runs as a local process. On Render: New → Web Service → this repo, build `npm install`, start `npm start`, then add `QLOO_API_KEY` and `GEMINI_API_KEY` as environment secrets.

## Responsible data use

- Only the brand description is sent to the model. No names, emails or personal data are sent to Qloo.
- Qloo results describe aggregate cultural affinities, not facts about any individual.
- Keys live only in server environment variables. The browser never calls Qloo or Gemini directly.
- Results are cached for 6 hours per brand description, and requests are rate-limited, to respect the event quota.

## Known limitations

- Results depend on how well the brand description maps to Qloo tags. When a match is ambiguous, the agent picks the closest one and says so.
- Affinity is not causation. A city where a taste is popular is a lead to test, not a sales forecast.
- The free Gemini tier has rate limits, so heavy traffic may get a "try again" message.

## License

MIT
