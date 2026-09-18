# QA Test Suite

**Project:** Visitor Check-in System  
**Document:** QA Test Suite  
**Prepared By:** Bhim Kumar Sah  
**Date:** September 19, 2026  
**Version:** 1.0  

---

## 1. Test Overview

### Scope

This test suite covers the Visitor Check-in feature across the React frontend
and Rails API.

Testing covers:

- Visitor registration
- Required and optional fields
- Visitor checkout
- Active visitor list
- Deactivation
- Repeat visits
- Pagination
- Visitor search
- API visitor creation
- API validation
- API error handling
- Check-in time and timezone behavior

### Test Approach

Testing was performed using:

- React frontend through the browser
- Rails API through direct HTTP requests using PowerShell `Invoke-RestMethod`
- Existing seeded visitor and host data
- Additional controlled test records created during API testing

The expected results are based on the requirements provided in the
assessment email. Behavior that is not explicitly defined in the
requirements was treated as an open question rather than automatically
reported as a defect.

### Execution Markers

- `[ ]` — Not executed
- `[pass]` — Executed and passed
- `[fail]` — Executed and failed

---

# 2. Happy Path Tests

## TC-REG-001 — Verify successful visitor registration

**Type:** Happy Path

**Preconditions:**
- The React frontend is running.
- The Rails API is running.
- At least one valid host is available.

**Steps:**
1. Check that the visitor registration form is displayed.
2. Enter a valid visitor full name.
3. Enter a company name.
4. Confirm a valid host is selected.
5. Enter a visit purpose.
6. Confirm the registration form is submitted.
7. Check the Active Visitors list.

**Expected Result:**
The visitor is successfully registered and appears in the Active Visitors
list with a check-in time.

**Actual Result:**
The visitor was successfully registered and appeared in the Active Visitors
list with a check-in time.

**Result:** [pass]


## TC-CHK-001 — Verify active visitor can be checked out

**Type:** Happy Path

**Preconditions:**
- An active visitor is present in the Active Visitors list.

**Steps:**
1. Check that an active visitor is displayed.
2. Confirm the Check Out action is available.
3. Confirm the Check Out action is selected.
4. Check the Active Visitors list.

**Expected Result:**
The visitor is checked out and is removed from the Active Visitors list.

**Actual Result:**
The visitor was removed from the Active Visitors list after checkout.

**Result:** [pass]


## TC-API-001 — Verify hosts endpoint returns available hosts

**Type:** Happy Path / API

**Preconditions:**
- The Rails API is running.
- Host seed data is available.

**Steps:**
1. Check that the Rails API is running.
2. Confirm a GET request is sent to `/api/hosts`.
3. Check the response.
4. Confirm that host records are returned.

**Expected Result:**
The API returns the available host employees successfully.

**Actual Result:**
The API returned 12 host records, including Alice Mercer and
Benjamin Okafor.

**Result:** [pass]


## TC-API-002 — Verify visitors endpoint returns visitor records

**Type:** Happy Path / API

**Preconditions:**
- The Rails API is running.
- Visitor records are available.

**Steps:**
1. Confirm a GET request is sent to `/api/visitors`.
2. Check the response.
3. Confirm that visitor records are returned.
4. Check that visitor records contain visitor and host information.

**Expected Result:**
The API returns visitor records successfully.

**Actual Result:**
The API returned visitor records containing fields including ID,
full name, company name, purpose, check-in time, active status,
host ID and host name.

**Result:** [pass]


## TC-API-003 — Verify valid visitor creation through API

**Type:** Happy Path / API

**Preconditions:**
- The Rails API is running.
- A valid host exists.

**Steps:**
1. Confirm that a valid visitor payload is prepared.
2. Confirm a POST request is sent to `/api/visitors`.
3. Check the API response.
4. Confirm that a visitor ID is returned.
5. Check that the supplied visitor information is present.

**Expected Result:**
The API successfully creates the visitor and returns the created visitor
record.

**Actual Result:**
Visitor ID 89 was successfully created with the supplied visitor details,
host information and a check-in timestamp.

**Result:** [pass]

---

# 3. Negative Tests

## TC-REG-002 — Verify Full Name is required

