# ADR 1 — ChatKit as Primary Chat Interface Layer

## Status
Accepted

## Context
We needed a way to present a chat-driven UI quickly, without building and maintaining a full chat interface from scratch. Alternatives such as custom React chat components or third-party chat UIs required significantly more engineering effort, state management, and styling work.

## Decision
Adopt ChatKit as the main conversational UI layer for all user interactions.

## Consequences
- Rapid development and immediate access to features like streaming, widgets, and message formatting.
- Less control over low-level UI customisation until later.
- Ties the project to the ChatKit ecosystem, but reduces initial development cost massively.


# ADR 2 — Use ChatKit Embed for the MVP Instead of Building a Full ChatKit UI

## Status
Accepted

## Context
The priority was speed: produce a working MVP that looks good and demonstrates the core capabilities without deep Next.js or UI work. Building a custom ChatKit UI would slow down delivery without adding value at this stage.

## Decision
Use ChatKit Embed to deliver the initial user experience.

## Consequences
- Fastest path to a working, polished UI.
- Reduced customisation options compared to a full custom ChatKit integration.
- Can be replaced later without major architectural impact.


# ADR 3 — Choose Vercel as Primary Deployment Platform over AWS

## Status
Accepted

## Context
The project required fast iteration, minimal DevOps overhead, and painless integration with Next.js. AWS was more flexible but too heavy for the speed and simplicity needed for the MVP.

## Decision
Deploy the application using Vercel as the hosting platform.

## Consequences
- Faster development cycle, zero-config deployments.
- Less fine-grained control compared to AWS.
- Simplifies scaling and edge delivery automatically.


# ADR 4 — Use Next.js for Frontend and Backend Instead of a Separate Python API

## Status
Accepted

## Context
Running a separate Python backend adds deployment complexity and requires hosting API keys outside the frontend. Next.js serverless functions give the backend capability needed without a second service.

## Decision
Use Next.js for both UI and API logic rather than maintaining a standalone Python backend.

## Consequences
- Fewer moving parts and simpler deployment pipeline.
- No need to expose API keys to the client.
- Python-specific libraries become harder to use.


# ADR 5 — Use Vercel Serverless Functions for Backend Logic

## Status
Accepted

## Context
We needed a secure place to execute server-side code, hold API keys, and perform agent interactions. Vercel serverless functions provide this without managing infrastructure.

## Decision
Implement backend logic (API calls, agent orchestration) using Vercel serverless functions.

## Consequences
- Secure secret handling.
- Scales automatically.
- Functions must remain stateless and lightweight.


# ADR 6 — Adopt OpenAI Starter App Instead of the Vercel AI Template

## Status
Accepted

## Context
The Vercel AI template is powerful but includes many components we don’t need yet (database adapters, UI systems, streaming utilities). The OpenAI starter app is simpler and faster to modify for the MVP.

## Decision
Use the OpenAI starter app as the base instead of the Vercel AI template.

## Consequences
- Less template code to rip out or refactor.
- Faster startup time for the project.
- Slightly fewer built-in features, but no complexity tax.


# ADR 7 — Use API Routes in Next.js Instead of an External Server for Secret Management

## Status
Accepted

## Context
We needed a secure place to handle API keys without exposing them in the browser. An external server would introduce extra infrastructure and authentication.

## Decision
Handle all secret-required logic in Next.js API routes.

## Consequences
- No API keys leak to the client.
- All backend functionality lives within one deployable.
- Bound to Vercel's serverless model until something more scalable is required.


# ADR 8 — Use Cloudflare for DNS and Front-Door Protection in Front of Vercel

## Status
Accepted

## Context
We wanted CDN caching, rate limiting, and optional protection without shifting off Vercel. Cloudflare’s free tier offers strong benefits with minimal setup.

## Decision
Use Cloudflare for DNS and optional security features in front of Vercel deployment.

## Consequences
- Simple performance boost and security layer.
- Slightly more complicated DNS management.
- Gives future flexibility to add throttling or WAF rules.


# ADR 9 — Use ChatKit Widgets for Interactive Structured Output

## Status
Accepted

## Context
The sailing planner and future tools need richer UI elements (maps, cards, lists) than plain text. Widgets reduce front-end development by letting the agent format interactive content.

## Decision
Adopt ChatKit widgets to render structured, interactive UI components.

## Consequences
- Faster feature development.
- Ties structured output to ChatKit’s widget system.
- Future migration to a custom UI will require translation logic.


# ADR 10 — Represent Routes Using a JSON Schema

