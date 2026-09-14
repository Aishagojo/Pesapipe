# Persistent Storage

Future database connection, migrations, and repositories for transfers, invoices,
payout attempts, received events, and durable jobs. Select the database before
implementing the first workflow. Unique event and payout references must survive
process restarts to support deduplication and reconciliation.