**Type:** Negative

**Preconditions:**
- The visitor registration form is displayed.
- A valid host is available.

**Steps:**
1. Check that the Full Name field is empty.
2. Confirm a valid host is selected.
3. Enter valid values in the other applicable fields.
4. Confirm the registration form is submitted.
5. Check the validation message.

**Expected Result:**
The form prevents submission and indicates that Full Name is required.

**Actual Result:**
The browser displayed a required-field validation message and prevented
submission.

**Result:** [pass]


## TC-REG-003 — Verify Host is required

**Type:** Negative

**Preconditions:**
- The visitor registration form is displayed.

**Steps:**
1. Enter a valid Full Name.
2. Check that Host is not selected.
3. Enter valid values in the other applicable fields.
4. Confirm the registration form is submitted.
5. Check the validation message.

**Expected Result:**
The form prevents submission because Host is required.

**Actual Result:**
The browser displayed a validation message indicating that a host must
be selected.

**Result:** [pass]


## TC-ACT-001 — Verify deactivated visitor is excluded from Active Visitors

**Type:** Negative / Functional

**Preconditions:**
- An existing visitor can be deactivated through the available API endpoint.

**Steps:**
1. Check that Ana Smith exists as a visitor.
2. Confirm the deactivation request is sent to `/api/visitors/1/deactivate`.
3. Check the API response.
4. Confirm that Ana Smith has `active: false`.
5. Refresh the frontend.
6. Check the Active Visitors list.

**Expected Result:**
A deactivated visitor must not appear in the Active Visitors list.

**Actual Result:**
Ana Smith remained visible in the Active Visitors list even though the
API reported `active: false`.

**Result:** [fail]

**Related Defect:** DEF-001


## TC-ACT-002 — Verify deactivated visitor cannot be selected for repeat visit

**Type:** Negative / Functional

**Preconditions:**
- Ana Smith has been deactivated.

**Steps:**
1. Check that the visitor registration form is displayed.
2. Enter Ana Smith in the Full Name field.
3. Check the visitor suggestions.
4. Confirm whether Ana Smith is available for selection.
5. Select Ana Smith if the option is available.
6. Check whether the registration can be submitted.

**Expected Result:**
A deactivated visitor must not be available for selection for a repeat
visit.

**Actual Result:**
Ana Smith appeared in the suggestions and could be selected.

**Result:** [fail]

**Related Defect:** DEF-002


## TC-ACT-003 — Verify deactivated visitor cannot be registered again

**Type:** Negative / Functional

**Preconditions:**
- Ana Smith has been deactivated.

**Steps:**
1. Check that Ana Smith is deactivated.
2. Confirm Ana Smith can be selected from the registration suggestions.
3. Confirm the required registration information is available.
4. Confirm the registration is submitted.
5. Check the Active Visitors list.

**Expected Result:**
A deactivated visitor must not be registered for a new active visit.

**Actual Result:**
Ana Smith was successfully registered again and appeared in the Active
Visitors list.

**Result:** [fail]

**Related Defect:** DEF-002


## TC-API-006 — Verify API rejects visitor creation without Full Name

**Type:** Negative / API Validation

**Preconditions:**
- The Rails API is running.
- A valid host is available.

**Steps:**
1. Check that the Rails API is running.
2. Confirm a POST request is prepared without `full_name`.
3. Confirm the request is sent to `/api/visitors`.
4. Check the API response.
5. Check whether a visitor record is created.

**Expected Result:**
The API rejects the request and returns an appropriate validation error
because Full Name is required.

**Actual Result:**
The API created visitor ID 90 with an empty `full_name` and `active: True`.

**Result:** [fail]

**Related Defect:** DEF-004


## TC-API-007 — Verify API rejects visitor creation without Host

**Type:** Negative / API Validation

**Preconditions:**
- The Rails API is running.

**Steps:**
1. Check that the Rails API is running.
2. Confirm a POST request is prepared without `host_id`.
3. Confirm the request is sent to `/api/visitors`.
4. Check the API response.
5. Check whether a visitor record is created.

**Expected Result:**
The API rejects the request and returns an appropriate validation error
because Host is required.