## Status
Accepted

## Context
The sailing planner requires consistent, validated, predictable output. Without a schema, routes would drift or become inconsistent across prompts and versions.

## Decision
Define and enforce a JSON schema for route descriptions (including legs array).

## Consequences
- Predictable data structure for rendering and storage.
- Easier to validate and test.
- Agents must always adhere to the schema, requiring occasional correction.


# ADR 11 — Maintain Agent Instructions Outside the Prompt to Reduce Token Cost

## Status
Accepted

## Context
The sailing planner uses structured instructions, canonical routes, and constraints. Putting all this in every request inflates token usage and slows responses.

## Decision
Store long-form instructions outside prompts (e.g., in configuration or files) and keep runtime prompts minimal.

## Consequences
- Lower token usage and lower cost.
- Cleaner prompts.
- Requires a file-based or configuration-based instruction system.


# ADR 12 — Use Multi-Pane UI (Chat, History, Saved Trips) for Pro Version

## Status
Accepted

## Context
Users need quick navigation between conversations and stored trips. A single-pane chat UI becomes cluttered and is hard to scale for power users.

## Decision
Adopt a multi-pane layout for the Pro version: chat, trip history, saved routes.

## Consequences
- Better UX and future-proofing.
- More Next.js work to implement state and navigation.
- More complexity than the simple MVP embed.


# ADR 13 — Store Chat History and Saved Trips in the Application Database, Not Agent Memory

## Status
Accepted

## Context
Agent memory is volatile, expensive, and not designed for durable, user-specific state. Saved trips and chat logs need to persist between sessions.

## Decision
Use the application’s database (or persistence layer) to store chat history and saved trips.

## Consequences
- Reliable user history.
- Enables future multi-device access.
- Requires schema design and database integration later.


# ADR 14 — Use Token-Based Usage Model Instead of Subscription-Only

## Status
Accepted

## Context
AI usage costs scale per request. Subscription-only models risk abuse by heavy users and alienate light users. Tokens give a fairer, usage-aligned model.

## Decision
Use a token-based model where paid plans allocate tokens, and tokens roll over.

## Consequences
- Fair pricing aligned to cost.
- Requires token accounting in backend.
- Easier to add a free tier with throttling.


# ADR 15 — Use Stripe for Payments

## Status
Accepted

## Context
We needed a modern payment system supporting usage-based billing, webhooks, and customer management. Stripe is the industry standard with excellent developer tooling.

## Decision
Integrate Stripe for payments and customer billing.

## Consequences
- Fast integration.
- Minimal long-term lock-in.
- Some complexity managing webhooks in serverless environments.

# ADR 16 — Adopt OpenAI Agents Instead of LangGraph

## Status
Accepted

