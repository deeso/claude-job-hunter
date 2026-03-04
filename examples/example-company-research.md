# Company Research: Acme AI

*Research completed March 2026*

## Company Basics
- **Name:** Acme AI
- **Product:** AI gateway / unified API for LLM providers
- **Model:** Usage-based pricing, developer-focused API
- **Stage:** Startup, growing rapidly in AI infrastructure space
- **Focus:** Multi-provider LLM routing, unified API interface

## Product & Business Model
- Unified API that routes to multiple LLM providers (OpenAI, Anthropic, Google, Meta, etc.)
- Developers integrate once, access all models
- Usage-based billing (pay per token)
- Key value: provider abstraction, fallback routing, cost optimization
- Developer-first product with API documentation focus

## Technical Infrastructure
- API gateway handling high-volume LLM inference requests
- Multi-provider integration layer
- Edge compute for low-latency routing
- Real-time usage tracking and billing
- Rate limiting and API key management

## Security Context
- **Hiring first security engineer** — no existing security team
- Handles sensitive data: API keys, prompts, model outputs
- Trust layer between customers and model providers
- Compliance needs driven by enterprise customer acquisition
- No known SOC2/ISO certifications yet (hence the hire)
- API security is critical: key management, rate limiting, abuse prevention

## Security Challenges (Inferred)
- API key custody and rotation
- Prompt injection and data leakage across provider boundaries
- Usage-based billing abuse (fake requests, stolen keys)
- Multi-tenant isolation
- Compliance requirements from enterprise buyers (SOC2, HIPAA for healthcare AI)
- Model provider trust boundaries
- Data residency and privacy (GDPR for EU customers)

## Culture Signals
- Startup environment, small team
- Developer-focused culture
- Remote-friendly (US)
- Fast-moving, ship-oriented
- AI-native team

## Why Security Matters for Acme AI
1. **Trust layer:** Enterprise customers trust the platform with their API keys and data
2. **Multi-provider risk:** Security posture depends on weakest provider integration
3. **Abuse prevention:** Usage-based billing makes abuse detection existential
4. **Enterprise sales:** SOC2/ISO required to close enterprise deals
5. **Developer experience:** Security can't slow down the API or DX
