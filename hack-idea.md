nopCommerce Agentic Hack — Challenge Track
0) Repo intake with Copilot Agent Mode

Goal: force agentic comprehension of a large codebase.
Build: in VS Code, use Copilot Agent Mode to scan the solution (Nop.Core, Nop.Services, Nop.Web, plugins). Ask for:

list of core domain entities (Order, Customer, Product, ShoppingCart, Payment, Shipment).

identify service classes and key extension points (events, plugins).
Generate an ADR: “Expose nopCommerce services as MCP Tools.”
Accept: /docs/architecture-overview.md and /docs/ADR-001-mcp-tools.md.

1) Contracts: JSON Schemas for core business objects

Goal: turn domain entities into AI-friendly contracts.
Build: create schemas for 3–4 entities you’ll surface:

Order (Id, CustomerId, Lines, Total)

Customer (Id, Email, Roles, RewardPoints)

Product (Id, Name, Price, StockQuantity)

PaymentResult (Status, TransactionId)
Accept: /Schemas/*.schema.json + sample payloads in /examples/.

2) MCP Server: wrap domain services

Goal: expose nopCommerce services as tools.
Build: C# MCP server project that references Nop.Services. Tools:

GetOrderById(orderId) → Order

CalculateOrderTotal(orderId) → Money

GetOverdueCustomers(days) → Customer[]

SearchProducts(query) → Product[]
Each validates against JSON Schema.
Accept: CLI invocation returns JSON matching schema; tests cover happy/error paths.

3) SSE Gateway with OAuth

Goal: secure streaming of tool results.
Build: ASP.NET Core gateway:

GET /stream/tools/{tool} streams text/event-stream from MCP tool call.

Protect with JWT bearer auth (Azure Entra ID, or nopCommerce’s own token plugin if you want OSS only).
Accept: curl -N without token → 401; with valid token → streamed events ending in done.

4) AI-powered catalog enhancements

Goal: inject AI into the product catalog.
Build: add MCP tool GenerateProductDescription(productId) that calls LLM, produces SEO-optimized description. Optionally wire into nopCommerce’s ProductService so admins can “Generate Description” in the admin UI.
Accept: admin clicks button, description field auto-fills with AI text (editable).

5) Customer service automation

Goal: leverage MCP for customer insights.
Build: tool SummarizeCustomerHistory(customerId) that pulls orders, returns, support tickets and streams a natural-language summary via SSE.
Accept: connecting client sees streaming summary of customer’s last 5 orders.

6) Payment/Order status monitoring

Goal: show AI in ops workflows.
Build: MCP tool DetectStalledOrders(hours) that queries orders stuck in Pending/Processing beyond threshold. LLM can suggest probable causes.
Accept: running tool returns list of order IDs + summary of reasons.

7) GitHub Agentic DevOps: Issues → Branches → PRs

Goal: practice agent-assisted development.
Build:

Issue templates with DoD.

Use Copilot Agent to: “Generate tests for CalculateOrderTotal MCP tool.”

PR flow: build, test, CodeQL, secret scan.
Accept: at least 2 features delivered via PR with Copilot’s help.

8) CI/CD with GitHub Actions

Goal: deploy SSE gateway in automated flow.
Build:

ci.yml: restore, build, test, run analyzers.

deploy-dev.yml: deploy SSE gateway + MCP server container to Azure App Service/Container Apps.
Accept: merge to main redeploys; health check probe passes.

9) Observability & Policy

Goal: guardrails + visibility.
Build:

Add logging with correlation IDs (per order/customer).

Application Insights telemetry (duration, failures).

Rate limit SSE connections per client.
Accept: telemetry shows tool invocation metrics; unauthorized calls denied.

10) Stretch ideas

Personalization: MCP tool RecommendProducts(customerId) (uses order history + AI).

Moderation: AI screens customer reviews before publishing.

Pluginized MCP: deliver MCP server as a nopCommerce plugin (instead of sidecar).

Multi-tenant: if using nopCommerce multi-store, enforce per-store tool policies.

Suggested “golden path” (1-day hack)

0–1: Repo recon + schemas (Ch. 0–1)

1–3: MCP core tools (Ch. 2)

3–5: SSE + OAuth (Ch. 3)

5–6: Product description generator (Ch. 4)

6–7: Customer history summarizer (Ch. 5)

7+: CI/CD + Observability (Ch. 7–9)

Why nopCommerce works well

Rich domain: Orders, Customers, Products, Payments = great MCP surfaces.

Service-oriented: Nop.Services layer wraps business logic cleanly.

Extensible: plugin architecture = natural “injection points” for AI.

Relatable use cases: AI-powered product descriptions, customer history summaries, and order monitoring show immediate value.