## Context
We needed an orchestration layer for AI reasoning, tool use, and structured outputs. LangGraph is powerful but adds conceptual overhead, hosting complexity, and more code. OpenAI Agents provide orchestration with minimal setup and far tighter integration with the chosen model. Open AI also provide a visual [agent builder tool](https://platform.openai.com/agent-builder).

## Decision
Adopt OpenAI Agents as the default orchestration layer instead of LangGraph.

## Consequences
- Less infrastructure and less code to maintain.
- Faster development of workflows.
- Some orchestration patterns may require more creativity compared to LangGraph’s graph-based model.
- Cost implications in terms of LLM use.

# ADR 17 — Reject Python Backend in Favour of Full Next.js Stack

## Status
Accepted

## Context
A separate Python backend creates deployment, scaling, and secret-storage complexity. The project does not currently require Python-specific libraries.

## Decision
Do not implement or maintain a Python backend; use Next.js exclusively.

## Consequences
- One codebase to deploy and maintain.
- Simplifies CI/CD and hosting.
- Python tooling becomes impractical until or unless needed later.


# ADR 18 — Reject AWS Amplify / Serverless for MVP

## Status
Accepted

## Context
The MVP requires minimal DevOps overhead and rapid iteration. AWS Amplify or a full serverless pattern (Lambda, API Gateway, Cognito, DynamoDB) would slow development.

## Decision
Do not use AWS Amplify or AWS serverless services for the MVP.

## Consequences
- Simpler infrastructure.
- Faster prototyping.
- Limits the ability to deeply customise backend architecture until post-MVP.


# ADR 19 — Exclude Custom tools for data from MVP

## Status
Accepted

## Context
Accurate data requires external APIs, paid data sources, and domain-specific algorithms. Implementing this early would delay the MVP significantly.

## Decision
Do not use custom tools and API's in the MVP.

## Consequences
- Faster MVP delivery.
- Route recommendations are conservative and generic.
- Real-time routing added as a premium feature later.

# ADR 20 — Use Safe default for user preferences

## Status
Accepted

## Context
There are many input parameters we could use for user preferences.  However, this will create a less streamlined experience as well as increase model usage (and therefore costs).

## Decision
Implement safe, reasonable defaults rather than force users to enter all details.

## Consequences
- Simpler user experience
- Lower costs due to LLM usage
- Less tailored output without additional user prompting

# ADR 21 — Keep Canonical Routes Outside Agent Instructions to Reduce Token Cost

## Status
Accepted

## Context
Canonical sample routes are long. Including them inside the agent’s system prompt inflates token counts and slows responses.

## Decision
Store canonical routes (and other contextual data) externally (JSON or file-based), referencing them only when required.

## Consequences
- Lower runtime costs.
- Cleaner, shorter prompts.
- Requires explicit file reads where needed.


# ADR 22 — Reject External Vector Databases for MVP

## Status
Accepted

## Context
Vector DBs (Pinecone, Weaviate, Chroma) offer sophisticated retrieval features, but are unnecessary for the MVP and increase hosting, cost, and complexity.

## Decision
Do not integrate any external vector database until retrieval needs exceed OpenAI’s built-in RAG.

## Consequences
- Lower infrastructure cost.
- Fewer dependencies.
- Limits custom search behaviour until later.

# ADR 23 — Use a Splash Screen with Logo and Tagline for Empty State

## Status
Accepted

## Context
A blank screen creates a poor first impression. A simple splash screen provides guidance, branding, and a clear call-to-action when no chat is open.

## Decision
Show a logo and tagline ("Where to today?") when the chat is empty.

## Consequences
- Better user experience.
- Professional first impression.
- Very low implementation cost.

# ADR 24 — Use Side-Panel Navigation for Chat History and Saved Trips

## Status
Accepted

## Context
Users need to browse previous chats and saved routes. Embedding everything in the main chat stream becomes messy and unusable.

## Decision
Implement a side panel for browsing chat history and saved trips.

## Consequences
- Cleaner navigation.
- Supports multi-session UX.
- Requires more UI structure than the simple ChatKit embed.

# ADR 25 — Support Overage Charging for Token Use Exceeding Plan

## Status
Proposed

## Context
Users may occasionally exceed their plan limits. Simply blocking them harms UX, while charging for overage is fair and scalable.

## Decision
Implement overage-based billing for users exceeding their monthly token allocation.

## Consequences
- Better UX than hard limits.
- Requires Stripe metered billing integration.

# ADR 26 — Never Expose API Keys in Client-Side Code

## Status
Accepted

## Context
ChatKit embeds share JavaScript on the client. Exposing API keys in public JS is a major security risk.

## Decision
All secrets must reside in serverless functions or environment variables.

## Consequences
- Strong security posture.
- Requires backend routing for all AI interactions.
- Slightly more code than direct client calls.


# ADR 27 — Use Vercel Environment Variables for Secret Management

## Status
Accepted

## Context
We needed a safe way to store secrets. Environment variables in Vercel provide secure, encrypted storage tied to deployments.

## Decision
Store API keys and credentials in Vercel environment variables.

## Consequences
- No secrets committed to Git.
- Per-environment values (dev/prod) are possible.
- Requires Vercel for all deployments.


# ADR 28 — Use Cloudflare Rate Limiting for Guest Throttling

## Status
Accepted

## Context
Guest mode requires throttling. Rate limiting is best handled at the edge rather than the app layer.

## Decision
Use Cloudflare’s rate limiting to control guest usage.

## Consequences
- Protects backend from abuse.
- Offloads throttling to the edge.
- Requires rule configuration in Cloudflare dashboard.

# ADR 29 — Use Free Guest Mode with Pragmatic Throttling

## Status
Accepted

## Context
We needed a way to let new users try the product without paying, but building a full authentication system with usage quotas would delay the MVP. A simple, good-enough throttling method was required to control costs without introducing heavy infrastructure or complex accounting.

## Decision
Implement a lightweight throttling approach using Cloudflare rate limiting combined with basic server-side request counting, instead of a full quota-management system.

## Consequences
- Quick to implement and sufficient for MVP traffic levels.
- Not as precise as full token-based metering, but effective at limiting abuse.
- May require revisiting once usage grows or paid plans scale.
