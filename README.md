# PesaPipe

A planned backend service for requesting Bitcoin payments and coordinating
Kenyan shilling (KES / Ksh) payouts to mobile money recipients. Users will access
it through a terminal or USSD menu, with SMS for payment instructions.

## Tech Stack

Color badges identify each technology; the status column distinguishes today's
setup from planned integrations.

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

Badges load from Shields.io and require an internet connection.

No custom smart contracts are planned. CI/CD will check and deploy code; the
payment service and background workers will run independently of CI jobs.

## Status

Project setup only: organized folders, configuration templates, documentation,
and a tiny runnable CLI. Payment integrations are not implemented. This is a
foundation for a product, not a production-ready payment service.

## Run Today

Use Node.js 22 or newer. No dependencies or API keys are needed for this scaffold.

```sh
cd /home/omar/Pesapipe/pesapipe
npm start
npm run check
```

`.env.example` documents future settings. Keep real credentials in an ignored
`.env` file when configuration loading is implemented.

## Folder Guide

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
```

Each reserved component has a README describing what belongs there. Add code
as each feature is built, rather than maintaining empty implementations.
