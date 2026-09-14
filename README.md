1 file changed
+175
-44
README.md
# PesaPipe

A planned backend service for requesting Bitcoin payments and coordinating
Kenyan shilling (KES / Ksh) payouts to mobile money recipients. Users will access
it through a terminal or USSD menu, with SMS for payment instructions.
**Bitcoin-to-KES remittances through the terminal and USSD.**

PesaPipe is a lightweight, headless remittance gateway being built to connect
Bitcoin payments with Kenya's mobile money ecosystem, including Safaricom M-Pesa.
Its goal is to let a sender request a transfer, pay a Bitcoin invoice, and have
the backend coordinate a Kenyan shilling (KES / Ksh) payout to the recipient.

The project brings together two access channels: a programmable terminal
interface for developers and a USSD menu for phone users, with SMS delivering
payment instructions. No custom website or mobile application is planned.

> **Development status:** Initial project scaffold. The repository contains an
> organized backend structure, configuration templates, documentation, and a
> runnable CLI status message. Invoices, USSD, SMS, and payouts are not yet
> implemented.

## Tech Stack

Color badges identify each technology; the status column distinguishes today's
setup from planned integrations.
The implementation targets Node.js and JavaScript. The table separates the
current setup from planned integrations and decisions still to be made.

| Layer | Technology | Purpose | Status |
| --- | --- | --- | --- |
| KES payouts | ![Provider To Be Selected](https://img.shields.io/badge/Provider-To_Be_Selected-64748B?style=for-the-badge) | Mobile money payouts through a verified sandbox | Undecided |
| Database | ![Storage To Be Selected](https://img.shields.io/badge/Storage-To_Be_Selected-64748B?style=for-the-badge) | Durable transfer, event, and payout records | Undecided |

Badges load from Shields.io and require an internet connection.
Kotani Pay and Bitlipa are candidate payout providers, not integrated partners.
Provider selection depends on suitable sandbox access, payout support, and
funding arrangements. Badges are hosted by Shields.io.

## Project Overview

PesaPipe aims to make Bitcoin-funded mobile money transfers accessible without
building a separate graphical application. Developers should be able to request
transfers from scripts, while phone users should be able to enter a recipient
and amount through a short USSD session.

The backend will connect invoice creation, payment verification, and payout
tracking in one durable workflow. Removing a custom frontend reduces the scope
of the application; secure APIs, persistent storage, and reliable payment
processing remain essential parts of the design.

### Developer Channel

A command-line interface will accept a recipient and KES amount, return a
transfer reference, and display payment instructions with a terminal QR code.
It is intended for local use and integration into automated workflows.

CI/CD will handle code checks and deployment. A persistent backend and workers
will handle payment callbacks and payout processing, independently of CI job
timeouts. Sensitive payment details should not be exposed in shared build logs.

### USSD Channel

A planned USSD integration will guide users through entering a recipient,
choosing an amount, and confirming a transfer request. SMS will deliver payment
instructions after the session ends, with the delivery format validated during
sandbox development.

USSD provides a familiar menu experience but is not an M-Pesa SIM Toolkit
integration. It can collect requests from compatible feature phones; paying the
Bitcoin invoice still requires access to a compatible Bitcoin wallet.

No custom smart contracts are planned. CI/CD will check and deploy code; the
payment service and background workers will run independently of CI jobs.
## Planned Transfer Flow

## Status
1. **Request a transfer.** The sender provides a recipient's phone number and a
   KES amount through the CLI or USSD.
2. **Prepare the quote.** The backend validates the request, establishes the
   amount, fees, and expiry, and checks the available payout funding.
3. **Create an invoice.** The BTCPay integration requests an invoice for the
   agreed amount using the configured payment methods and rate settings.
4. **Deliver payment instructions.** Terminal users receive invoice text and a
   QR code; USSD users receive instructions through SMS.
5. **Verify settlement.** The backend verifies the invoice status through
   authenticated provider communication and records the payment.
6. **Request the KES payout.** A worker submits the payout to the selected
   provider using a stable reference to prevent duplicate requests.
7. **Confirm the outcome.** The service records the provider's confirmed result
   and reconciles pending or uncertain payouts before reporting completion.

Project setup only: organized folders, configuration templates, documentation,
and a tiny runnable CLI. Payment integrations are not implemented. This is a
foundation for a product, not a production-ready payment service.
These steps describe the intended behavior, not functionality available today.
Payment and payout completion times will depend on the selected networks and
providers.

## Run Today
## Architecture

Use Node.js 22 or newer. No dependencies or API keys are needed for this scaffold.
```text
Terminal CLI ----+
                |
USSD gateway ---+--> PesaPipe backend --> BTCPay invoice
                         |                     |
                         |              Verified settlement
                         |                     |
                         +<--------------------+
                         |
                  Durable storage
                         |
                  Background worker
                         |
                  KES payout provider --> Recipient mobile money
```

Bitcoin collection and KES disbursement are separate operations. The project
must establish a funded KES balance or a supported conversion arrangement with
the payout provider; invoice settlement alone does not complete a fiat payout.

The workflow will need persistent transfer records, authenticated callbacks,
event deduplication, payout idempotency, and recovery after service restarts.
Late payments, expired invoices, failed payouts, and refunds also need explicit
handling.

## Smart Contracts

No custom smart contracts are planned for this architecture. PesaPipe will
coordinate Bitcoin invoice handling and fiat provider APIs through its backend.
Bitcoin settlement does not by itself guarantee the KES payout; that outcome
depends on the application's controls, available liquidity, and payout provider.

## Development Approach

Development will start with local mock adapters, then move to compatible Bitcoin
test environments and provider sandboxes before considering live transfers.

- **Local mocks:** Build and test the transfer workflow without provider accounts.
- **Bitcoin testing:** Confirm a BTCPay test setup and a wallet supporting the
  same network and payment method, including Lightning where required.
- **Telecom testing:** Validate USSD sessions and SMS delivery in the selected
  gateway's simulator or sandbox.
- **Payout testing:** Integrate one confirmed KES payout sandbox and test success,
  failure, timeout, and duplicate-request behavior.

Test-network compatibility, account requirements, and provider or hosting costs
must be checked during integration. The project does not assume every external
service is free.

## Getting Started

Use Node.js 22 or newer. From the repository root:

```sh
cd /home/omar/Pesapipe/pesapipe
cd pesapipe
npm start
npm run check
```

`pesapipe/.env.example` documents future settings. Keep real credentials in an ignored
`.env` file when configuration loading is implemented.
No dependency installation or API credentials are needed for the current
scaffold. The start command prints the project status and exits; the check
command validates the starter JavaScript syntax.

## Folder Guide
[Environment template](pesapipe/.env.example) lists future configuration
settings. It is not loaded by the starter CLI. Keep real credentials in an
ignored `pesapipe/.env` file when configuration loading is implemented.

The main `README.md` lives at the repository root, beside the `pesapipe/`
project folder shown below.
## Repository Structure

```text
pesapipe/
|-- src/
|   |-- cli/                 Terminal entry point
|   |-- api/                 Future USSD and webhook HTTP endpoints
|   |-- config/              Future environment validation
|   |-- services/            Transfer workflow and payment rules
|   |-- integrations/
|   |   |-- btcpay/          Bitcoin invoices and payment verification
|   |   |-- telecom/         USSD gateway and SMS provider
|   |   `-- payouts/         KES payout provider adapters
|   |-- storage/             Durable transfer and event records
|   `-- workers/             Background processing and reconciliation
|-- tests/                   Test plan and future automated tests
|-- docs/
|   |-- architecture.md     Component responsibilities and money flow
|   |-- roadmap.md          Implementation checklist
|   `-- operations.md       Development and deployment notes
|-- .editorconfig
|-- .env.example
|-- .gitignore
|-- .nvmrc
`-- package.json
Pesapipe/
|-- README.md
`-- pesapipe/
    |-- src/
    |   |-- cli/                 Terminal entry point
    |   |-- api/                 USSD and webhook endpoints
    |   |-- config/              Environment validation
    |   |-- services/            Transfer workflow and payment rules
    |   |-- integrations/
    |   |   |-- btcpay/          Bitcoin invoice adapter
    |   |   |-- telecom/         USSD and SMS adapters
    |   |   `-- payouts/         KES payout adapters
    |   |-- storage/             Durable transfer and event records
    |   `-- workers/             Background jobs and reconciliation
    |-- tests/                   Test plan and future automated tests
    |-- docs/                    Architecture, roadmap, and operations
    |-- .editorconfig
    |-- .env.example
    |-- .gitignore
    |-- .nvmrc
    `-- package.json
```

Each reserved component has a README describing what belongs there. Add code
as each feature is built, rather than maintaining empty implementations.
Component folders currently contain responsibility notes, except for the small
CLI entry point. They reserve space for implementation without implying that
the payment integrations already exist.

## Roadmap

- [x] Establish the project structure and configuration templates.
- [x] Add a runnable CLI status message and architecture documentation.
- [ ] Implement input validation, durable storage, and a mock transfer workflow.
- [ ] Add BTCPay test invoices and settlement verification.
- [ ] Integrate one KES payout sandbox with idempotency and reconciliation.
- [ ] Add USSD menus, SMS delivery, and transfer status retrieval.
- [ ] Add workflow tests, monitoring, deployment, and recovery procedures.
- [ ] Resolve provider onboarding, funding, and operating requirements before
  a live pilot.

## Documentation

- [Architecture](pesapipe/docs/architecture.md): component responsibilities and
  payment reliability requirements.
- [Implementation roadmap](pesapipe/docs/roadmap.md): development milestones.
- [Operations](pesapipe/docs/operations.md): configuration and hosting notes.
