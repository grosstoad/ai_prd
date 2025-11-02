# The Product Manager's Guide to AI Evaluation & Operations

## Executive Summary
This guide provides a practical framework for product managers to evaluate and optimize AI features. It covers what to measure, when to measure it, and how to drive improvements based on evaluation results. Includes both general framework and specific examples using a Loan Serviceability Calculator Agent.

---

## 📊 The Evaluation Pyramid: Three Levels of Testing

### Level 1: Unit Testing (Daily)
**What**: Individual component validation
- Single prompt/response pairs
- Tool call accuracy
- Safety guardrails

**Example - Serviceability Agent**:
```
Input: "Client earns $120k salary"
Expected: Agent calls payg_income_api($120,000)
Actual: Agent calls payg_income_api($120,000)
Result: ✅ Pass
```

**Time**: 15-30 min/day reviewing automated reports

### Level 2: Integration Testing (Weekly)
**What**: End-to-end workflow validation
- Multi-step conversations
- API integration reliability
- Context retention

**Example - Serviceability Agent**:
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

**Time**: 2-3 hours/week deep dive

### Level 3: System Testing (Monthly)
**What**: Full production assessment
- User satisfaction metrics
- Business KPI impact
- Cost/performance optimization
**Time**: 4-6 hours/month comprehensive review

---

## 🔄 The Weekly Evaluation Rhythm

### Monday: Gather (30 min)
- Pull production logs from previous week
- Collect user feedback tickets
- Review error reports
- Note any prompt/model changes

**Example Data Pull - Serviceability Agent**:
```sql
SELECT conversation_id, broker_id, income_types,
       calculation_result, accuracy_flag, duration
FROM serviceability_logs
WHERE date >= '2024-10-21'
AND accuracy_flag = 'MISMATCH'
```

### Wednesday: Analyze (2 hours)
- Run regression tests on top issues
- Identify patterns in failures
- Score sample conversations
- Review cost metrics

### Friday: Improve (1 hour)
- Update prompt engineering
- Adjust guardrails
- Create new test cases
- Document learnings

**Total Weekly Time**: ~3.5 hours (7% of your week)

---

## 📈 Types of Evaluations & When to Use Them

### 1. **Regression Testing**
**When**: Before every release
**What**: Ensure new changes don't break existing functionality
**How**:
- Maintain 50-100 "golden" test cases
- Run automatically in CI/CD
- Block deployment if >5% degradation

### 2. **A/B Testing**
**When**: Major prompt or model changes
**What**: Compare performance of variants
**How**:
- 10% traffic to variant B
- Run for minimum 1000 interactions
- Statistical significance before rollout

### 3. **User Feedback Analysis**
**When**: Continuous
**What**: Real-world performance indicators
**How**:
- Thumbs up/down on responses
- Session abandonment rates
- Support ticket analysis

### 4. **Safety & Compliance Audits**
**When**: Monthly + after incidents
**What**: Ensure responsible AI use
**How**:
- Adversarial testing
- Bias detection
- Compliance checklist review

### 5. **Performance Benchmarking**
**When**: Weekly
**What**: Speed, cost, and reliability metrics
**How**:
- P50/P95 latency tracking
- Token usage analysis
- API failure rates

---

## 🎯 Key Metrics & Target Thresholds

### Quality Metrics
| Metric | Target | Red Flag |
|--------|--------|----------|
| Accuracy Rate | >85% | <70% |
| User Satisfaction | >4.0/5 | <3.5/5 |
| Task Completion | >80% | <60% |
| Hallucination Rate | <5% | >10% |

### Operational Metrics
| Metric | Target | Red Flag |
|--------|--------|----------|
| Response Time (P95) | <3s | >5s |
| Cost per Request | <$0.05 | >$0.10 |
| Error Rate | <2% | >5% |
| Availability | >99.5% | <99% |

### Business Metrics
| Metric | Target | Red Flag |
|--------|--------|----------|
| Automation Rate | >70% | <50% |
| Human Escalation | <20% | >35% |
| ROI | >3x | <1.5x |
| Adoption Rate | >60% | <30% |

---

## 🛠️ The Evaluation Toolkit

### Essential Tools
1. **Logging**: Every request, response, and decision
2. **Analytics**: Dashboards for real-time monitoring
3. **Test Harness**: Automated evaluation runner
4. **Feedback Widget**: In-product user ratings
5. **Cost Tracker**: Token and API usage monitor

