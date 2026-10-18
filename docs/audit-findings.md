# Audit notes

## What I was checking

I wanted to see whether the furniture enquiry workflow made the right decisions, not just whether Make.com reported a successful run.

I used an isolated QA copy with a different webhook, Google Sheets workbook and Data Store. The original automation wasn't changed.

## Finding 1: Same message, different route

**Customer message:** “Reserve two grey sofas for me in Lanet.”

- **T004-R1:** AI said **Ready to Buy**; route was **Ready to Order**.
- **T004-R2:** AI said **Just Asking**; route was **General Enquiry**.

The product, quantity, delivery location and KSh 90,800 calculation were correct in both runs. The problem was inconsistent purchase-intent classification.

### Why it happened

The AI instructions asked it to choose between Ready to Buy, Considering Purchase and Just Asking but didn't define explicit reservation phrases. The router relied on the AI's answer without a separate check for those phrases.

### What I changed

I added **Set variable module 61**, which normalizes selected affirmative reservation requests to **Ready to Buy**, and updated the Ready to Order and General Enquiry filters to use the normalized value.

The rule also tries to exclude some questions, negations and hypothetical statements. These exclusions need more testing; it's not a general language-understanding solution.

## Finding 2: Payment response and formula error

Originally, a reservation without a stated payment method displayed **“Payment method: unknown.”**

I updated the response to ask:

> How would you prefer to pay — M-Pesa or cash on delivery?

An initial formula using unsupported functions failed during execution at T004-R3. After replacing those functions with supported conditional expressions, the response worked in T004-R4.

This was a reminder that a Make scenario can pass configuration validation but fail when a particular branch runs.

## Final verification

**T004-R4** used the same message: “Reserve two grey sofas for me in Lanet.”

- AI intent: **Just Asking**
- Normalized intent: **Ready to Buy**
- Router: **Ready to Order**
- Total: **KSh 90,800**
- Customer received the payment clarification
- QA Enquiries sheet logged the result (row 64)
- Execution completed successfully

This directly tests the original misrouting case. It doesn't measure the reliability of the safeguard on other phrases.

## One more issue to check

The final run used **Ready to Buy** for routing while the AI's original value was **Just Asking**. The separate master report says that original value was stored in conversation state and the Sheets log marked the enquiry as an order. This should be checked against those records before calling it a confirmed data-consistency defect.

## Remaining limitations

- Some other routes still depend on the raw AI purchase intent.
- The reservation check uses phrase matching and can miss or misread unfamiliar wording.
- Multi-turn conversation handling wasn't fully audited.
- The full 30-case evaluation framework hasn't been executed.
- All enquiries in this demonstration were synthetic; no live sale or payment was processed.

## Next steps

Extend the tests to ambiguous, negative and informal requests; review upstream intent-dependent filters; and test the corrected logic against an independent set of unseen customer messages.
