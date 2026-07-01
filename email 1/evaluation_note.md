# How to evaluate the triage prompt

Run the six emails in `failure_examples.md` through the Project with the new prompt. Pass/fail on each:

## 1. Output shape
- Single JSON object, nothing else
- Valid keys: `category`, `urgent`, `route_to`, `draft`
- `urgent` is boolean; spam rows have `"draft": ""`

## 2. Routing correctness (the six failures)

| Email | Expected category | Expected route | Why |
|---|---|---|---|
| Kevin (22 pallets, Wed deadline) | corporate_food_donation | priya | Dock/receiving decision, deadline-driven |
| Sarah (canned goods drop-off) | individual_food_donation | auto_ok | Standard script, no staff needed |
| Pastor Dan (extra allocation) | partner_agency_request | marcus | Allocation authority; draft must not commit |
| GrantStation spam | spam_no_reply | no_reply | Empty draft |
| Jen (NHS hours + food drive) | volunteer_inquiry or food_drive | diane | School partnership; no fake attachment |
| Ana Lucía (Spanish, needs food) | food_assistance_seeker | intake_line | Pantry referral, not warehouse pickup |

## 3. Draft content checks
- **Kevin:** no yes/no on pallets; acknowledges deadline
- **Sarah:** Donor Intake address/hours — not "warehouse anytime 8–6 daily"
- **Pastor Dan:** no promise of half-pallet or protein cases
- **Spam:** draft is empty string
- **Jen:** offers to *send* needs list, does not say "I'm attaching"
- **Ana:** Spanish reply, intake phone/website, no warehouse visit instructions

## 4. Regression spot-check
Pull 10 rows from `inbox_sample.csv` covering each category. Confirm corporate surplus hits `priya`, allocation requests hit `marcus`, promos hit `no_reply`, and assistance emails hit `intake_line`.

## 5. Speed test
Volunteer should copy-paste JSON `draft` field directly into Gmail. If they are editing more than one sentence per email, tighten the prompt.

**Ship criteria:** all six failures pass routing + draft rules; zero allocation promises; zero warehouse directions to assistance seekers.
