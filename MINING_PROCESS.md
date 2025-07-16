# Mining Process

## Overview

This document outlines the end-to-end mining process in the protocol, detailing the responsibilities of oracles and the mechanisms by which off-chain property data is validated, transformed, and anchored on-chain. Each stage contributes to maintaining the integrity, timeliness, and verifiability of decentralized property records.

---

## Process Stages

### 1. Seeding

**Objective:** Identify and define the minimum viable dataset (MVD) to initialize a property in the protocol.

**Procedure:**

- Oracles locate target properties using publicly available county metadata.
- Each seed includes:
  - `parcel_id`
  - `property_address`
  - `source_url`
  - `county_jurisdiction`
- Seeds are structured in canonical JSON format to ensure compatibility across oracle agents.
- DAO-published county priorities dictate jurisdictional mining order.

### 2. Consensus & Commitment

**Objective:** Achieve agreement across oracles on seed data and commit its Merkle root to the smart contract.

**Procedure:**

- Multiple oracles independently validate the same seed data.
- Consensus is reached when >50% submit identical seed payloads.
- A Merkle DAG is generated from the seed and the root hash is committed on-chain.
- This establishes the foundational property identity within the protocol ledger.

### 3. Ingestion

**Objective:** Acquire the full property data set from the seed’s source URL.

**Procedure:**

- Oracles use standardized ingestion pipelines to retrieve data such as:
  - Owner information
  - Parcel and tax data
  - Assessed values
  - Structural and zoning details
- Supported by shared tools: API connectors, parsers, and scraping agents.
- Data is stored in an intermediate structure for transformation.

### 4. Conversion to Lexicon Schema

**Objective:** Normalize ingested data into the protocol-wide Lexicon format.

**Procedure:**

- Lexicon is a domain-specific schema designed for cross-jurisdictional consistency.
- Conversion is performed via:
  - Rule-based transformation scripts
  - AI-assisted mapping agents
  - CLI utilities available in the GitHub repository
- Output: A set of files for each property, where each file represents a distinct class or data group.

### 5. Merkle Hash Commitment (Minting)

**Objective:** Anchor the property’s full dataset to the blockchain via Merkle root hash.

**Procedure:**

- Oracles build a deterministic Merkle DAG from the Lexicon-formatted data.
- The CLI command `validate-and-upload` validates extracted data against the lexicon and uploads it to IPFS.
- The CLI command `submit-to-contract` commits the root hash to the protocol smart contract.
- This step finalizes the creation of an immutable, versioned record for the property.

### 6. Token Issuance

**Objective:** Reward oracle agents for successful contributions.

**Procedure:**

- Upon successful minting, the smart contract mints:
  - `vMahout`: non-transferable token attesting to verified contributions.
- Upon data usage by the protocol providers
  - `Mahout`: transferable token representing economic and governance rights.
- Tokens are automatically distributed to the oracle’s registered wallet.

### 7. Updating

**Objective:** Detect and apply updates to property data in near real time.

**Procedure:**

- Oracles continuously monitor seed `source_url`s.
- If the new Merkle root differs from the latest on-chain record:
  - A new ingestion-conversion-mint cycle is triggered.
  - Updated records are committed, ensuring continuity and versioning.
- The update loop enables high-fidelity tracking of changes in county records.

---

## CLI & Automation Notes

- All major steps (`validate-and-upload`, `submit-to-contract`) are automatable via CLI.
- AI-powered transformation is optional but recommended for complex jurisdictions.

---

## Reward Summary

| Stage        | Token Output | Description                          |
| ------------ | ------------ | ------------------------------------ |
| Mint Success | Mahout       | Economic token for staking/utility   |
| Mint Success | vMahout      | Proof-of-contribution (non-transfer) |

---

## Security & Consensus Guarantees

- Merkle DAG generation ensures data immutability and versioning.
- Multi-oracle validation mitigates bad data injection and single-point failure.
- Token rewards align oracle incentives with protocol data integrity.

---

## Future Extensions

- Integration of ZK proofs for ingestion verification.
- DAO-based oracle slashing for repeated invalid submissions.
- Automated jurisdictional prioritization via market demand signals.

---

## License

Copyright 2025 Elephant.xyz
Licensed under the Apache License, Version 2.0 (the “License”); you may not use this file except in compliance with the License.
You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0
