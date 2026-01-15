## Pearl Hybrid Intelligence: AI Draft + Expert Verification (Across 100+ Expert Domains)

Turn complex user questions into high-confidence answers by combining AI speed with professional human verification.

[Pearl](https://www.pearl.com/enterprise) provides access to a network of **20,000+ licensed/credentialed experts** (including **JDs, MDs, PhDs, DVMs, CPAs, engineers**) spanning **100+ domains**—not just Legal or Health (those are simply example prompts and categories).

### What this template does
- Accepts an OpenAI Chat Completions–style `messages[]` array via webhook (your client retains conversation history).
- Uses an **intake/clarification gate**: the model returns either `END` (ready to answer) or a single clarifying question.
- If clarification is needed, returns the question to the caller immediately.
- If ready, generates a **draft answer** with OpenAI.
- Automatically sends the draft and chat history to the **Pearl Hybrid Intelligence MCP server** for **professional expert verification**.
- Returns the verified answer (plus expert attribution) to the caller.

### How it works
1. **Webhook intake** receives `messages[]` and an optional `model`.
2. **OpenAI – Clarify** decides whether more intake is needed:
   - Returns **`END`** if there’s enough information, or
   - Returns **one** best clarifying question.
3. **Branching**
   - If not `END`: respond with `status=needs_clarification`.
   - If `END`: proceed to drafting.
4. **OpenAI – Draft Answer** produces the draft response.
5. **Pearl MCP – verifyAnswer** routes the draft + history to a licensed Pearl expert for verification.
6. **Webhook response** returns `status=verified`, the verified answer, and the verifying expert’s name/expertise.

### Setup steps (10–15 minutes)
- Connect an **OpenAI credential** in n8n (used by the Clarify + Draft steps).
- Connect a **Pearl MCP credential** (Header Auth / API key) for the MCP Client node.
- Activate the workflow and call the **Production Webhook URL** with your `messages[]` payload.

> Detailed setup notes and example requests/responses are included as sticky notes inside the workflow.

### Customization
- Update the **intake (clarification)** and **draft answer** prompts in the `Prepare Input` node to match your end-user needs (domain, tone, risk policy, compliance requirements).
- Adjust response formatting (JSON shape, additional fields, disclaimers) in the two **Respond to Webhook** nodes.
- If your Pearl MCP tool name or response schema differs, update the **MCP Client** node configuration and the final response mapping.

### Recommended use cases
- Any workflow where missing context is common and you want AI to collect intake before answering.
- High-stakes domains (legal, health, financial, engineering, automotive, home services, and more) where expert verification improves trust.
- Products that need a stateless webhook interface while the client manages conversation state.

### Input payload format
Send a JSON body like:

```json
{
  "model": "gpt-4o-mini",
  "messages": [
    { "role": "user", "content": "My question..." }
  ]
}
