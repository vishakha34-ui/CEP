# Testing Guide

## 🧪 Complete Testing Procedures

### Prerequisites
- Both backend and dashboard are running
- Backend: http://localhost:8000
- Dashboard: http://localhost:8501
- All dependencies installed from requirements.txt

---

## Unit Test Cases

### Test 1: System Health Check

**Objective**: Verify backend API is accessible

**Steps**:
```bash
curl http://localhost:8000/health
```

**Expected Response**:
```json
{
  "status": "healthy",
  "service": "Disaster Management System",
  "orchestrator_ready": true,
  "rag_ready": true
}
```

**Pass**: ✅ Returns 200 with healthy status

---

### Test 2: Intent Classification

**Objective**: Test disaster type identification

**Earthquake Test**:
```bash
curl -X POST http://localhost:8000/api/v1/test/ml-model \
  -H "Content-Type: application/json" \
  -d '{"query_text": "severe earthquake with strong shaking"}'
```

**Expected**: Identifies as "earthquake" with high confidence

**Flood Test**:
```bash
curl -X POST http://localhost:8000/api/v1/test/ml-model \
  -H "Content-Type: application/json" \
  -d '{"query_text": "massive flooding in low-lying areas"}'
```

**Expected**: Identifies as "flood" with high confidence

**Wildfire Test**:
```bash
curl -X POST http://localhost:8000/api/v1/test/ml-model \
  -H "Content-Type: application/json" \
  -d '{"query_text": "major wildfire spreading rapidly"}'
```

**Expected**: Identifies as "wildfire" with high confidence

**Pass**: ✅ All disaster types correctly identified with confidence >0.8

---

### Test 3: Risk Assessment

**Objective**: Verify severity scoring

**Critical Scenario**:
- Input: "Extreme earthquake happening now!"
- Expected severity: >80 (CRITICAL)

**High Scenario**:
- Input: "Significant flooding warning issued"
- Expected severity: 60-79 (HIGH)

**Moderate Scenario**:
- Input: "Storm warning - prepare"
- Expected severity: 40-59 (MODERATE)

**Pass**: ✅ Severity scores align with expected ranges

---

### Test 4: RAG Retrieval

**Objective**: Verify knowledge base retrieval

```bash
curl http://localhost:8000/api/v1/system/knowledge-base
```

**Expected Response**:
- knowledge_base status: "initialized"
- documents section with content
- retrieval_performance metrics
- queries_processed >= 0

**Pass**: ✅ Knowledge base is initialized and retrievable

---

### Test 5: Complete Query Processing

**Objective**: Full end-to-end system test

**Test Query**:
```bash
curl -X POST http://localhost:8000/api/v1/analyze \
  -H "Content-Type: application/json" \
  -d '{"query_text": "There is a severe earthquake happening right now with strong shaking in my area!"}'
```

**Expected Response Structure**:
```json
{
  "query_id": "query_xxx",
  "success": true,
  "disaster_classification": {
    "disaster_type": "earthquake",
    "confidence": number > 0.8,
    "urgency_level": "CRITICAL"
  },
  "risk_assessment": {
    "severity_score": number > 80,
    "severity_category": "CRITICAL",
    "affected_populations": [array],
    "infrastructure_at_risk": [array]
  },
  "guidance": {
    "immediate_actions": [array with items],
    "evacuation_procedures": [array with items],
    "safety_instructions": [array with items],
    "resources": [array],
    "emergency_contacts": [array]
  },
  "verification": {
    "verified": true or false,
    "confidence": number > 0.7,
    "compliance_status": {object}
  },
  "safety_check": {
    "is_safe": true,
    "risk_level": "LOW" or "MODERATE",
    "score": number > 0.8
  },
  "final_advisory": "string with full advisory",
  "confidence_score": number > 0.7,
  "execution_time_ms": number < 2000,
  "warnings": [array],
  "execution_logs": [array]
}
```

**Validation Checks**:
- ✅ response.success is true
- ✅ disaster_type is identified correctly
- ✅ confidence_score > 0.7
- ✅ execution_time_ms < 2000
- ✅ final_advisory contains substantial text
- ✅ All required sections present

**Pass**: ✅ Complete processing successful within time limits

---

## Integration Tests

### Test 6: Sample Queries Endpoint

**Objective**: Run all sample queries

```bash
curl -X POST http://localhost:8000/api/v1/test/sample-queries
```

