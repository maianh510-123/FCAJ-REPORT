---
title: "Event 2"
date: 2026-07-25
weight: 1
chapter: false
pre: " <b> 4.2. </b> "
---

# Summary Report: “FCAJ x Agentic AI Build Week: Show Up. Build. Pitch. WIN!"

### Purpose of the Event

- Hackathon: Connect the community of young engineers and technology-passionate students to realize Agentic AI ideas on Cloud infrastructure (AWS).
- Spread a Lifelong Learning mindset: Innovate mental models, overcome the fear of failure, and embrace experimentation with new technologies to prepare for an automated future.
- Share competition & project execution experience: Reflect on the journey, technical architectures, business models, and lessons learned from the winning teams at Agentic AI Build Week.

### List of Speakers

- **Mr. Joseph Marazota** – Head of Technology in Asia, Amazon Web Services
- **Mr. Nguyen Gia Hung** – Head of Solutions Architecture in Vietnam & Cambodia, Amazon Web Services
- OneTeam: Nguyen Tan Thong & team representative (KFC Agent / Conversational Ordering Project)
- Signal Scout (Dream AI): Le Tan Luc, Do Hoang Hieu, Trieu Quoc Hao, Nguyen Van Duy Khiem, Nguyen Cong Minh, Nguyen Tran Minh Quan (Multi-Agent Corporate Strategy Analysis Project)
- Plan V Team: Pham Tien Thuan Phat, Huynh Hoang Long, Le Minh Nghia, Tran Dai Vi, Nguyen An (SA Professional AI-Native Assistant Project)
- 3K Team: Huynh An Khuong, Nguyen Quoc Huy, Ngo Quang Khoi, Hoang Le Thanh Duc, Dang Nguyen Phuoc Loc, Dang Truong Hung (SHEPHERD - Computer Vision & Crowd Tracking Project)
- Six Pillar Team: Viet, Nguyen Van Linh, Nguyen, Minh Nhat, Huyen (Adaptive Workflow Engine for Anti-Money Laundering Project)

### Key Highlights

#### Inspiring Message from AWS Leadership (Mr. Joseph Marazota)
- Mental Model shift: 20 years ago software releases were measured in quarters or weeks; today with AI Agents, automated releases happen by the minute.
- Challenging traditional barriers: Young engineers must dare to experiment and not be tied down by legacy mindsets; AI and Robots (like the 1 million robots in Amazon warehouses) only deliver real value when humans direct them and build the driving data/agents.
- Hands-on learning: Advises students to participate in at least one Hackathon every year to translate theory into practice.

#### Transitioning to New Application Architecture - Microservice Architecture
**OneTeam:**

- Problem statement: Ordering food via traditional apps creates friction (app switching, account registration, complex menu navigation). McDonald's previously struggled with AI Drive-thru due to hallucinations (e.g., misinterpreting an order as 100 chicken nugget packs).
- Solution: Built a Multi-channel Conversational Ordering Agent directly on Zalo/WhatsApp featuring natural interaction and strict cart confirmation to prevent order errors.
- Architecture & Cost: Leveraged AWS Agent Core (retaining Memory), Bedrock, WAF for traffic protection, and integrated TinyFish for web scraping KFC menu data. Extremely cheap infrastructure optimization (~$0.006 USD/order, ~$88 USD/month).

**Signal Scout Team:**

- Problem statement: Enterprises lack tools to aggregate and synthesize scattered strategic information and reports from competitors.
- Solution: Built a Multi-Agent data scraping system (bypassing Login Walls via TinyFish/Apify) and utilized Bedrock/Langfuse to analyze and forecast ROI metrics when altering organizational structures.
- Optimization: Proposed transitioning entirely from 3rd-party tools to AWS Native solutions to ensure compliance and reduce costs down from $130/month to a more optimized level.

**Plan Team:**

- Problem statement: Solutions Architects (SAs) spend 2-3 days drawing architectures, calculating costs, and generating IaC files (Terraform) when customers request urgent proposals overnight.
- Solution: Built an AI Assistant that accepts natural language inputs or policy documents -> Automatically renders Diagrams on Draw.io -> Calculates pricing -> Renders Terraform files complying with internal rules and security policies.

**3K Team:**

