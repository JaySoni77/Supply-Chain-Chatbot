# SCM Assistant Bot — Trinamix AI Engineer Case Study

## Public Chatbot URL
https://cloud.flowiseai.com/chatbot/54a9eb5e-117f-42c2-be0d-582908417a66

## Csv agent link ( i have used this as custom tool inside the main agent flow)
https://cloud.flowiseai.com/chatbot/59510f8b-24f3-4f46-bbdb-6a53e04ec232

## GitHub Repository
https://github.com/JaySoni77/Supply-Chain-Chatbot

## Stack Used
- **LLM:** [Mistral / OpenAI / "nvidia/nemotron"]
- **Embeddings:** [ huggingface embedding / sentence-transformers/all-MiniLM-L6-v2]
- **Vector Store:** [Flowise in-memory / Qdrant]

## Chunk Configurations Tried

| Config | Chunk Size | Overlap | Chunks (CSV) | Chunks (PDF) |
|--------|------------|---------|--------------|--------------|
| Config 1 | 500 | 50 | X | X |
| Config 2 | 1000 | 200 | X | X |

Config 2 gave better answers.

```markdown
## Q&A — Sample Questions & Chatbot Responses

**Q1:** Which Tier-3 suppliers have an active disruption flag, and what response level applies per policy?

**A:** There are 11 Tier-3 suppliers with an active disruption flag: Dravex Components India,
Plataforma Metales SA, Maghreb Castworks, Helios Pack Greece, Cerromax Mineria, Orinoco Pack SAPI,
Quetzal Textiles, Sibertek Molding, Archipelago PCB Corp, Varna Electronics EAD, and Deltaforge
Vietnam. Since all are classified as High Risk with an active disruption flag, Policy §9 mandates a
Level 3 Activate response — this includes CPO escalation and shifting at least 40% of volume to an
alternate supplier.

---

**Q2:** Which suppliers qualify for the annual Volume Rebate Program and how many are there?

**A:** 19 suppliers meet the Volume Rebate Program criteria under Policy §4.2, which requires Tier-1
status, OTD ≥ 93%, defect rate below 0.5%, and a Sustainability Score of 85 or higher. The qualifying
suppliers are: Borealis Composites, Crestline Chemical Supply, Fenwick Alloy Solutions, Hanguk Circuit
Works, Hokkaido Alloy Tech, Krauss-Polymex GmbH, Lakeshore Components, Lumivex Semiconductor NL,
Maplewood Polymer Corp, Norbec Alloy Works, Nordloom Finland Oy, Orrentek Precision Mfg, Ostwind
Composites AG, PrecisionForge Taiyuan, Solveig Eco Packaging, Straits Packaging Hub, Tasman Circuit
Boards, Toreval Electronics, and Valdoro Special Alloys.

---

**Q3:** Which region has the highest total PO value, and does it breach the concentration limit?

**A:** EMEA leads with a total PO value of $193,987,179.91, which accounts for roughly 48.5% of the
overall spend of $399,563,494.10. This exceeds the 45% regional concentration cap defined in Policy
§5.3, triggering a mandatory Diversification Plan to be submitted within 60 days.

---

**Q4:** Which suppliers are on Supplier Watch List (SWL) status and what does it restrict?

**A:** 11 suppliers with a Compliance Score below 60 are on the Supplier Watch List: Deltaforge Vietnam
(52.1), Maghreb Castworks (53.8), Helios Pack Greece (54.2), Cerromax Mineria (55.0), Orinoco Pack
SAPI (56.3), Varna Electronics EAD (57.1), Quetzal Textiles (57.5), Plataforma Metales SA (58.0),
Archipelago PCB Corp (58.4), Dravex Components India (58.9), and Sibertek Molding. Per Policy §3.4,
SWL status restricts new PO issuance to a maximum of 20% of the prior quarter's volume.

---

**Q5:** Which product category has the highest average defect rate and does it exceed the Tier-2 limit?

**A:** Mechanical Components has the highest average defect rate at 2.12% across 360 purchase orders.
The Tier-2 defect ceiling under Policy §3.2 is 2.50%, so there is no breach — however the rate is
close enough to warrant monitoring.
```

## What I'd Improve

### 1. Retrieval & Accuracy
- Implement **metadata filtering** so queries about specific tiers, regions, or suppliers
  skip irrelevant chunks entirely — reducing hallucination risk on numerical questions
- Add a **reranker** (e.g. Cohere Rerank) on top of vector search to prioritize the most
  relevant chunks before passing to the LLM
- Use **hybrid search** (keyword + semantic) since supplier names and policy section
  numbers are exact-match lookups that pure vector search can miss

### 2. Data Handling
- Pre-process the CSV into **aggregated summaries** (per supplier, per region, per tier)
  and store them as separate documents — this gives the LLM cleaner context for
  analytical questions like spend concentration or defect averages
- Parse the PDF policy into **section-level chunks** (§1, §2 ... §10) rather than
  fixed-size splits, so policy references always land in full context
- Add **structured tool calling** to handle pure numerical queries (total PO value,
  defect averages) via direct CSV computation rather than relying on retrieved text
