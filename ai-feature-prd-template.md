# AI Feature PRD Template

## 1. Problem Statement
*What specific problem are we solving? Include user pain points and current state limitations.*

## 2. Opportunity & Impact
*Quantifiable business value, user benefit, and strategic importance. Include success metrics.*

## 3. User Stories
*Key scenarios written as "As a [user], I want to [action] so that [outcome]"*

## 4. Solution Overview
*High-level description of the AI approach and why AI is the right solution.*

## 5. Functional Requirements
*Core capabilities the feature must have, acceptance criteria, and constraints.*

## 6. User Flow
*Step-by-step interaction between user, AI agent, and system. Include happy path and key error states.*

## 7. Data Requirements
*Input data sources, quality needs, volume estimates, and privacy considerations.*

## 8. AI Agent Design
*System prompt approach, tool/API integrations needed, and context management strategy.*

## 9. Evaluation Strategy
*How we'll measure accuracy, key eval metrics, and testing approach.*

## 10. Guardrails & Limitations
*What the agent should NOT do, safety boundaries, and fallback mechanisms.*

## 11. Technical Dependencies
*APIs, services, models, and infrastructure required.*

## 12. Launch Criteria
*Minimum performance thresholds, go/no-go metrics, and rollout strategy.*

---

# Example: Loan Serviceability Calculator Agent

## 1. Problem Statement
Brokers spend 30+ minutes manually calculating serviceability for complex income scenarios (self-employed, trusts), leading to errors and lost deals.

## 2. Opportunity & Impact
- Reduce calculation time by 90% (30 min → 3 min)
- Improve accuracy from 75% to 95%
- Target: 10,000 calculations/month, $2M annual savings

## 3. User Stories
- As a broker, I want to input varied income sources conversationally so that I get accurate borrowing capacity
- As a broker, I want the agent to ask clarifying questions so that calculations use complete information

## 4. Solution Overview
Conversational AI agent that guides brokers through income collection, dynamically calls appropriate calculator APIs, and provides detailed borrowing capacity breakdowns.

## 5. Functional Requirements
- Handle 8 income types (PAYG, self-employed, trust, rental, etc.)
- Support multi-applicant scenarios
- Provide calculation explanations
- Export results to PDF

## 6. User Flow
1. Broker: "Client is self-employed earning $150k"
2. Agent: "Is this through a company, trust, or sole trader?"
3. Broker: "Trust with 2 beneficiaries"
4. Agent: Calls trust_income_api → calculates → returns capacity
5. Agent: "Based on trust income of $150k, borrowing capacity is $650k. Would you like to add other income?"

## 7. Data Requirements
- Income documents (not stored, only processed)
- Historical calculator API responses for training evals
- No PII retention, session-only processing

## 8. AI Agent Design
- Model: GPT-4 for conversation, structured data extraction
- Tools: serviceability_api, trust_calc_api, rental_calc_api
- Context: Current session only, no history retention

## 9. Evaluation Strategy
- Accuracy: 95% match with manual calculations
- Completeness: 90% of conversations gather all required data
- Speed: <3 minutes end-to-end
- Weekly eval on 100 production samples

## 10. Guardrails & Limitations
- No financial advice, only calculations
- Cannot process actual documents (security)
- Must escalate if confidence <80%
- Clear disclaimer on all outputs

## 11. Technical Dependencies
- Existing serviceability API (must handle 100 req/sec)
- New trust/rental calculator endpoints
- OpenAI API with 99.9% uptime SLA

## 12. Launch Criteria
- 95% accuracy on 500 test scenarios
- <5 second response time P95
- Fallback to manual calculator ready
- 10 broker beta test with >4.5/5 satisfaction