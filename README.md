# PesaPipe

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

The implementation targets Node.js and JavaScript. The table separates the
current setup from planned integrations and decisions still to be made.

| Layer | Technology | Purpose | Status |
| --- | --- | --- | --- |
| Runtime | ![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white) | Run the CLI and backend | Configured |
| Language | ![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black) | Application logic using ES modules | Configured |
| HTTP server | ![Express](https://img.shields.io/badge/Express-303030?style=for-the-badge&logo=express&logoColor=white) | USSD requests and provider webhooks | Planned |
| HTTP client | ![Native Fetch](https://img.shields.io/badge/Native_Fetch-007ACC?style=for-the-badge) | Call provider APIs | Planned |
| Terminal | ![qrcode-terminal](https://img.shields.io/badge/qrcode--terminal-6D28D9?style=for-the-badge) | Display invoice QR codes | Planned |
| Bitcoin payments | ![BTCPay Server](https://img.shields.io/badge/BTCPay_Server-51B13E?style=for-the-badge) | Create invoices and verify payment status | Planned |
| Payment network | ![Bitcoin Lightning](https://img.shields.io/badge/Bitcoin_Lightning-F7931A?style=for-the-badge&logo=bitcoin&logoColor=white) | Bitcoin payments in a compatible test environment | Planned |
| USSD and SMS | ![Africa's Talking](https://img.shields.io/badge/Africa%27s_Talking-E85D04?style=for-the-badge) | Phone menus and payment instructions | Planned |
| KES payouts | ![Provider To Be Selected](https://img.shields.io/badge/Provider-To_Be_Selected-64748B?style=for-the-badge) | Mobile money payouts through a verified sandbox | Undecided |
| Database | ![Storage To Be Selected](https://img.shields.io/badge/Storage-To_Be_Selected-64748B?style=for-the-badge) | Durable transfer, event, and payout records | Undecided |

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

## Planned Transfer Flow

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

These steps describe the intended behavior, not functionality available today.
Payment and payout completion times will depend on the selected networks and
providers.

## Architecture

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
cd pesapipe
npm start
npm run check
```

No dependency installation or API credentials are needed for the current
scaffold. The start command prints the project status and exits; the check
command validates the starter JavaScript syntax.

All environment files, including `.env.example`, are excluded from Git.
An existing local `pesapipe/.env.example` can be used for future configuration,
but it is not included in new clones or loaded by the starter CLI. Keep real
credentials in `pesapipe/.env` when configuration loading is implemented.

## Repository Structure

```text
Pesapipe/
|-- .gitignore
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
    |-- .nvmrc
    `-- package.json
```

Component folders currently contain responsibility notes, except for the small
CLI entry point. They reserve space for implementation without implying that
the payment integrations already exist.

## Task Board

| Phase | Task | Status |
| --- | --- | --- |
| Foundation | Establish project folders and configuration notes | ![Complete](https://img.shields.io/badge/Complete-15803D?style=for-the-badge) |
| Foundation | Add the CLI status message and architecture documentation | ![Complete](https://img.shields.io/badge/Complete-15803D?style=for-the-badge) |
| Local workflow | Implement input validation, durable storage, and mock transfers | ![Next](https://img.shields.io/badge/Next-0369A1?style=for-the-badge) |
| Bitcoin | Add BTCPay test invoices and settlement verification | ![Planned](https://img.shields.io/badge/Planned-6D28D9?style=for-the-badge) |
| Payouts | Integrate a KES sandbox, idempotency, and reconciliation | ![Planned](https://img.shields.io/badge/Planned-6D28D9?style=for-the-badge) |
| Phone access | Add USSD menus, SMS delivery, and transfer status | ![Planned](https://img.shields.io/badge/Planned-6D28D9?style=for-the-badge) |
| Reliability | Add workflow tests, monitoring, deployment, and recovery | ![Planned](https://img.shields.io/badge/Planned-6D28D9?style=for-the-badge) |
| Live pilot | Resolve provider onboarding, funding, and operating requirements | ![Pending Review](https://img.shields.io/badge/Pending_Review-B45309?style=for-the-badge) |

## Documentation

- [Architecture](pesapipe/docs/architecture.md): component responsibilities and
  payment reliability requirements.
- [Implementation roadmap](pesapipe/docs/roadmap.md): development milestones.
- [Operations](pesapipe/docs/operations.md): configuration and hosting notes.
