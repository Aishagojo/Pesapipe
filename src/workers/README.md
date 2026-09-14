# Background Workers

Future durable event processing, bounded retries, invoice expiry checks, and
payout reconciliation. Recover unfinished jobs after restarts. Query uncertain
provider outcomes before retrying a payout. Do not use an in-memory timer as the
only record of pending payment work.
