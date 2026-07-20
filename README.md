# infisuite Reverse-Engineering Documentation

Full reverse-engineered functional + data-structure documentation of the infisuite-hosted business systems, built page-by-page and function-by-function from the live front end (no source code access — this is black-box reverse engineering via the UI).

## Systems in scope

| System | URL | Status |
|---|---|---|
| CRM (Bharatnet) | https://bharatnet.infisuite.in | pilot in progress |
| Billstack (Bharatnet) | https://bharatnet.infisuite.in | not started |
| CRM/Billstack (Citylinenetworks) | http://citylinenetworks.infisuite.in/ | complete (docs/cityline-crm/, docs/cityline-billstack/) |
| Contracts (Citylinenetworks) | http://citylinenetworks.infisuite.in/contracts | not started |
| Ticketing (Citylinenetworks) | http://citylinenetworks.infisuite.in/ticketing | not started |
| Indyanet (type TBD — identified via exploration) | https://indyanet.infisuite.in/ | not started — 3 logins to explore (abhishek@indyanet.com, aitad@bbtel.in, shrinad@indyanet.com), document access differences per account |

## Structure

Each system gets its own folder under `docs/<system-slug>/` and `screenshots/<system-slug>/`. Notes should cover, per page/module/function: purpose, data fields (name/type/required/validation), data tables involved, business logic and conditions, inputs and outputs, and screenshots of each distinct state.

Login credentials for these systems are NOT stored in this repo. They live in the AI-OS task prompt.txt on the dispatching server only.