- Problem statement: Crowd congestion at airports, events, and supermarkets.
- Solution: Streamed camera feeds via Kinesis Video Stream -> Used YOLOv8/v11 + BoT-SORT on ECS Fargate for zone-based human detection/tracking -> Combined with Bedrock Agents to analyze data and trigger automated staff dispatch alerts.

**Six Pillar Team:**

- Problem statement: 90-95% of Anti-Money Laundering (AML) alerts in banks/crypto are False Positives, costing $20-25 USD per manual check.
- Solution: 3-Layer Architecture (Kinesis -> XGBoost/Bedrock Classifier -> Multi-subagents inspecting KYC/Money Flow/Sanctions -> Double Check via LLM/Guardrails) reducing investigation time from 3 hours to a few minutes.

### Key Takeaways

#### Product & Design Thinking

- **Business-First:** No matter how complex technology gets, it cannot overcome business constraints. Identify user Pain Points accurately before jumping straight into code.
- **Scope Limitation:** Within a 24-hour Hackathon, focus solely on building an MVP (Minimum Viable Product) that solves the core workflow; avoid Scope Creep which leads to failed live demos.
- **Human-in-the-loop:** In sensitive domains (Finance, Banking, Security), AI serves as a productivity optimizer (Co-pilot); the final decision still requires human review/audit.

#### Technical Architecture

- **Multi-Agent Mindset:** Decoupling tasks across specialized Sub-agents (Crawl agent, KYC agent, Analysis agent...) orchestrated by a central Orchestrator Agent yields higher efficiency than a single massive Prompt.
- **Token & Cost Management:** Clean and sanitize data via pure code logic (Data Sanitization) before feeding it into LLMs to cut Token costs and mitigate Prompt Injection risks.
- **Handling Hallucination & Consistency:** Apply Guardrail enforcement mechanisms and utilize Double-Check mechanisms (dual LLMs or Rule-based logic) to guarantee output data consistency.

#### Soft Skills & Teamwork

- **Put Egos Aside:** Every team experiences conflicts; what matters is resolving disputes objectively, focusing on the shared goal, and avoiding personal attacks.
- **Role Assignment & Discipline:** Clarify responsibilities (AI Engineer, Data/SE, Business/Pitching) and manage time strictly when pulling overnight sprints.

### Application to Work

- **Apply Multi-Agent patterns to real-world problems:** Deconstruct complex tasks into Sub-agents integrated with AWS Bedrock/Step Functions.
- **Optimize R&D Workflows:** Use AI Agents to automate document reading/summarization, competitor data scraping, and boilerplate code generation (Terraform/IaC).
- **Embrace the "Show Up - Build - Pitch" Spirit:** Actively register for real-world Hackathons/Workshops to gain practical experience, expand your network, and enrich your Portfolio/CV.

### Event Experience

The live session **FCAJ x Agentic AI Build Week: Show Up. Build. Pitch. WIN!** brought an explosive, practical, and highly energetic atmosphere:

#### Support from the Community & Experts
- **Felt the strong connecting spirit of the AWS First Cloud AI Journey (FCAJ) community:** Senior members were always ready to offer technical support, architecture feedback, and guidance to competing teams.
- Incisive questions and feedback from the professional judging panel helped teams identify blind spots in business models and infrastructure cost optimization.

#### Authentic Hackathon Atmosphere
- "Bittersweet" real moments: Staying up until 3-4 AM, crashing on the floor or backpacks, doing push-ups to stay awake, drinking cans of RedBull, or accidentally pushing `.env` files containing API Keys to GitHub.
- Passion and resilience from young students/engineers across universities (FPT, Swinburne, etc.) as they finalized project submissions while continuously tuning live demos on stage.

#### Event Photos
<img src="/images/IMG_4205.png" alt="Sự kiện AWS Event 2" width="500px" />
<img src="/images/IMG_4206.png" alt="Sự kiện AWS Event 2" width="500px" />
<img src="/images/IMG_4209.png" alt="Sự kiện AWS Event 2" width="500px" />

> In conclusion, the event was not just a celebration of winning teams but also a powerful, inspiring "battlefield diary": Step out of your comfort zone, form a team, sign up for a Hackathon, experience overnight product building—because those memories and lessons will be the most valuable assets on your career journey.