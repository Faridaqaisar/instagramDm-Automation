# instagramDm-Automation
Instagram DM Automation with Interest Detection  An AI-powered system that responds to Instagram DMs in real time, answers questions using a business's own knowledge base, and detects when a contact is ready to buy, automatically alerting the team and following up with a next step, rather than leaving every message for a human to triage.
human to triage.

What it does:

Receives Instagram DMs through Meta's webhook, verified via a custom GET/POST handshake built directly in n8n
Identifies new vs. returning contacts automatically, using the sender's Instagram PSID as a persistent identity key
Uses an AI Agent (Google Gemini) with a RAG-based knowledge base tool to answer questions grounded in real business information, not invented answers
Maintains per-contact conversation memory, so the AI doesn't repeat itself across a conversation
Detects buying intent conversationally, not just from an exact keyword, recognizing phrasing like "sounds good, let's do it" the same as "I'm interested"
Interested contacts get an instant Slack alert to the team and a status update in the database; all contacts receive an automatic reply either way
Replies are sent back through Instagram's Graph API directly from the workflow
Architecture highlights:

Custom webhook verification, built without relying on a pre-made trigger node, handling Meta's hub.challenge handshake explicitly
Contact identity resolution via PSID lookup/creation, same pattern used across the other two projects, adapted for a channel with no phone number or email available
RAG-grounded responses using Supabase pgvector, with an explicit instruction layer preventing the AI from guessing when the knowledge base has no relevant match
Structured intent detection extracted from natural conversation via a tagged output, rather than a rigid keyword match
(Postgres + pgvector), LangChain-based AI Agent with persistent memory, Slack API

Known limitations:

Average response time is 30 to 60 seconds per message, due to chained model calls (main reasoning plus a separate knowledge-base summarization call) and vector search running in sequence
Conversation history logging to the database is not yet wired up on either branch; only the contact's status updates currently
The comment-to-DM automation from the original design (replying to a public comment by starting a DM) is not yet built, only direct DMs are handled
An earlier version of the system occasionally answered questions outside the knowledge base's scope by inferring from adjacent context rather than declining; this was caught in testing and fixed by adding an explicit instruction against guessing, worth re-testing periodically as the model or knowledge base changes[Instagram DM Agent - Main Pipeline.json](https://github.com/user-attachments/files/33045339/Instagram.DM.Agent.-.Main.Pipeline.json)


