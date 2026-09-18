# Defect Report

**Project:** Visitor Check-in System  
**Document:** Defect Report  
**Prepared By:** Bhim Kumar Sah  
**Date:** September 19, 2026  
**Version:** 1.0  

---

## 1. Summary

Six functional defects were confirmed during testing of the Visitor
Check-in System.

The defects cover:

- Deactivated visitor state handling
- Repeat visit restrictions
- Timezone display
- API validation
- API error handling

All defects below were reproduced during testing.

---

# DEF-001 — Deactivated visitor remains in Active Visitors list

**Summary:**  
A deactivated visitor remains visible in the Active Visitors list.

**Type:**  
Functional

**Severity:**  
High

**Status:**  
Confirmed

**Description:**  
The application continues to display a visitor in the Active Visitors list
after the visitor has been deactivated.

The assessment explicitly states that deactivated visitors must not appear
in the active visitor list.

**Steps to Reproduce:**

1. Check that Ana Smith exists as visitor ID 1.
2. Confirm Ana Smith is an existing visitor.
3. Send a PATCH request to `/api/visitors/1/deactivate`.
4. Check the API response.
5. Confirm that Ana Smith has `active: false`.
6. Open or refresh the Visitor Check-in frontend.
7. Check the Active Visitors list.

**Expected Result:**  
The deactivated visitor should not appear in the Active Visitors list.

**Actual Result:**  
Ana Smith continued to appear in the Active Visitors list even though the
API reported `active: false`.

**Related Test Case:**  
TC-ACT-001

---

# DEF-002 — Deactivated visitor can be registered for a repeat visit

**Summary:**  
A deactivated visitor remains selectable and can be registered again.

**Type:**  
Functional

**Severity:**  
High

**Status:**  
Confirmed

**Description:**  
After a visitor is deactivated, the registration form still displays the
visitor in its suggestions. The visitor can then be selected and registered
as a new active visit.

The assessment explicitly states that deactivated visitors must not be
selectable for repeat visits.

**Steps to Reproduce:**

1. Check that Ana Smith exists.
2. Confirm Ana Smith is deactivated through
   `/api/visitors/1/deactivate`.
3. Open the visitor registration form.
4. Enter Ana Smith in the Full Name field.
5. Check the visitor suggestions.
6. Confirm Ana Smith is available for selection.
7. Select Ana Smith.
8. Confirm the required registration information is available.
9. Submit the registration.
10. Check the Active Visitors list.

**Expected Result:**  
A deactivated visitor should not be available for selection or registration
for a repeat visit.

**Actual Result:**  
Ana Smith appeared in the suggestions, could be selected, and was
successfully registered again. The new visit appeared in the Active
Visitors list.

**Related Test Cases:**  
TC-ACT-002, TC-ACT-003

---

# DEF-003 — Check-in time is displayed using UTC instead of local timezone

**Summary:**  
The frontend displays the UTC check-in time instead of the receptionist's
local timezone.

**Type:**  
Functional

**Severity:**  
Medium

**Status:**  
Confirmed

**Description:**  
The API provides timestamps in UTC, but the frontend displays the UTC
clock value instead of converting the timestamp to the receptionist's
local timezone.

The assessment states that the application is designed for
Asia/Kathmandu and that all times should be displayed in the receptionist's
local timezone.

**Steps to Reproduce:**

1. Open the Visitor Check-in application.
2. Locate Bola Jones in the visitor list.
3. Check the API timestamp for Bola Jones.
4. Confirm the timestamp is `2026-09-12T17:38:24Z`.
5. Convert the timestamp from UTC to Asia/Kathmandu.
6. Check the time displayed in the frontend.

**Expected Result:**  
The timestamp should be converted to the receptionist's local timezone.

`17:38 UTC` corresponds to approximately `23:23` in
Asia/Kathmandu.

**Actual Result:**  
The frontend displayed approximately `17:38`, matching the UTC clock
time instead of the Asia/Kathmandu local time.

**Related Test Case:**  
TC-TZ-001

---

# DEF-004 — API allows visitor creation without required fields

**Summary:**  
The visitor creation API accepts requests with missing required fields and
creates active visitor records.

**Type:**  
Functional / API Validation

**Severity:**  
High

**Status:**  
Confirmed

**Description:**  
The POST `/api/visitors` endpoint does not enforce the required Full Name
and Host fields at the API layer.

Two separate negative tests successfully created visitor records with
required information missing.

**Steps to Reproduce:**

### Scenario A — Missing Full Name

1. Check that the Rails API is running.
2. Send a POST request to `/api/visitors` without `full_name`.
3. Check the API response.
4. Check whether a visitor record is created.

### Scenario B — Missing Host

1. Check that the Rails API is running.
2. Send a POST request to `/api/visitors` without `host_id`.
3. Check the API response.
4. Check whether a visitor record is created.

**Expected Result:**  
The API should reject the invalid requests and return an appropriate
validation error.

**Actual Result:**  
The API accepted both invalid requests and created active visitor records.

Evidence:

- Visitor ID `90` was created without `full_name`.
- Visitor ID `91` was created without `host_id`.

**Related Test Cases:**  
TC-API-006, TC-API-007

---

# DEF-005 — Checkout API does not gracefully handle a non-existent visitor ID

**Summary:**  
The checkout API produces an unhandled server error when a non-existent
visitor ID is supplied.

**Type:**  
Functional / API Error Handling

**Severity:**  
Medium

**Status:**  
Confirmed

**Description:**  
The PATCH `/api/visitors/:id/check_out` endpoint does not handle a
non-existent visitor ID using a controlled API error response.

Instead, the request results in an unhandled Rails/ActiveRecord exception.

**Steps to Reproduce:**

1. Check that the Rails API is running.
2. Check that visitor ID `9999` does not exist.
3. Send a PATCH request to `/api/visitors/9999/check_out`.
4. Check the response.

**Expected Result:**  
The API should return an appropriate client error, such as HTTP 404 Not
Found, with a controlled error response and without exposing internal
application details.

**Actual Result:**  
The request produced an unhandled Rails/ActiveRecord error and PowerShell
reported a WebException. The response exposed internal Rails error
information.

**Related Test Case:**  
TC-API-009

---

# DEF-006 — Deactivate API does not gracefully handle a non-existent visitor ID

**Summary:**  
The deactivate API produces an unhandled server error when a non-existent
visitor ID is supplied.

**Type:**  
Functional / API Error Handling

**Severity:**  
Medium

**Status:**  
Confirmed

**Description:**  
The PATCH `/api/visitors/:id/deactivate` endpoint does not handle a
non-existent visitor ID using a controlled API error response.

Instead, the request results in an unhandled Rails/ActiveRecord exception.

**Steps to Reproduce:**

1. Check that the Rails API is running.
2. Check that visitor ID `9999` does not exist.
3. Send a PATCH request to `/api/visitors/9999/deactivate`.
4. Check the response.

**Expected Result:**  
The API should return an appropriate client error, such as HTTP 404 Not
Found, with a controlled error response and without exposing internal
application details.

**Actual Result:**  
The request produced an unhandled Rails/ActiveRecord error and PowerShell
reported a WebException. The response exposed internal application error
information.

**Related Test Case:**  
TC-API-010
