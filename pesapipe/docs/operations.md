# Operations

## Current Setup

`npm start` prints the scaffold status and exits. `npm run check` checks its
JavaScript syntax. Neither command contacts providers or moves money.

No dependency installation, server, database, Docker environment, or cloud
account is needed today. Add packages and commit a generated package lock when
the first integration is implemented.

## Future Configuration

Implement environment loading and validation in `src/config/`. Start with mocks,
then provider sandboxes. Keep credentials out of source control and logs. Avoid
logging full phone numbers or sensitive payment data in shared CI output.

The environment template does not enforce sandbox behavior by itself. Future
code must explicitly validate its selected mode and provider configuration.

## Future Hosting

Add deployment configuration after choosing the database and hosting target.
Use a persistent service for callbacks and a worker for background tasks. Add CI
checks when meaningful workflow tests exist. Sandbox access, test network
support, SMS charges, and hosting costs need verification with selected providers.
