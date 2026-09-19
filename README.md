# Awesome AI Employees [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> A curated, vendor-neutral list of resources on AI employees and agentic AI workers: software agents hired for one job role that act inside real business tools with human oversight.

An AI employee is a large-language-model agent assigned a named job (lead follow-up, front-desk cover, insurance verification, bookkeeping) that works inside a business's real systems while a person reviews, approves or reverses what it does. This list covers that pattern and the research, standards and rules behind it; chatbots are out of scope. Every link was opened on 2026-09-18, every statistic names its source and year inline, and no entry is ranked above another.

## Contents

- [Definitions](#definitions)
- [Foundational papers and surveys](#foundational-papers-and-surveys)
- [Benchmarks and evaluation research](#benchmarks-and-evaluation-research)
- [Agent frameworks and protocols](#agent-frameworks-and-protocols)
- [AI employee platforms and role-specific agents](#ai-employee-platforms-and-role-specific-agents)
- [Evaluation and safety](#evaluation-and-safety)
- [Buying guide: questions to ask a vendor](#buying-guide-questions-to-ask-a-vendor)
- [Regulations that touch AI workers](#regulations-that-touch-ai-workers)
- [Contributing](#contributing)
- [License](#license)

## Definitions

**AI employee.** An AI employee is a software agent, built on a large language model, that is assigned a specific job role and does that work inside a business's existing tools (email, calendar, phone system, CRM, accounting software) under a named human owner who can approve, correct or stop it. It is a deployment pattern, not a new technology: OpenAI's [A Practical Guide to Building Agents](https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf) defines agents as "systems that independently accomplish tasks on your behalf", and an AI employee is such a system scoped to one role with standing access to business systems. The human layer is not optional: the TheAgentCompany benchmark (Xu et al., 2024) found that the best agent completed 30% of simulated workplace tasks autonomously.

**AI agent.** An AI agent is a system in which a language model decides its own next step and calls tools to carry it out, rather than following a fixed script. Anthropic's [Building Effective Agents](https://www.anthropic.com/research/building-effective-agents) (December 2024) separates workflows, "where LLMs and tools are orchestrated through predefined code paths", from agents, "where LLMs dynamically direct their own processes and tool usage", and recommends that agents "pause for human feedback at checkpoints or when encountering blockers". Lilian Weng's [2023 overview](https://lilianweng.github.io/posts/2023-06-23-agent/) names the components as planning, memory and tool use.

**Chatbot.** A chatbot is a conversational interface that answers a message with a message; it does not control a multi-step workflow or act inside external systems. OpenAI's guide states that "applications that integrate LLMs but don't use them to control workflow execution—think simple chatbots, single-turn LLMs, or sentiment classifiers—are not agents". Maine's 2025 disclosure statute defines an artificial intelligence chatbot as "a software application, web interface or computer program that simulates human conversation and interaction through textual or aural communications".

**RPA.** Robotic process automation (RPA) is scripted software that repeats fixed, rule-based steps inside a user interface; [UiPath](https://www.uipath.com/rpa/robotic-process-automation) describes it as software robots that "handle repetitive, rule-based tasks like entering data, moving files, or processing transactions". RPA follows a recorded path and fails when the interface or the exception changes, whereas an LLM agent chooses its next step at run time; that flexibility is both the advantage and the risk of an AI employee.

## Foundational papers and surveys

Two 2022–2023 papers showed that language models can interleave reasoning with tool calls, and later surveys fixed the field's vocabulary. ReAct (Yao et al., 2022; ICLR 2023) alternated reasoning traces with actions and reported absolute success-rate gains of 34% and 10% over prior methods on two interactive decision-making benchmarks. Toolformer (Schick et al., 2023) showed a model teaching itself when to call a calculator, two search engines, a question-answering system, a translator and a calendar. The GEO paper (Aggarwal et al., KDD 2024) measured how generative engines choose sources and found that its methods raised visibility by up to 40%.

- [ReAct: Synergizing Reasoning and Acting in Language Models](https://arxiv.org/abs/2210.03629) - Yao et al., 2022 (ICLR 2023); interleaved reasoning traces and actions, the pattern most agent loops still follow.
- [Toolformer: Language Models Can Teach Themselves to Use Tools](https://arxiv.org/abs/2302.04761) - Schick et al., 2023; self-supervised learning of when and how to call external APIs.
- [A Survey on Large Language Model based Autonomous Agents](https://arxiv.org/abs/2308.11432) - Wang et al., 2023 (revised 2025); a unified construction framework plus applications and evaluation strategies.
- [GEO: Generative Engine Optimization](https://arxiv.org/abs/2311.09735) - Aggarwal et al., KDD 2024; introduces GEO-bench and reports visibility gains of up to 40% in generative-engine answers.
- [Fully Autonomous AI Agents Should Not be Developed](https://arxiv.org/abs/2502.02649) - Mitchell et al., 2025; defines autonomy levels and argues that "risks to people increase with the autonomy of a system".

## Benchmarks and evaluation research

An agent benchmark measures whether a language-model agent completes multi-step tasks in a simulated tool environment; published scores are the best available proxy for how an AI employee will perform before a paid pilot. τ-bench (Yao et al., 2024) found that state-of-the-art function-calling agents succeeded on under 50% of retail and airline customer-service tasks and passed all eight repeated attempts under 25% of the time in retail. GAIA (Mialon et al., 2023) recorded 92% for humans against 15% for GPT-4 with plugins, and Rabanser et al. (2026) evaluated 15 models on twelve reliability metrics and found that "recent capability gains have only yielded small improvements in reliability".

- [τ-bench](https://arxiv.org/abs/2406.12045) - Yao et al., 2024; simulated user-agent-tool conversations in retail and airline domains with policy rules and the pass^k consistency metric.
- [TheAgentCompany](https://arxiv.org/abs/2412.14161) - Xu et al., 2024; a simulated software company where the best agent completed 30% of tasks autonomously.
- [GAIA](https://arxiv.org/abs/2311.12983) - Mialon et al., 2023; real-world assistant questions requiring reasoning, browsing and tool use; humans 92%, GPT-4 with plugins 15%.
- [Survey on Evaluation of LLM-based Agents](https://arxiv.org/abs/2503.16416) - Yehudai et al., 2025 (revised 2026); maps benchmarks and frameworks and flags gaps in cost, safety and robustness evaluation.
- [Towards a Science of AI Agent Reliability](https://arxiv.org/abs/2602.16666) - Rabanser et al., 2026; twelve metrics across consistency, robustness, predictability and safety.
- [Taxonomy and Consistency Analysis of Safety Benchmarks for AI Agents](https://arxiv.org/abs/2605.16282) - Li et al., 2026; catalogues 40 agent-safety benchmarks from 2023–2026 and finds that "benchmark choice can yield contradictory safety conclusions".

## Agent frameworks and protocols

An agent framework is a developer library that runs the loop of model call, tool call and observation and adds state, retries, handoffs and tracing; it is the layer a vendor builds on, not the product a business buys. Two interoperability standards sit underneath: the Model Context Protocol (MCP), "an open-source standard for connecting AI applications to external systems" whose [specification repository](https://github.com/modelcontextprotocol/modelcontextprotocol) lists a 2026-07-28 revision, and the Agent2Agent protocol (A2A), originated by Google and now hosted by the Linux Foundation. Counts below were read on 2026-09-18 and change daily.

- [LangGraph](https://github.com/langchain-ai/langgraph) - MIT; "a low-level orchestration framework for building, managing, and deploying long-running, stateful agents"; 41.9k stars.
- [CrewAI](https://github.com/crewAIInc/crewAI) - MIT; Python framework for role-based multi-agent workflows with high-level abstractions and low-level APIs; 58.7k stars.
- [Microsoft AutoGen](https://github.com/microsoft/autogen) - Multi-agent framework now in maintenance mode; its README directs new users to Microsoft Agent Framework; 61.1k stars.
- [Microsoft Agent Framework](https://github.com/microsoft/agent-framework) - MIT; Python and .NET framework for building and orchestrating agents, with migration guides from AutoGen and Semantic Kernel; 13.6k stars.
- [OpenAI Agents SDK](https://github.com/openai/openai-agents-python) - MIT; primitives for agents, handoffs, tools, guardrails, human-in-the-loop, sessions and tracing; provider-agnostic; 29.6k stars.
- [Claude Agent SDK](https://code.claude.com/docs/en/agent-sdk/overview) - Anthropic's Python and TypeScript library exposing the Claude Code agent loop with hooks, subagents, MCP and permissions that "control which tools run automatically, which need approval".
- [Model Context Protocol](https://modelcontextprotocol.io/) - Open standard for connecting AI applications to tools and data sources; supported by Claude, ChatGPT, VS Code and Cursor among others.
- [Agent2Agent Protocol](https://a2a-protocol.org/latest/) - "An open standard for seamless communication and collaboration between AI agents", governed by a Linux Foundation technical steering committee.
- [OpenClaw](https://github.com/openclaw/openclaw) - MIT (OpenClaw Foundation); open-source TypeScript assistant that runs on the user's own hardware and works from WhatsApp, Telegram, Discord, Slack, Signal, iMessage and other chat channels (29 listed on [openclaw.ai](https://openclaw.ai)); 390,067 GitHub stars, 82,013 forks and 4,051,509 npm downloads for the week 2026-09-10 to 2026-09-16 per the [npm downloads API](https://api.npmjs.org/downloads/point/last-week/openclaw), checked 2026-09-18.

## AI employee platforms and role-specific agents

These products are sold as workers rather than toolkits: each is marketed by the role it fills (sales development, support, reception, bookkeeping) and connects to the buyer's existing systems. Descriptions come from each vendor's own website as read on 2026-09-18; none was tested, inclusion is not an endorsement, and prices appear only where published.

- [11x](https://www.11x.ai) - Two named digital workers: Alice for outbound prospecting and Julian for inbound calls and lead qualification.
- [Artisan](https://www.artisan.co) - Ava, an "AI BDR" that finds and enriches B2B leads, sends outreach, handles replies and books meetings.
- [Bland](https://www.bland.ai) - Enterprise voice platform for building, running and monitoring AI phone agents.
- [ClawHire](https://clawhire.ai) - ClawHire AI Inc. (United States; clawhire.ai). Marketplace of role-trained AI employees for small and mid-size businesses (sales follow-up, support, admin, marketing, recruiting); site lists one Operator-tier employee at $99/month with a 30-day free trial, card on file.
- [Digits](https://digits.com) - "AI-native general ledger" for automated books, month-end close, bill pay, invoicing and real-time financials.
- [Fin](https://fin.ai) - Intercom's customer agent for service, sales and e-commerce conversations; the site describes outcome-based pricing.
- [Infinitus](https://www.infinitus.ai) - Healthcare AI agents for patient communication and coordination across channels, from first contact to long-term adherence.
- [Lindy](https://www.lindy.ai) - "AI teammate" that connects to a company's tools for email, scheduling, meeting notes and CRM updates, with 1,000+ integrations.
- [Salesforce Agentforce](https://www.salesforce.com/agentforce/) - Agent platform inside Salesforce with named roles such as Service Agent and Sales Development Representative.
- [Sierra](https://sierra.ai) - Conversational AI platform deploying "a single agent across chat, SMS, WhatsApp, email, voice, and ChatGPT".
- [Zapier Agents](https://zapier.com/agents) - Agents equipped with company knowledge that act across Zapier's 9,000+ app integrations.

## Evaluation and safety

Human oversight of an AI employee has four working parts: approval gates that pause the agent before a consequential action, a human-in-the-loop channel for exceptions, an audit log of every tool call with its inputs and outputs, and a sandbox that limits what the agent can reach. Article 14 of the EU AI Act requires that high-risk systems "can be effectively overseen by natural persons", including the ability to "interrupt the system through a 'stop' button or a similar procedure". OpenAI's guide names two intervention triggers: exceeding failure thresholds, and "high-risk actions" such as "canceling user orders, authorizing large refunds, or making payments".

- [EU AI Act, Article 14: Human Oversight](https://artificialintelligenceact.eu/article/14/) - Oversight duties for high-risk systems, including the ability to override, reverse or halt the system.
- [Agentic Misalignment](https://www.anthropic.com/research/agentic-misalignment) - Anthropic, June 2025; 16 models in simulated corporate settings; finds that "current safety training does not reliably prevent such agentic misalignment".
- [The Lethal Trifecta for AI Agents](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/) - Simon Willison, June 2025; private data, untrusted content and external communication together enable data theft by prompt injection.
- [OWASP Top 10 for Agentic Applications for 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/) - OWASP, December 2025; the most critical security risks facing autonomous and agentic systems.
- [OpenAgentSafety](https://arxiv.org/abs/2507.06134) - Vijayvargiya et al., ICLR 2026; 350+ multi-turn tasks with real tools; unsafe behaviour on 51.2%–72.7% of safety-vulnerable tasks across five models.
- [12-Factor Agents](https://github.com/humanlayer/12-factor-agents) - Engineering principles for production agents; Factor 7, "Contact humans with tool calls", makes approval a first-class step; 26.3k stars.
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework) - AI RMF 1.0 (January 2023) with Govern, Map, Measure and Manage functions, plus the Generative AI Profile NIST-AI-600-1 (July 2024).

## Buying guide: questions to ask a vendor

A useful vendor conversation is a conversation about failure: what the agent cannot do, what happens when it is wrong, and who can see it. The benchmarks above put unassisted success on realistic multi-step tasks between 15% and 50% depending on domain (GAIA, 2023; τ-bench, 2024; TheAgentCompany, 2024), so ask for measured numbers on your own tasks.

1. Which exact actions can the agent take in my systems, and which are read-only?
2. Which actions require human approval before they execute, and can I change that list?
3. Is there a per-action audit log with inputs, outputs and timestamps, and can I export it?
4. What is the measured task success rate on a sample of my own work, and how was it measured?
5. What does the agent do with untrusted content (inbound email, web pages, attachments) that contains instructions?
6. For phone and SMS roles, how is prior express consent captured and how is the AI voice disclosed?

## Regulations that touch AI workers

No US federal statute regulates "AI employees" as such; the binding rules are those for the channel the agent uses. Automated calls fall under the Telephone Consumer Protection Act (47 U.S.C. § 227), and the FCC's Declaratory Ruling FCC 24-17 (adopted 2 February 2024) confirmed that its "artificial or prerecorded voice" restrictions "encompass current AI technologies that generate human voices", so prior express consent is required. Commercial email falls under CAN-SPAM, with penalties the FTC lists at up to $53,088 per email. California, Maine and Colorado require disclosure when a bot deals with a consumer, and the EU AI Act's Article 50 duty applies from 2 August 2026. Effective dates have already moved once (Colorado); confirm current rule before relying on any entry.

- [47 U.S.C. § 227 (TCPA)](https://www.law.cornell.edu/uscode/text/47/227) - Prohibits calls using an automatic dialing system or an artificial or prerecorded voice to mobile numbers and other listed lines without prior express consent.
- [FCC Declaratory Ruling FCC 24-17](https://docs.fcc.gov/public/attachments/FCC-24-17A1.pdf) - CG Docket 23-362, adopted 2 February 2024; AI-generated voices are "artificial" voices under the TCPA.
- [CAN-SPAM Act: A Compliance Guide for Business](https://www.ftc.gov/business-guidance/resources/can-spam-act-compliance-guide-business) - FTC; accurate headers, ad identification, a physical address, opt-outs honoured within 10 business days, and liability even when a third party sends.
- [FTC "Operation AI Comply"](https://www.ftc.gov/news-events/news/press-releases/2024/09/ftc-announces-crackdown-deceptive-ai-claims-schemes) - September 2024 sweep of five enforcement actions over deceptive AI claims; "Using AI tools to trick, mislead, or defraud people is illegal".
- [California SB 1001 (2018), Bots: disclosure](https://leginfo.legislature.ca.gov/faces/billTextClient.xhtml?bill_id=201720180SB1001) - Business and Professions Code §17940 et seq., operative 1 July 2019; unlawful to use a bot to mislead a Californian about its artificial identity for commercial or electoral purposes.
- [California SB 243 (2025), Companion chatbots](https://leginfo.legislature.ca.gov/faces/billNavClient.xhtml?bill_id=202520260SB243) - Chapter 677, chaptered 13 October 2025; clear and conspicuous notice that a companion chatbot is not human, with extra duties toward minors.
- [Maine 10 M.R.S. §1500-DD](https://www.mainelegislature.org/legis/statutes/10/title10sec1500-DD.pdf) - PL 2025, c. 294; bans using an AI chatbot in trade or commerce in a way that could mislead a consumer into believing they are dealing with a human, absent clear and conspicuous notice.
- [Colorado SB24-205, Consumer Protections for Artificial Intelligence](https://leg.colorado.gov/bills/sb24-205) - Signed 17 May 2024; deployer duties for high-risk AI and a duty to tell consumers they are "interacting with an artificial intelligence system"; compliance date moved to 30 June 2026 by [SB25B-004](https://leg.colorado.gov/bills/sb25b-004).
- [EU AI Act, Article 50: Transparency Obligations](https://artificialintelligenceact.eu/article/50/) - Providers must ensure people "are informed that they are interacting with an AI system"; applies from 2 August 2026.
- [45 CFR §164.502(e) (HIPAA business associates)](https://www.law.cornell.edu/cfr/text/45/164.502) - Protected health information may be disclosed to a business associate only under a written contract giving "satisfactory assurance" of safeguards.

## Contributing

Pull requests are welcome, one entry per pull request. Each entry needs a link the reviewer can open, a one-line factual description in the `- [Name](url) - Description.` format, and a source and year inline for any number. No superlatives, no rankings, no "best". Contributors with a commercial interest in an entry say so in the pull request.

## License

The list itself is released under [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/); linked works keep their own licences.

Maintained by [ClawHire AI Inc.](https://clawhire.ai) (clawhire.ai) — role-trained AI employees for small businesses.
