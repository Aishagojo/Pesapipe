# Implementation Roadmap

## Today: Structure

- [x] Create project directories and ownership notes.
- [x] Add package metadata and a runnable setup message.
- [x] Add configuration template and secret exclusions.
- [x] Document intended architecture and next steps.

## Next: Local Mock Workflow

- [ ] Validate environment settings and recipient/amount input.
- [ ] Choose durable storage and add the transfer schema.
- [ ] Implement mock invoice, payment, and payout adapters.
- [ ] Implement CLI transfer creation and status commands.
- [ ] Test settlement checks, duplicate events, and payout timeouts.

## Then: Provider Sandboxes

- [ ] Verify current provider documentation and account requirements.
- [ ] Confirm a compatible BTCPay test environment and wallet.
- [ ] Integrate test invoices and authenticated settlement notifications.
- [ ] Select and integrate one KES payout sandbox.
- [ ] Add the HTTP service, USSD flow, and SMS delivery.
- [ ] Test reconciliation and restart recovery end to end.

## Before a Live Pilot

- [ ] Implement authentication, rate limits, redacted logs, and monitoring.
- [ ] Confirm fees, quote expiry, liquidity, limits, and refund procedures.
- [ ] Resolve applicable provider onboarding and operating requirements.
- [ ] Add backups, deployment automation, and recovery procedures.
- [ ] Review security and payment correctness before enabling real funds.