**Actual Result:**
The API created visitor ID 91 with an empty `host_id` and `active: True`.

**Result:** [fail]

**Related Defect:** DEF-004


## TC-API-009 — Verify checkout API handles a non-existent visitor ID

**Type:** Negative / API Error Handling

**Preconditions:**
- The Rails API is running.
- Visitor ID 9999 does not exist.

**Steps:**
1. Check that visitor ID 9999 does not exist.
2. Confirm a PATCH request is sent to `/api/visitors/9999/check_out`.
3. Check the API response.

**Expected Result:**
The API handles the invalid visitor ID using an appropriate client error
response, such as HTTP 404 Not Found, without exposing an internal
application stack trace.

**Actual Result:**
The request produced an unhandled Rails/ActiveRecord error and PowerShell
reported a WebException.

**Result:** [fail]

**Related Defect:** DEF-005


## TC-API-010 — Verify deactivate API handles a non-existent visitor ID

**Type:** Negative / API Error Handling

**Preconditions:**
- The Rails API is running.
- Visitor ID 9999 does not exist.

**Steps:**
1. Check that visitor ID 9999 does not exist.
2. Confirm a PATCH request is sent to `/api/visitors/9999/deactivate`.
3. Check the API response.

**Expected Result:**
The API handles the invalid visitor ID using an appropriate client error
response, such as HTTP 404 Not Found, without exposing an internal
application stack trace.

**Actual Result:**
The request produced an unhandled Rails/ActiveRecord error and PowerShell
reported a WebException.

**Result:** [fail]

**Related Defect:** DEF-006

---

# 4. Boundary Tests

## TC-REG-004 — Verify registration with empty Company

**Type:** Boundary / Requirement Clarification

**Preconditions:**
- The registration form is displayed.
- A valid Full Name and Host are available.

**Steps:**
1. Enter a valid Full Name.
2. Check that Company is empty.
3. Confirm a valid Host is selected.
4. Check that Purpose is empty or valid.
5. Confirm the form is submitted.
6. Check the visitor list.

**Expected Result:**
The application should handle the input according to the defined
requirements. Company is not explicitly stated as required in the
assessment.

**Actual Result:**
The visitor was successfully created with an empty Company value.

**Result:** [pass]

**QA Note:**
This is not reported as a defect because the assessment does not state
that Company is mandatory.


## TC-REG-005 — Verify registration with empty Purpose

**Type:** Boundary / Requirement Clarification

**Preconditions:**
- The registration form is displayed.
- A valid Full Name and Host are available.

**Steps:**
1. Enter a valid Full Name.
2. Enter a valid Company.
3. Confirm a valid Host is selected.
4. Check that Purpose is empty.
5. Confirm the form is submitted.
6. Check the visitor list.

**Expected Result:**
The application should handle the input according to the defined
requirements. Purpose is not explicitly stated as required in the
assessment.

**Actual Result:**
The visitor was successfully created with an empty Purpose value.

**Result:** [pass]

**QA Note:**
This is not reported as a defect because the assessment does not state
that Purpose is mandatory.


## TC-REG-006 — Verify registration with a long Full Name

**Type:** Boundary

**Preconditions:**
- The registration form is displayed.
- A valid host is available.

**Steps:**
1. Enter a 100-character Full Name.
2. Confirm a valid host is selected.
3. Enter valid values for the other fields.
4. Confirm the form is submitted.
5. Check the Active Visitors list.

**Expected Result:**
The application handles the long input without an application error.

**Actual Result:**
The visitor was successfully created. The long name was displayed without
an application failure.

**Result:** [pass]


## TC-REG-007 — Verify registration with special characters

**Type:** Boundary

**Preconditions:**
- The registration form is displayed.
- A valid host is available.

**Steps:**
1. Enter `John @#$123` as the Full Name.
2. Enter valid values for Company and Purpose.
3. Confirm a valid host is selected.
4. Confirm the form is submitted.
5. Check the Active Visitors list.

**Expected Result:**
The application handles the supplied input without an application error.

**Actual Result:**
The visitor was successfully created.

**Result:** [pass]


## TC-PAG-001 — Verify visitor list displays 20 records per page

