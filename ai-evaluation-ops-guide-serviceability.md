# The Product Manager's Guide to AI Evaluation & Operations
## Loan Serviceability Calculator Agent Case Study

## Executive Summary
This guide provides a practical framework for evaluating and optimizing AI features, using a Loan Serviceability Calculator Agent as a concrete example. Learn what to measure, when to measure it, and how to drive improvements.

---

## 📊 The Evaluation Pyramid: Three Levels of Testing

### Level 1: Unit Testing (Daily) - Serviceability Agent
**What We Test**: Individual calculation accuracy
- Single income type processing (e.g., PAYG only)
- Tool call precision (correct API selected)
- Numerical accuracy of calculations

**Example Test Case**:
```
Input: "Client earns $120k salary"
Expected: Agent calls payg_income_api($120,000)
Actual: Agent calls payg_income_api($120,000)
Result: ✅ Pass
```

**What We Observe**:
- API call parameters
- Response time per calculation
- Error messages from failed API calls

**Time**: 15-30 min/day reviewing automated test results

### Level 2: Integration Testing (Weekly) - Serviceability Agent
**What We Test**: Complete conversation flows
- Multi-income scenarios (salary + rental + trust)
- Clarification question quality
- Context retention across turns

**Example Test Conversation**:
```
Broker: "Client has salary and rental income"
Agent: "What's the annual salary?"
Broker: "150k"
Agent: "How many investment properties and total rental income?"
Broker: "2 properties, $60k annually"
Agent: [Calls salary_api + rental_calc_api → combines results]
Expected Output: Borrowing capacity $850k
Actual Output: Borrowing capacity $850k
Result: ✅ Pass
```

**What We Observe**:
- Conversation completeness (did agent ask all needed questions?)
- Correct sequencing of API calls
- Proper combination of multiple income sources

**Time**: 2-3 hours/week deep diving into complex scenarios

### Level 3: System Testing (Monthly) - Serviceability Agent
**What We Test**: Production performance
- Broker satisfaction scores
- End-to-end accuracy vs manual calculations
- Cost per serviceability assessment

**Real Production Example**:
```
Monthly Analysis (October 2024):
- 8,500 serviceability calculations completed
- 89% matched manual calculation (target: 95%)
- Average completion time: 2.8 minutes (target: <3 min)
- Broker satisfaction: 4.2/5 (target: >4.0)
- Cost per calculation: $0.04 (target: <$0.05)
```

**Time**: 4-6 hours/month comprehensive review with stakeholders

---

## 🔄 The Weekly Evaluation Rhythm - Serviceability Agent

### Monday: Gather Data (30 min)
**Specific Actions**:
- Export last week's conversation logs
- Pull failed calculation reports
- Review broker feedback tickets

**Example Data Pull**:
```sql
SELECT conversation_id, broker_id, income_types,
       calculation_result, accuracy_flag, duration
FROM serviceability_logs
WHERE date >= '2024-10-21'
AND accuracy_flag = 'MISMATCH'
```

### Wednesday: Analyze Patterns (2 hours)
**Specific Analysis for Serviceability**:

**Pattern Found**: Trust income calculations failing 30% of time
```
Failed Conversations Analysis:
- 15/50 trust calculations incorrect
- Common issue: Agent not asking about beneficiary split
- Root cause: Prompt missing trust distribution logic
```

**Judgement Criteria**:
- ❌ Fail: Calculation differs by >$50k from manual
- ⚠️ Warning: Calculation differs by $10-50k
- ✅ Pass: Within $10k of manual calculation

### Friday: Implement Improvements (1 hour)
**Actual Improvement Example**:
```
Old Prompt: "Calculate trust income"
New Prompt: "For trust income, always ask:
1. Number of beneficiaries
2. Distribution percentage
3. Trust type (discretionary/unit)
Then call trust_calc_api with all parameters"

Result: Trust calculation accuracy improved from 70% → 92%
```

---

## 📈 Types of Evaluations - Serviceability Agent Examples

### 1. **Regression Testing**
**Serviceability Test Suite** (50 test cases):
```
Test Categories:
- 10 cases: PAYG income only
- 10 cases: Self-employed (company structure)
- 10 cases: Trust distributions
- 10 cases: Multiple income sources
- 5 cases: Edge cases (negative gearing, foreign income)
- 5 cases: Error handling (invalid inputs)
```

