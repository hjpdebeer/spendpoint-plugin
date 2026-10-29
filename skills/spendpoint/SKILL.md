---
name: spendpoint
description: Record receipts in the user's Spendpoint ledger and answer questions about their spending. Use whenever the user shows a receipt, asks to log an expense, or asks what they spent.
---

# Spendpoint

Spendpoint is the user's expense ledger. Its tools record receipts exactly as printed and compute spending reports; never add amounts yourself.

## Recording a receipt
1. Read the receipt the user showed: merchant, date, total, VAT or sales tax if printed, currency, and every line with its quantity and amount, including discounts and rounding lines, so that the lines add up to the total. When printed, also take the receipt number, the merchant's tax number, the time, how it was paid, and the VAT summary per rate, a sales tax line with its rate being a summary of one rate; leave each out when it is not printed.
2. Call `capture_receipt` once per receipt, with a verbatim transcription in `receipt_text`.
3. If the server answers `possible_duplicate`, an entry with the same merchant and receipt number, or the same merchant, date and total, exists. Ask the user; resend with `confirm_duplicate: true` only if they say it is a separate purchase.
4. If the date, the total or a line is not legible, ask the user rather than guess. Before recording, tell the user what you are about to record (merchant, date, total, category). When the category is not obvious, ask which fits, offering the list, or add a new one with `add_category`; the server refuses `unknown` until the user has been asked, and accepts it with `confirm_unknown: true` only when the user wants the entry left unclassified.
5. An entry is recorded only when the tool result carries its id. A result marked as an error means nothing was stored: never report it as recorded; say what was refused and act on the message.
6. Read the entry afresh from the ledger with `get_expense` and tell the user what the ledger holds, not what you sent.
7. When the user says the purchase is for work, a client or a claim, record it with `reimbursement: "claimable"`; when they name a trip, a project or a client to group it under, set `label` to that name. Never infer either from the receipt.
8. When the entry read back carries `budgets`, tell the user how the month stands against each: spent, remaining and the percentage used, in the same breath as the confirmation.

## Receipts in other languages
- Transcribe verbatim in every script printed; never translate or transliterate in `receipt_text`.
- Each line's `description` is the item name as printed in every language printed, separated by " / ", without quantity, price or barcode; those are fields.
- `merchant` is as printed, in the Latin script when the receipt prints more than one.
- Convert dates to YYYY-MM-DD and numbers to dot decimals, reading day and month order from the receipt's country and language. Ask the user when a date is ambiguous.
- Map the printed payment words to `cash`, `card`, `transfer` or `other`.
- A two-digit year is 20YY; a 12-hour time becomes 24-hour. Include each line's `unit_price` when printed; the server checks quantity times price against the amount.
- Translate when telling the user what was recorded, never in what you record.

## Correcting
- `amend_expense` replaces an entry with the complete corrected receipt; the original is kept and linked.
- `void_expense` retires an entry recorded by mistake, with the user's reason; `restore_expense` undoes a void when the user says the void was the mistake.
- Never record a receipt a second time to fix it.
- `set_reimbursement` marks an entry claimed when the user has put the claim in, paid when the money came back, or personal to withdraw it. An amendment keeps the entry's state.

## Answering questions
- `report` sums current entries by category, merchant, month, label or in total, per currency, over a date range; `label` and `reimbursement` narrow it, so "what is still claimable from the Stockholm trip" is one call.
- `list_expenses` and `get_expense` find and show entries; both filters apply to the list too.
- `export_ledger` returns the whole ledger as JSON or CSV when the user asks for their data. Ask the user for the address they registered with Spendpoint and pass it as `email`; never take it from memory. The service checks it and mails the user a notice; a mismatch is refused.

## Budgets
- `set_budget` sets, changes or removes (with 0) the user's monthly budget for one category in one currency, at their word.
- `list_budgets` shows every budget with its standing this month, or any month asked for as YYYY-MM.
- Budgets are reported against, never enforced: a capture is never refused for exceeding one.

## When the user asks what they can do
- Call `guide` and answer in your own words, briefly, with two or three examples that fit what they have been doing. It also says what is new.

## Categories and connections
- `list_categories` shows the user's categories with meanings; `add_category` and `update_category` change them when the user asks.
- `list_connections` and `end_connection` show and disconnect assistants, at the user's request only.
