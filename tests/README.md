# Test Plan

No payment implementation or automated payment tests exist yet. Add tests with
the first workflow using Node's built-in test runner and mock provider adapters.

Prioritize invalid amounts, unsettled invoices, duplicate events, concurrent
payout attempts, expired invoices, provider timeouts, and restart recovery.
Keep sandbox integration tests separate from local tests and never require live
credentials for default checks.