**Type:** Boundary

**Preconditions:**
- More than 20 visitor records are available.

**Steps:**
1. Check the Active Visitors list.
2. Confirm Page 1 is displayed.
3. Count the records on Page 1.
4. Confirm Page 1 contains 20 records.

**Expected Result:**
The active visitor list displays 20 records per page.

**Actual Result:**
Page 1 displayed 20 records.

**Result:** [pass]


## TC-PAG-002 — Verify final pagination page handles remaining records

**Type:** Boundary

**Preconditions:**
- More than 20 visitor records are available.

**Steps:**
1. Check the Active Visitors list.
2. Confirm the Next action is used to move through the pages.
3. Check the final page.
4. Confirm the final page contains the remaining records.
5. Check that Next is disabled on the final page.

**Expected Result:**
The final page displays the remaining records and prevents navigation
beyond the final page.

**Actual Result:**
The final page contained the remaining record and Next was disabled.

**Result:** [pass]

---

# 5. Pagination and Navigation Tests

## TC-PAG-003 — Verify Next and Previous pagination navigation

**Type:** Functional

**Preconditions:**
- More than 20 visitor records are available.

**Steps:**
1. Check Page 1.
2. Confirm Next is available.
3. Confirm Next is selected.
4. Check Page 2.
5. Confirm Previous is available.
6. Confirm Previous is selected.
7. Check that Page 1 is displayed again.

**Expected Result:**
Next and Previous navigation move between available pages correctly.

**Actual Result:**
The UI moved from Page 1 to Page 2 and back using Next and Previous.

**Result:** [pass]


## TC-API-003 — Verify visitor API pagination

**Type:** Boundary / API

**Preconditions:**
- The Rails API is running.
- More than 20 visitor records are available.

**Steps:**
1. Confirm a GET request is sent to `/api/visitors?page=1`.
2. Check the number of records returned.
3. Confirm that 20 records are returned.
4. Confirm a GET request is sent to `/api/visitors?page=2`.
5. Check the number of records returned.
6. Confirm that 20 records are returned.

**Expected Result:**
Each requested page returns a maximum of 20 visitor records.

**Actual Result:**
Page 1 returned 20 records and Page 2 returned 20 records.

**Result:** [pass]

---

# 6. Deactivation Tests

## TC-DEC-001 — Verify visitor deactivation changes visitor status

**Type:** Functional / API

**Preconditions:**
- Ana Smith exists as visitor ID 1.
- The Rails API is running.

**Steps:**
1. Check that Ana Smith exists.
2. Confirm a PATCH request is sent to `/api/visitors/1/deactivate`.
3. Check the response.
4. Confirm that the returned visitor has `active: false`.

**Expected Result:**
The visitor is marked as deactivated.

**Actual Result:**
Ana Smith was returned with `active: False`.

**Result:** [pass]


## TC-TZ-001 — Verify check-in time uses receptionist local timezone

**Type:** Functional / Boundary

**Preconditions:**
- Visitor records with UTC timestamps are available.
- The application is designed for Asia/Kathmandu.

**Steps:**
1. Check the API check-in timestamp for Bola Jones.
2. Confirm that the timestamp is `2026-09-12T17:38:24Z`.
3. Convert the timestamp from UTC to Asia/Kathmandu time.
4. Check the time displayed in the frontend.
5. Compare the displayed time with the local time.

**Expected Result:**
The check-in time is displayed in the receptionist's local timezone.
The supplied UTC timestamp corresponds to approximately 23:23 in
Asia/Kathmandu.

**Actual Result:**
The frontend displayed approximately 17:38, matching the UTC clock time
rather than the Asia/Kathmandu local time.

**Result:** [fail]

**Related Defect:** DEF-003

---

# 7. Additional API Tests

## TC-API-004 — Verify visitor search endpoint returns relevant records

**Type:** Functional / API

**Preconditions:**
- The Rails API is running.
- Visitor records are available.

**Steps:**
1. Confirm a GET request is sent to `/api/visitors/search?query=Ana`.
2. Check the response.
3. Confirm that visitor records are returned.
4. Check that records relevant to the supplied search term are present.

