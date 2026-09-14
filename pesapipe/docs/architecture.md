# Architecture

## Intended Flow

1. Receive a recipient and KES amount through CLI or USSD.
2. Validate input and persist a transfer with a unique reference.
3. Establish the quote, fees, expiry, and required payout liquidity.
4. Create a BTCPay invoice and store its reference against the transfer.
5. Deliver payment instructions through the terminal or SMS.
6. Verify payment settlement through authenticated provider communication.
7. Request one KES payout using a stable idempotency reference.
8. Confirm the payout result and reconcile uncertain outcomes.

## Responsibilities

CLI and HTTP endpoints translate input into service calls. Services own transfer
rules. Integrations isolate provider-specific requests. Storage keeps durable
records. Workers handle retries, event processing, and reconciliation.

BTCPay invoice handling and KES payout execution are separate responsibilities.
The project must establish how BTC is converted or how KES liquidity is funded;
receiving Bitcoin alone does not fund an M-Pesa payout.

USSD collects transfer details. Paying Bitcoin still requires a compatible
wallet; a USSD menu alone does not give a feature phone Bitcoin wallet capability.
Decide on a practical SMS delivery format before implementing invoices.

## Reliability Requirements for Implementation

- Persist invoice, transfer, payout, and provider event identifiers.
- Verify webhook signatures and deduplicate provider events.
- Verify settlement before authorizing a payout.
- Use database constraints and provider idempotency to prevent duplicate payouts.
- Treat payout submission as pending until a confirmed provider result arrives.
- Reconcile ambiguous timeouts before retrying a money movement request.
- Define expiry, underpayment, late payment, failed payout, and refund handling.
- Store monetary values in exact units with explicit currency, not floating point.

## Deployment Shape

The eventual HTTP service receives USSD requests and provider callbacks. A worker
processes durable jobs. Both share persistent storage. CI/CD runs checks and
deployment tasks, without waiting for customers to pay inside workflow logs.