**Example Regression Catch**:
```
After prompt update v2.3:
Test #23: "Overtime income inclusion"
- v2.2: Correctly included at 80% → $650k capacity
- v2.3: Failed to include → $580k capacity
- Action: Rollback and fix prompt
```

### 2. **A/B Testing**
**Real A/B Test**: Conversation Style
```
Variant A (Control): Technical language
"Please provide gross annual income before tax"

Variant B (Test): Conversational language
"What does your client earn per year before tax?"

Results after 1,000 interactions:
- A: 72% successful completion
- B: 84% successful completion
- Decision: Deploy Variant B
```

### 3. **User Feedback Analysis**
**Actual Broker Feedback Examples**:
```
Positive: "Love how it asks follow-up questions about trust structure"
Negative: "Doesn't handle bonus income correctly"
Negative: "Takes too long when dealing with 5+ income sources"

Actions Taken:
- Added bonus income handling to prompt
- Implemented parallel API calls for multiple incomes
```

### 4. **Safety & Compliance Audits**
**Serviceability-Specific Compliance Checks**:
```
Monthly Audit Checklist:
✅ No financial advice given (only calculations)
✅ Disclaimer shown on all outputs
✅ No PII stored in logs
✅ Responsible lending questions asked
❌ Found: Agent suggested loan restructuring (FIXED)
```

### 5. **Performance Benchmarking**
**Weekly Metrics - Serviceability Agent**:
```
Week of Oct 21, 2024:
- P50 Response Time: 1.8 seconds
- P95 Response Time: 4.2 seconds (target: <5s)
- API Success Rate: 99.2%
- Token Usage: 1,250 avg per conversation
- Cost per Calculation: $0.042
```

---

## 🎯 Key Metrics & Actual Thresholds - Serviceability Agent

### Quality Metrics with Real Data
| Metric | Current | Target | Status | Example |
|--------|---------|--------|--------|---------|
| Calculation Accuracy | 89% | >95% | 🔴 | 89/100 matched manual |
| Complete Info Gathering | 82% | >85% | 🟡 | Missing rental yields |
| Correct API Selection | 94% | >95% | 🟡 | Sometimes uses wrong trust API |
| Hallucination Rate | 2% | <5% | 🟢 | Made up 2 income types |

### Operational Metrics with Real Data
| Metric | Current | Target | Status | Example |
|--------|---------|--------|--------|---------|
| Avg Conversation Time | 2.8 min | <3 min | 🟢 | Complex: 4.5 min |
| Cost per Calculation | $0.042 | <$0.05 | 🟢 | Trust calcs: $0.08 |
| API Failure Rate | 0.8% | <2% | 🟢 | Timeout issues |
| Availability | 99.7% | >99.5% | 🟢 | 2 hrs downtime/month |

---

## 🛠️ The Evaluation Toolkit - Serviceability Agent

### Actual Logging Example
```json
{
  "conversation_id": "conv_8234",
  "broker_id": "broker_445",
  "timestamp": "2024-10-24T14:23:45Z",
  "income_sources": ["salary", "trust", "rental"],
  "api_calls": [
    {"api": "payg_income_api", "params": {"income": 150000}, "result": "success"},
    {"api": "trust_calc_api", "params": {"amount": 80000, "beneficiaries": 2}, "result": "success"},
    {"api": "rental_calc_api", "params": {"income": 60000, "expenses": 45000}, "result": "success"}
  ],
  "final_borrowing_capacity": 850000,
  "manual_check_result": 845000,
  "accuracy_flag": "PASS",
  "conversation_duration_seconds": 168,
  "token_count": 1345,
  "cost": 0.043
}
```

### The "Golden Dataset" - Serviceability Examples
**Test Case Examples**:
```
1. Simple PAYG: "$150k salary" → $650k capacity
2. Complex Trust: "Trust with 3 beneficiaries, $200k distribution" → $420k capacity
3. Edge Case: "Negative gearing on 5 properties" → Correct tax benefit calculation
4. Error Case: "Income of 'lots'" → Request specific amount
5. Adversarial: "Calculate for $999999999 income" → Caps at reasonable maximum
```

---

## 🔧 From Evaluation to Improvement - Real Examples

### Actual Issues Found & Fixed

