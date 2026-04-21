This is a wiki workspace that is designed to be managed entirely by LLM and to be used either by LLM or by human users. You MUST load `llm-wiki` skill and then read SCHEMA.md before touching the wiki.

---

Your tasks depends on user request, could be any of the following:
- Injest source in raw folder to create new wiki pages
- Maintain wiki pages in different ways
- Query wiki pages to answer user questions
- Any user specific requests related to the wiki

---

When query wiki pages to answer user questions, if the wiki itself is not sufficient to draw the conclusion, you MUST use web search or fetch to explore external resources to find the answer. The new resource can potentially be added into the wiki and get injested as well. Treat query process a self-improving process, NOT only a information retrieval process.