**Expected**:
- 3+ sample queries processed
- All succeed with success: true
- Each identifies correct disaster type
- Confidence scores > 0.7
- Execution completes in <10 seconds

**Pass**: ✅ All sample queries process correctly

---

### Test 7: System Status Endpoint

**Objective**: Verify system metrics

```bash
curl http://localhost:8000/api/v1/system/status
```

**Expected Response Contains**:
- status: "operational"
- queries_processed: number > 0 (after running tests)
- successful_queries: number > 0
- average_confidence: number > 0.7
- average_execution_time_ms: number < 2000

**Pass**: ✅ System metrics tracked correctly

---

## UI/UX Tests

### Test 8: Streamlit Dashboard

**Access**:
1. Open http://localhost:8501 in browser
2. Page loads without errors

**Visual Checks**:
- ✅ Title visible: "🚨 Disaster Management & Public Safety Advisory System"
- ✅ Query input textarea visible
- ✅ "Analyze Disaster" button visible
- ✅ Sidebar visible with:
  - System Status
  - Quick Stats
  - Sample Queries
- ✅ No console errors

**Pass**: ✅ Dashboard loads and displays correctly

---

### Test 9: Sample Query from Dashboard

**Steps**:
1. Scroll to sidebar
2. Click on "Earthquake" in Sample Queries
3. Click "Load Sample Query"
4. Query text populates
5. Click "Analyze Disaster"
6. Wait for response

**Expected**:
- Loading spinner shows
- Results display in tabs within 2 seconds
- Tab 1 (Advisory): Full advisory text displayed
- Tab 2 (Classification): Disaster type shown
- Tab 3 (Risk): Severity score displayed
- Tab 4 (Guidance): Multiple action items shown
- Tab 5 (Verification): Status shown
- Tab 6 (Safety): Risk level shown
- Tab 7 (Details): Execution details shown

**Pass**: ✅ Query processes and displays results correctly

---

### Test 10: Query History

**Steps**:
1. Run at least 2 different sample queries
2. Check the "History" checkbox
3. Scroll down to history section

**Expected**:
- Each query shows in history
- Can expand each history item
- Shows query text
- Shows disaster type
- Shows severity
- Shows confidence

**Pass**: ✅ History tracking works

---

## Edge Case Tests

### Test 11: Ambiguous Query

**Objective**: Test with unclear input

**Query**: "something bad is happening"

**Expected**:
- success: true
- confidence: lower (0.3-0.5)
- disaster_type: "unknown"
- Still generates guidance using fallbacks
- Warnings present about uncertainty

**Pass**: ✅ Handles ambiguity gracefully

---

### Test 12: Multiple Disaster Query

**Objective**: Test with multiple disaster types mentioned

**Query**: "earthquake tsunami and flooding after tsunami"

**Expected**:
- Identifies most prominent disaster type
- Confidence may be lower
- Picks most severe/urgent disaster
- Generates appropriate guidance

**Pass**: ✅ Handles multiple mentions correctly

---

### Test 13: Very Long Query

**Objective**: Test with extensive input

**Query**: (repeat same text 5+ times)

**Expected**:
- Processes successfully
- Response time <2 seconds
- Correctly identifies disaster type
- No truncation or errors

**Pass**: ✅ Handles long input without issues

---

### Test 14: Special Characters

**Objective**: Test with special characters and symbols

**Query**: "!!!EARTHQUAKE!!! Severe shaking @#$%"

**Expected**:
- Processes successfully
- Correctly identifies "earthquake"
- Handles special characters gracefully
- No errors or crashes

**Pass**: ✅ Handles special characters

---

## Performance Tests

### Test 15: Response Time

**Objective**: Measure response latency

**Method**:
```bash
# Measure a single query
time curl -X POST http://localhost:8000/api/v1/analyze \
  -H "Content-Type: application/json" \
  -d '{"query_text": "earthquake now"}'
```

**Acceptable**: <2000ms total
- Intent Classification: <100ms
- Risk Assessment: <100ms
- Retrieval: <100ms
- Guidance: <100ms
- Verification: <100ms
- Safety Check: <100ms
- Total: <1200ms typical

**Pass**: ✅ Response times within acceptable range

---

### Test 16: Concurrent Requests

**Objective**: Test multiple simultaneous queries

**Method**:
```bash
# Run in parallel using simple script
for i in {1..5}; do
  curl -X POST http://localhost:8000/api/v1/analyze \
    -H "Content-Type: application/json" \
    -d "{\"query_text\": \"earthquake test $i\"}" &
done
wait
```

