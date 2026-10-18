# Test results

These are the key results recorded from the Make.com QA executions. The tests used synthetic customer messages and isolated QA storage.

| Test | Message / situation | Outcome |
| --- | --- | --- |
| T001 | Two grey sofas to Lanet; cash on delivery | Core checks passed; KSh 90,800 |
| T002 | One grey sofa to Lanet; M-Pesa | Core checks passed; KSh 45,800 |
| T004-R1 | Reserve two grey sofas for me in Lanet | AI: Ready to Buy; Ready to Order; correct total but displayed payment as unknown |
| T006 | Three grey sofas to Lanet; cash on delivery | Core checks passed; KSh 135,800 |
| T004-R2 | Repeat of the same reservation request, fresh state | AI: Just Asking; incorrectly routed to General Enquiry |
| T004-R3 | Reservation after adding intent normalization | Normalizer returned Ready to Buy and router selected Ready to Order, but response formula failed |
| T004-R4 | Reservation after response-template repair | **Passed targeted retest:** AI Just Asking → normalized Ready to Buy → Ready to Order; KSh 90,800; correct payment question; QA log written |

## How results were judged

A successful Make execution on its own was not counted as proof of a correct business decision. I checked the extracted fields, route, amount and message returned.

The four initial tests passed their reported core functional checks. Some additional AI extraction fields were not captured, so this isn't a full field-by-field accuracy score.

## Evidence and privacy

The execution IDs and synthetic phone numbers are kept out of this public summary. Raw webhook URLs, tokens and connection details are not published.

This work used a 30-case evaluation framework, but the full 30 cases were not run. No overall reliability or accuracy percentage is claimed.
