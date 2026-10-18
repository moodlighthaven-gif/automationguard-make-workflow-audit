# AutomationGuard - Make.com Workflow Audit

A small project I built to test and improve an AI-powered furniture enquiry workflow.

I already had a working Make.com automation that could read customer messages, find furniture prices, calculate delivery fees and prepare replies. I wanted to find out what happened when the AI understood a message incorrectly — even if the scenario itself ran without errors.

## The problem I found

I tested this message twice, using fresh conversation records each time:

> Reserve two grey sofas for me in Lanet.

The AI gave two different answers for the customer's intent:

| Test | AI's decision | Where the workflow sent the enquiry |
| --- | --- | --- |
| T004-R1 | Ready to Buy | Ready to Order |
| T004-R2 | Just Asking | General Enquiry |

The second run returned general product information, even though the customer had asked to reserve sofas.

The prices were correct in both runs. The issue was the **route the automation chose**, not the calculation.

## What I changed

I made the changes in a separate QA copy of the scenario, leaving the original furniture workflow alone.

1. Added a **Set variable** step to recognize selected clear reservation requests such as “reserve two sofas for me.”
2. Used that normalized intent in the **Ready to Order** and **General Enquiry** router filters.
3. Updated the response so an unspecified payment method prompts a useful question instead of showing “Payment method: unknown.”
4. Checked the changes through Make execution logs.

I also caught a response-template error: a formula passed the scenario's configuration checks but failed during execution. I replaced the unsupported functions and retested.

## What happened in the final test

The final test used the same reservation message.

| Check | Result |
| --- | --- |
| AI's original intent | Just Asking |
| Intent after my rule | Ready to Buy |
| Route taken | Ready to Order |
| Product | Grey L-Shaped Fabric Sofa (P001) |
| Quantity | 2 |
| Subtotal | KSh 90,000 |
| Delivery to Lanet | KSh 800 |
| Total | **KSh 90,800** |
| Payment question | Shown correctly |
| Execution | Completed successfully |

The reply asked:

> How would you prefer to pay — M-Pesa or cash on delivery?

The enquiry was also recorded in the QA Google Sheet.

**This confirmed the fix for the message that originally went down the wrong route.** It does not prove the automation will interpret every customer message correctly.

## How I tested it

I copied the existing scenario and gave it its own webhook, Google Sheets workbook and Make Data Store. The QA copy had no direct WhatsApp sending module, so I could test with made-up customer enquiries.

I created a 30-case evaluation framework covering straightforward orders, unclear products, payment questions and missing information. I ran four initial order tests and several follow-up investigations and retests. The whole set of 30 cases has **not** been completed.

For a short record of the findings and remaining work, see [the audit notes](docs/audit-findings.md) and [the test results](docs/test-results.md).

## Tools used

- Make.com — webhooks, routers, variables and automation logic
- Make AI Toolkit — extraction of product details and purchase intent
- Google Sheets — product information, business rules and QA enquiry records
- Make Data Store — conversation state
- JSON — structured inputs and responses

## What I learned

A workflow can show **“success”** in its execution history and still make the wrong business decision. Checking the response and the route taken matters just as much as checking whether the scenario ran.

## What I would improve next

- Test more wording, including questions and negated reservations.
- Review other router filters that still use raw AI intent.
- Check longer, multi-message conversations.
- Run the remaining test cases, including a fresh independent holdout set.

## Related project

This audit uses a QA copy of my earlier [WhatsApp Furniture Enquiry Automation](https://github.com/moodlighthaven-gif/whatsapp-furniture-enquiry-automation).

---

*This is a self-initiated demonstration using synthetic test enquiries. It is not a paid client project, and no live customer order or payment was processed.*
