# QA Notes

**Project:** Visitor Check-in System  
**Document:** QA Summary & Notes  
**Prepared By:** Bhim Kumar Sah  
**Date:** September 19, 2026  
**Version:** 1.0  

---

## 1. Testing Scope

Testing covered both the React frontend and Rails API.

The testing approach included:

- Functional testing
- Happy-path testing
- Negative testing
- Boundary testing
- API testing
- Data-state testing
- Pagination testing
- Timezone testing
- Regression planning

The expected behavior was based on the requirements provided by
Chulo Solutions. Where the requirements did not explicitly define a
behavior, it was treated as an open question rather than automatically
reported as a defect.

---

## 2. Highest-Risk Area

### Visitor Lifecycle and Deactivation State

The highest-risk area is the visitor lifecycle, particularly the handling
of active, checked-out and deactivated visitor states.

The feature allows a visitor to move through different states:

```text
Visitor Registration
        |
        v
Active Visitor
        |
        v
Checkout
        |
        v
No longer in Active List
```

A separate deactivation operation should prevent a visitor from appearing
in the active list and from being selected for a repeat visit.

During testing, two problems were confirmed in this area:

- A deactivated visitor remained visible in the Active Visitors list.
- A deactivated visitor remained selectable and could be registered
again as an active visit.

This area is considered high risk because incorrect visitor state handling
can result in reception staff seeing inaccurate visitor information and
can allow a visitor who has been deactivated to create another active
visit.

API testing also identified weaknesses in backend validation and error
handling. The API accepted visitor creation requests without required
fields and produced unhandled errors for non-existent visitor IDs.

These findings increase the importance of validating visitor state and
business rules at both the frontend and backend layers.

---

## 3. QA Judgment on Checkout State

During testing, the checkout API response contained:

`active: True`

together with a populated checked_out_at timestamp.

Rather than immediately reporting this as a defect, the actual business
requirement was checked by sending a subsequent request to the visitor
list endpoint.

The checked-out visitor was not returned by the active visitor list.

Therefore, the observed response field was not reported as a separate
functional defect because the required user-visible behavior — removal
from the active visitor list — was working.

This demonstrates the approach of comparing observed behavior against the
specified requirement rather than reporting an unexpected implementation
detail automatically.

---

## 4. Requirements / Open Questions

The assessment explicitly states that unspecified behavior should be
treated as an open question.

For example, the assessment does not explicitly state that Company or
Purpose must be mandatory. Therefore, successful registration with those
fields empty was not reported as a defect.

The exact matching behavior of the visitor search endpoint is also not
fully specified. Therefore, additional search results were not reported
as a defect without a documented expected matching rule.

---

## 5. Product Owner Question Before Sign-off

Question:

Should a visitor who has previously checked out be allowed to register
another visit later, provided that the visitor has not been deactivated?

Reason for asking:

The requirements explicitly state that deactivated visitors must not be
selectable for repeat visits, but they do not explicitly define the
expected behavior for a visitor who has previously checked out and has
not been deactivated.

Clarifying this business rule would allow the repeat-visit behavior to be
tested and signed off against the intended product behavior.

---

## 6. Test Data Created During Execution

The following records were created during controlled API testing:

- ID 89 — Valid API test visitor; subsequently checked out.
- ID 90 — Negative test visitor created without full_name.
- ID 91 — Negative test visitor created without host_id.

These records were retained as evidence of the observed API behavior.

---

## 7. Recommendations

The following areas should be reviewed by the development team:

- Enforce required visitor fields at the API/model validation layer.
- Prevent deactivated visitors from appearing in active visitor results.
- Prevent deactivated visitors from being selected for repeat visits.
- Convert UTC timestamps to the receptionist's local timezone before
display.
- Handle non-existent visitor IDs with controlled API error responses.
- Avoid exposing internal Rails exception details to API consumers.
