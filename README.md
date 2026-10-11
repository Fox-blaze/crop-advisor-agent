# AgriGuide Connect

**Weather-aware, multilingual AI crop advice for small farmers, delivered through the Kisan Sahayak Telegram bot.**

Built for the IBM-AICTE AI internship (Agentic AI project) · Supports **UN SDG 2: Zero Hunger** (targets 2.3 and 2.4)

**Try the bot:** https://t.me/kisan_sahayak_demo_bot
**Built by:** Tarsh Jain

---

## The problem

Small and marginal farmers make sowing, spraying and irrigation decisions without timely local advice. Extension officers and helplines are stretched thin, generic advice ignores local weather, and serious cases (pesticide illness, total crop loss, insurance claims) need a human expert.

## What it does

A farmer messages the **Kisan Sahayak** bot on Telegram, in Hindi, Hinglish or English. An AI agent decides which tools to use, replies with short practical advice, and escalates risky cases to a human.

- **Weather-aware:** checks the forecast for the farmer's town before advising on spraying, irrigation and sowing
- **Local knowledge:** looks up a crop profile (22 crops) and a state profile (17 states) from a database
- **Conversation memory:** remembers the earlier messages in the chat
- **Season-aware:** uses today's date to judge crop stage and what can be sown now
- **Safe escalation:** pesticide illness, whole-crop loss, loans, insurance or a request for a human get a short safe reply plus the Kisan Call Centre (1800-180-1551) and KVK hand-off
- **Feedback:** thumbs-up and thumbs-down buttons, saved against each reply
- **Logging:** every query and its feedback is stored in a Supabase database

## How it works

```
Farmer (Telegram)
      |
      v
n8n workflow ---- button tap? ---- yes --> save feedback in Supabase --> answer the tap
      |
      no
      v
AI agent (Gemini + memory) ---- tools ----> find_location, get_weather (Open-Meteo)
      |                                     regional_crops, crop_knowledge (Supabase)
      v
save row in Supabase --> reply with feedback buttons
```

## Tech stack

n8n (workflow and AI agent) · Google Gemini · Open-Meteo (geocoding and forecast) · Supabase (Postgres) · Telegram Bot API. Everything runs on free tiers.

## Repository contents

| Path | What it is |
|---|---|
| `workflow/crop-advisor-agent.json` | The n8n workflow export (credentials are not included) |
| `database/schema.sql` | Supabase tables: `query_log`, `crop_knowledge`, `state_crops` |
| `docs/` | Screenshots, architecture diagram and test results |

## Setup

1. **Telegram bot:** create one with @BotFather and copy the token.
2. **Gemini:** create an API key in Google AI Studio.
3. **Supabase:** create a project, then run `database/schema.sql` in the SQL Editor. Copy the project URL and the `service_role` key.
4. **n8n:** import `workflow/crop-advisor-agent.json`, then create credentials for Telegram, Google Gemini and Supabase, and select them on the nodes that ask for them.
5. **Publish** the workflow. Do not click "Execute workflow" afterwards, because test mode takes the bot over from the live version.

Never commit tokens or keys. Keep them in n8n credentials only.

## Testing

An automated n8n test workflow sent 18 test messages (crop advice, weather, escalation, off-topic and memory) to a copy of the agent and checked the results.

| Run | Result |
|---|---|
| Run 1, basic checks | 100% |
| Run 1 re-scored with stricter criteria (wrong script or language, unnecessary tool calls, glitches) | 39% |
| Final run after prompt fixes | 90% |

Replies in the tests took about 15 to 35 seconds. These are prototype test results, not measures of impact on farmers.

## Business model (proposed, untested)

Free for farmers. Proposed payers: FPOs (Rs 500 per month for a branded advisory bot plus a monthly report of members' top crop problems) and NGOs or CSR programmes (Rs 2,500 per programme per season). KVKs and state agriculture departments are pilot partners.

## Limitations

- Advice is general guidance and has not been validated by agriculture experts
- Crop and state data must be checked against ICAR, JNKVV and local KVK sources
- Free-tier limits affect speed and volume, and Simple Memory resets if the server restarts
- Hindi, Hinglish and English only

## Future scope

Proactive weather alerts, leaf photo diagnosis, a WhatsApp channel and voice replies, more languages, states and crops, and an expert-verified knowledge base through KVK partnerships.