**Expected Result:**
The search endpoint returns visitor records relevant to the supplied
search term.

**Actual Result:**
The API returned visitor records including Ana Smith and other records
relevant to the supplied search value.

**Result:** [pass]

**QA Note:**
The exact search matching or ranking behavior is not explicitly defined
in the assessment, so additional returned records were not treated as
defects.


## TC-API-008 — Verify checked-out visitor is removed from active list

**Type:** Functional / API

**Preconditions:**
- Active visitor ID 89 exists.

**Steps:**
1. Check that visitor ID 89 is active.
2. Confirm a PATCH request is sent to `/api/visitors/89/check_out`.
3. Check that the response contains a `checked_out_at` timestamp.
4. Confirm a GET request is sent to `/api/visitors`.
5. Check whether visitor ID 89 is returned.

**Expected Result:**
The visitor receives a checkout timestamp and is no longer returned in
the active visitor list.

**Actual Result:**
Visitor ID 89 received a `checked_out_at` timestamp and was not returned
by the subsequent visitor list request.

**Result:** [pass]

**QA Note:**
The checkout response contained `active: True`, but the visitor was
removed from the active list. Because the requirement concerns the active
list behavior, this was not reported as a separate defect.


---

# 8. Regression Subset — Minor Registration Form Update

## Assumption

The regression subset assumes that the minor update affects the visitor
registration form, its validation, visitor selection, or submission
behavior.

The subset includes tests directly affected by registration and excludes
tests that are independent of registration.

| Test ID | Decision | Rationale |
|---|---|---|
| TC-REG-001 | Include | The core registration flow could be affected by any form update. |
| TC-REG-002 | Include | Full Name validation could be affected by form changes. |
| TC-REG-003 | Include | Host selection and required-field behavior could be affected. |
| TC-REG-004 | Include | Changes to field handling could affect empty Company behavior. |
| TC-REG-005 | Include | Changes to field handling could affect empty Purpose behavior. |
| TC-REG-006 | Include | Input handling changes could affect long Full Name values. |
| TC-REG-007 | Include | Input handling changes could affect special characters. |
| TC-ACT-002 | Include | Registration form changes could affect deactivated visitor selection. |
| TC-ACT-003 | Include | Registration changes could affect repeat-visit restrictions. |
| TC-API-003 | Include | Visitor creation through the API supports the registration workflow. |
| TC-API-006 | Include | Backend validation can affect registration submissions. |
| TC-API-007 | Include | Backend Host validation can affect registration submissions. |
| TC-CHK-001 | Exclude | Checkout is a separate workflow from registration. |
| TC-PAG-001 | Exclude | Pagination is unrelated to the registration form. |
| TC-PAG-002 | Exclude | Final-page behavior is unrelated to registration. |
| TC-PAG-003 | Exclude | Pagination navigation is unrelated to registration. |
| TC-DEC-001 | Exclude | Deactivation is a separate administrative workflow. |
| TC-TZ-001 | Exclude | Timezone rendering is not directly related to the form update. |
| TC-API-001 | Exclude | Host retrieval itself is not changed by a minor registration form update. |
| TC-API-002 | Exclude | General visitor retrieval is separate from the registration form. |
| TC-API-004 | Include | Visitor search/autocomplete is part of the registration workflow. |
| TC-API-008 | Exclude | Checkout behavior is independent of registration. |
| TC-API-009 | Exclude | Invalid checkout handling is unrelated to registration. |
| TC-API-010 | Exclude | Invalid deactivation handling is unrelated to registration. |

---

# 9. Execution Summary

The executed suite contains functional, negative and boundary scenarios across
the frontend and API.

Confirmed failed scenarios are linked to:

- DEF-001 — Deactivated visitor remains in Active Visitors.
- DEF-002 — Deactivated visitor can be selected and registered again.
- DEF-003 — Check-in time is displayed using UTC instead of local time.
- DEF-004 — API allows creation without required fields.
- DEF-005 — Checkout API does not gracefully handle a non-existent ID.
- DEF-006 — Deactivate API does not gracefully handle a non-existent ID.

Tests that were not executed should remain marked `[ ]` rather than being
reported as passed.
