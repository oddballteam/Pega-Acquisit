# Acquisit: Pega acquisition and supply chain application

Acquisit is a Pega application that models acquisition and supply chain work for a
federal agency: procurement requests, contract management, budget allocation, supplier
onboarding and performance, inventory, logistics, compliance audits and asset disposal.
It was generated with Pega GenAI Blueprint, then exported from a Pega Community Edition
instance on 2026-08-25. It is a demonstration application, not a production system, and
it contains no real agency data.

## What's in this repo

| Path | What it is | Readable? |
|---|---|---|
| `export/Acquisit_010101_20260825T232326_GMT.zip` | The full application archive (RAP), 12,858 rule instances. You can import this into a Pega instance. | Binary (Pega format) |
| `blueprint/*.blueprint` | The Pega GenAI Blueprint file the application was generated from. You can import it into Pega Blueprint. | Binary (Pega format) |
| `inventory/SUMMARY.md` | Case types, their flows, data types, and rule counts by type. | Yes |
| `inventory/rules.csv` | One row per rule (2,482 rows). Version-history and tag records are left out. | Yes |
| `inventory/rules-all.csv` | Every instance in the archive (12,858 rows), version history included. | Yes |
| `inventory/by-type/*.csv` | `rules.csv` split into one file per rule type, such as `RULE-OBJ-FLOW.csv` or `RULE-DECLARE-DECISIONTABLE.csv`. | Yes |
| `inventory/contents.txt` | The archive's own rule manifest, unmodified (the source for every CSV). | Yes |
| `schema/` | Database table definitions for the work and data classes, from the archive's schema jar. | Yes (XML) |
| `edge-assessment/visual-mockup-proposal.html` | An EDGE assessment output from 2026-08-25 that proposes future-state screens on Pega, Salesforce and ServiceNow. It was generated before EDGE had a live Pega connector, so it is based on the application description, not on these rules. | Yes (open in a browser) |
| `scripts/extract.py` | Rebuilds `inventory/` and `schema/` from the archive. Uses only the Python standard library. | Yes |

### What the readable files don't cover

Inside the archive, the rule bodies (`instances_NNNNN.bin` in `*_rules.jar`) are stored
in Pega's own binary format, which isn't documented. The repo gives the complete list of
rules, with each rule's name, type, class, ruleset and timestamp. It does not give their
logic, such as decision-table rows, flow steps or view layouts. To get that, import the
archive into Pega and inspect it there, or use the Pega DX API on a running instance.

## The application at a glance

- **Pega version:** exported from Pega 26.1 (`PRPC-26.1.0`, engine 08-26-01).
- **Application:** `Acquisit` 01.01.01, built on the `MyOrg` / `MyOrgInt` layers.
- **Rulesets:** `Acquisit`, `MyOrg` and `MyOrgInt`, plus their `_Branch_BP-2` Blueprint branches. Most rules are in `Acquisit_Branch_BP-2`.
- **19 case types**, under `MyOrg-Acquisit-Work-*`: Acquisition Oversight, Asset Disposal, Budget Allocation, Compliance Audit, Contract Management, Demand Forecasting, Dispute Resolution, Emergency Procurement, Inventory Management, Logistics Coordination, Order Fulfillment, Procurement Request, Quality Assurance, Risk Assessment, Sourcing Evaluation, Stakeholder Collaboration, Supplier Onboarding, Supplier Performance and Supplier Renewal.
- **6 data types**, under `MyOrg-Acquisit-Data-*`: Compliance Document, Inventory Item, Procurement Contract, Purchase Order, Stakeholder and Supplier.
- **Personas and access groups:** Compliance Analyst, Finance Coordinator, Procurement Officer, Program Beneficiary, Supply Chain Manager, Vendor Representative, plus Users and Authors.
- **Counts:** 139 flows, 94 flow actions, 32 decision tables, 82 declare expressions, 337 views, 82 report definitions, 104 notifications and 1 AI agent (`AcquisitAgent`).

`inventory/SUMMARY.md` lists every flow for each case type.

## Importing into Pega

The archive imports into Pega 26.1 or later. You can import it in either of two ways:

- **Dev Studio:** Configure → Application → Distribution → Import, then pick the zip.
- **REST:** `POST /prweb/PRRestService/SystemManagement/v2/Import` as multipart/form-data.
  Send two parts: the zip itself, in a part named after its filename, and a single
  `jsonBody` text part that holds the import settings. Authenticate with HTTP Basic Auth
  as an operator who can import. An OAuth bearer token is rejected. Add `async=true` to
  get a job ID, then poll `GET .../v2/GetStatus?jobID=<id>&operationName=Import`.

A trial import into a clean Community Edition instance took about 5 minutes: 12,849
instances committed, 9 skipped, 0 errors.

## Regenerating the inventory

```sh
python3 scripts/extract.py
```

The script reads the single `.zip` in `export/` and rewrites `inventory/` and `schema/`.