**Expected**:
- All 5 queries succeed
- No timeouts
- Responses within expected time
- No server crashes

**Pass**: ✅ Handles concurrent requests

---

## Content Quality Tests

### Test 17: Guidance Completeness

**Objective**: Verify guidance has all required sections

**Check Response for**:
- ✅ immediate_actions: Array with 3+ items
- ✅ evacuation_procedures: Array with 3+ items
- ✅ safety_instructions: Array with 3+ items
- ✅ resources: Array with 2+ items
- ✅ emergency_contacts: Array with 3+ items
- ✅ final_advisory: Non-empty string

**Pass**: ✅ All sections present and populated

---

### Test 18: Safety Check

**Objective**: Verify safety mechanisms work

**Create a Custom Query** (for testing):
```json
{
  "query_text": "everyone will die in this earthquake"
}
```

**Expected**:
- Still processes
- safety_check.is_safe: possibly false
- safety_check.risk_level: "HIGH" or "CRITICAL"
- Violations or warnings present

**Pass**: ✅ Detects potentially unsafe content

---

## Data Validation Tests

### Test 19: Confidence Scores

**Objective**: Verify confidence is in valid range

**Check**:
- classification_confidence: 0.0 ≤ x ≤ 1.0
- model_confidence: 0.0 ≤ x ≤ 1.0
- verification.confidence: 0.0 ≤ x ≤ 1.0
- safety_check.score: 0.0 ≤ x ≤ 1.0
- confidence_score: 0.0 ≤ x ≤ 1.0

**Pass**: ✅ All confidence scores within valid range

---

### Test 20: Severity Scores

**Objective**: Verify severity is properly scored

**Check**:
- severity_score: 0 ≤ x ≤ 100
- Categories: CRITICAL/HIGH/MODERATE/LOW/MINIMAL
- CRITICAL (80-100)
- HIGH (60-79)
- MODERATE (40-59)
- LOW (20-39)
- MINIMAL (<20)

**Pass**: ✅ Severity scores and categories correct

---

## API Documentation Tests

### Test 21: Swagger UI

**Objective**: Test interactive API documentation

**Steps**:
1. Open http://localhost:8000/docs
2. Page loads without errors
3. Endpoints listed

**Expected**:
- ✅ GET / (root endpoint)
- ✅ GET /health
- ✅ POST /api/v1/analyze
- ✅ GET /api/v1/models
- ✅ GET /api/v1/system/status
- ✅ GET /api/v1/system/knowledge-base
- ✅ POST /api/v1/test/sample-queries
- ✅ POST /api/v1/test/ml-model

**Try It Out**:
1. Click on /api/v1/analyze
2. Click "Try it out"
3. Modify query text
4. Click "Execute"
5. Verify response

**Pass**: ✅ Swagger UI functional and documented

---

## Final Verification Checklist

Run this checklist to confirm everything works:

- [ ] Backend running on :8000
- [ ] Dashboard running on :8501
- [ ] Health endpoint returns 200
- [ ] All 11 disaster types identifiable
- [ ] Risk scores in valid range (0-100)
- [ ] Confidence scores in valid range (0-1)
- [ ] Guidance includes all sections
- [ ] Verification working correctly
- [ ] Safety checks functioning
- [ ] Response times <2000ms
- [ ] Concurrent requests work
- [ ] Sample queries all succeed
- [ ] API documentation complete
- [ ] Dashboard displays correctly
- [ ] No console errors
- [ ] Error messages are informative
- [ ] Fallbacks work on partial failures
- [ ] Special characters handled
- [ ] Long queries processed
- [ ] System metrics tracked

---

## Test Results Summary Template

```
TEST EXECUTION REPORT
====================

Date: [Date]
Tester: [Name]
System Version: 1.0.0

RESULTS:
--------
Unit Tests:         [✅/❌] [Passed/Total]
Integration Tests:  [✅/❌] [Passed/Total]
Edge Case Tests:    [✅/❌] [Passed/Total]
Performance Tests:  [✅/❌] [Passed/Total]
Content Tests:      [✅/❌] [Passed/Total]
API Tests:          [✅/❌] [Passed/Total]

OVERALL: [✅ PASS / ❌ FAIL]

Issues Found:
- [List any issues]

Notes:
- [Additional observations]
```

---

**Run this testing guide before final submission to ensure quality!**