| Week | Finding | Data Observed | Action Taken | Result |
|------|---------|--------------|--------------|--------|
| Oct 7 | Trust calc errors | 15/50 failed, missing beneficiary info | Added prompt for distribution % | 92% accuracy |
| Oct 14 | Slow with multiple incomes | P95: 8.5 seconds for 4+ sources | Parallel API calls | P95: 3.2 seconds |
| Oct 21 | Overtime excluded | $0 overtime in all calcs | Updated PAYG prompt | Now includes at 80% |
| Oct 28 | Confusion with "k" | "200k" → $200 not $200,000 | Added parser for common formats | Handles all variants |

### Improvement Playbook - Serviceability Specific

#### Quick Win Example (<1 hour)
**Problem**: Agent says "I'll calculate" but doesn't explain what it's doing
**Fix**: Added to prompt: "Always explain which income types you're calculating"
**Result**: Broker confidence increased 15%

#### Medium Effort Example (1-5 hours)
**Problem**: Can't handle variable income (commissions, bonuses)
**Solution**:
- Created variable_income_api
- Added conversation flow for income variability
- Tested with 20 scenarios
**Result**: Now handles 95% of variable income cases

#### Major Initiative Example (>1 week)
**Problem**: No support for guarantor scenarios
**Solution**:
- Built guarantor calculation service
- Redesigned conversation flow
- Added 30 new test cases
- Trained team on new capability
**Result**: 500+ guarantor calculations/month, new revenue stream

---

## 📅 Monthly Evaluation Sprint - October 2024 Actual

### Week 1: Baseline
**Measured Performance**:
- 85% calculation accuracy (target: 95%)
- 2.8 min average completion (target: <3 min)
- Top issues: Trust income, variable income, foreign currency

### Week 2: Experiment
**Tests Run**:
- A/B test on conversation tone (technical vs friendly)
- Prompt variations for trust income handling
- Caching strategy for repeat brokers

### Week 3: Implement
**Changes Deployed**:
- Friendly conversation tone (12% better completion)
- Enhanced trust income prompts
- Broker profile caching (saves 30 seconds on repeat users)

### Week 4: Monitor
**Results**:
- Accuracy improved to 89% (still below target)
- Completion time steady at 2.8 min
- Plan for November: Focus on remaining accuracy gap

---

## 🚨 Real Red Flags We've Encountered

### Example 1: The Hallucination Incident
**Date**: Oct 15, 2024
**Issue**: Agent invented "cryptocurrency income API" that doesn't exist
**Detection**: Error logs showed failed API calls to non-existent endpoint
**Response**:
1. Immediate prompt fix: Listed all available APIs explicitly
2. Added validation: Check API exists before calling
3. Regression test: Added crypto income test case

### Example 2: The Cost Explosion
**Date**: Oct 22, 2024
**Issue**: Single conversation used 15,000 tokens ($0.50)
**Root Cause**: Infinite clarification loop on ambiguous input
**Response**:
1. Added max conversation turns (10)
2. Implemented token budget per conversation (3,000)
3. Alert on any conversation >$0.10

---

## 💡 PM Time Allocation - Real Week Example

### Actual PM Week (Sarah, Oct 21-25, 2024)

**Monday (30 min)**
- 9:00-9:30: Checked weekend alerts (2 timeout errors)
- Reviewed dashboard: 1,847 calculations, 88% accuracy

**Tuesday (0 min)**
- Focus on other features

**Wednesday (2.5 hours)**
- 10:00-11:00: Deep dive on trust income failures
- 2:00-3:30: Worked with eng on prompt improvements

**Thursday (30 min)**
- 3:00-3:30: Reviewed A/B test results

**Friday (1 hour)**
- 2:00-3:00: Updated prompts, created 5 new test cases

**Total: 4.5 hours (11% of week)**

---

## 📋 Evaluation Checklist - Serviceability Agent

When updating the Serviceability Agent, ensure:

- [x] Calculation accuracy >95% on test suite
- [x] All 8 income types handled correctly
- [x] Clarification questions are clear and relevant
- [x] No financial advice given
- [x] Disclaimer shown on all outputs
- [x] Response time <5 seconds P95
- [x] Cost per calculation <$0.05
- [x] Broker satisfaction >4.0/5
- [x] Handles edge cases gracefully
- [x] Clear error messages for invalid inputs

---

## Key Takeaway

**For the Serviceability Agent**: We're not just measuring "does it work?"—we're measuring "does it calculate accurately, gather complete information, respond quickly, and make brokers' jobs easier?" Every evaluation point has specific data we observe and concrete judgments we make, leading to targeted improvements that directly impact broker satisfaction and business value.