### The "Golden Dataset"
Your most valuable evaluation asset:
- **Size**: 100-200 examples
- **Composition**:
  - 40% common cases
  - 30% edge cases
  - 20% adversarial inputs
  - 10% regression catches
- **Update**: Add 5-10 new cases weekly from production issues

---

## 🔧 From Evaluation to Improvement

### The Action Matrix

| Finding | Action | Timeline |
|---------|--------|----------|
| Consistent misunderstanding | Refine prompt | Same day |
| Tool selection errors | Update tool descriptions | Next sprint |
| Slow responses | Optimize context/caching | This week |
| Knowledge gaps | Expand training data | Next month |
| Safety violations | Immediate guardrail update | ASAP |

### Improvement Playbook

#### Quick Wins (<1 hour)
- Add examples to prompt
- Adjust temperature settings
- Update error messages
- Add clarifying instructions

#### Medium Efforts (1-5 hours)
- Redesign conversation flow
- Create new tools/APIs
- Implement caching layer
- Build specialized evaluators

#### Major Initiatives (>1 week)
- Fine-tune custom model
- Rebuild retrieval system
- Change base model
- Redesign architecture

---

## 📅 Monthly Evaluation Sprint

### Week 1: Baseline
- Run comprehensive test suite
- Document current performance
- Identify top 3 issues

### Week 2: Experiment
- A/B test improvements
- Gather additional data
- Prototype solutions

### Week 3: Implement
- Deploy winning variants
- Update documentation
- Retrain if needed

### Week 4: Monitor
- Track improvement metrics
- Gather user feedback
- Plan next cycle

---

## ⚡ The 80/20 Rule for AI Evals

**80% of improvements come from:**
1. Better prompts (not better models)
2. Fixing the top 5 failure modes
3. Clear tool descriptions
4. Good error handling
5. User feedback loops

**Don't waste time on:**
- Perfect edge case handling
- Premature fine-tuning
- Complex orchestration
- Over-engineering metrics
- Theoretical problems

---

## 🚨 Red Flags That Demand Immediate Action

1. **Hallucination spike** (>15% increase)
2. **Cost explosion** (>2x normal)
3. **Safety violation** (any occurrence)
4. **Data leak** (PII in logs)
5. **Cascading failures** (>10% error rate)

**Response Protocol:**
1. Roll back recent changes
2. Implement emergency fix
3. Add regression test
4. Post-mortem within 48 hours

---

## 💡 PM Time Allocation Guide

### Daily (15 min)
- Check automated eval dashboard
- Review any critical alerts
- Note unusual patterns

### Weekly (3-4 hours)
- Deep dive on metrics
- Run manual spot checks
- Update test cases
- Team sync on findings

### Sprint (8-10 hours)
- Comprehensive evaluation
- Prioritize improvements
- Update roadmap
- Stakeholder reporting

### Quarterly (2-3 days)
- Full system audit
- Competitive benchmarking
- Strategy adjustment
- Budget review

**Total: ~15% of your time on evaluation/optimization**

---

## 📋 Evaluation Checklist for PRDs

When writing your PRD, ensure you've defined:

- [ ] Success metrics and thresholds
- [ ] Evaluation frequency and owner
- [ ] Initial test cases (minimum 20)
- [ ] Feedback collection method
- [ ] Cost monitoring approach
- [ ] Safety evaluation criteria
- [ ] Regression test requirements
- [ ] A/B testing strategy
- [ ] Performance benchmarks
- [ ] Improvement cycle timeline

---

## 🎯 Quick Start: Your First Week

### Day 1: Setup
- Implement basic logging
- Create evaluation spreadsheet
- Define 3 key metrics

### Day 2-3: Baseline
- Manually test 20 scenarios
- Document current performance
- Identify obvious issues

### Day 4: Improve
- Fix top 2 issues
- Update prompts
- Add error handling

### Day 5: Measure
- Re-run tests
- Compare results
- Document improvements

**Expected outcome**: 20-30% performance improvement in first week

---

## Key Takeaway

**Evaluation is not a phase—it's a continuous practice.** The best AI products are built by teams that evaluate obsessively, improve rapidly, and never assume their AI is "done." Dedicate 15% of your time to evaluation, and you'll build AI features that actually work in the real world.

Remember: Ship fast, measure everything, improve continuously.