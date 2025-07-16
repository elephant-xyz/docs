# Mining Process

## Overview

This document outlines the end-to-end mining process in the protocol, detailing the responsibilities of oracles and the mechanisms by which off-chain property data is validated, transformed, and anchored on-chain. Each stage contributes to maintaining the integrity, timeliness, and verifiability of decentralized property records.

---

## Process Stages

### 1. Seeding

**Objective:** Identify and define the minimum viable dataset (MVD) to initialize a property in the protocol.

**Procedure:**
- Oracles locate target properties using publicly available county metadata.
- Each seed includes essential identifiers like `parcel_id` and the `source_http_request` used to retrieve the data.
- Seeds are structured in canonical JSON format to ensure compatibility across oracle agents.

**[Learn more about Seeding](./1_SEEDING.md)**

### 2. Consensus & Commitment

**Objective:** Achieve agreement across oracles on seed data, convert it to the Lexicon schema, and commit its Merkle root to the smart contract.

**Procedure:**
- Multiple oracles independently validate, format, and submit the same seed data.
- Consensus is reached when a supermajority submits an identical data hash.
- The Merkle root of the data is committed on-chain, establishing the foundational property record.

**[Learn more about Consensus & Commitment](./2_CONSENSUS_AND_COMMITMENT.md)**

### 3. Ingestion

**Objective:** Acquire the full property data set from the seed’s source HTTP request.

**Procedure:**
- Oracles use standardized ingestion pipelines to retrieve the complete property dataset.
- Data is stored in an intermediate structure for transformation.

**[Learn more about Ingestion](./3_INGESTION.md)**

### 4. Conversion to Lexicon Schema (Full Dataset)

**Objective:** Normalize the full, ingested property dataset into the protocol-wide Lexicon format.

**Procedure:**
- The full dataset is converted into the Lexicon schema using shared tools and scripts.
- The output is a set of linked files representing distinct data classes.

**[Learn more about Conversion to Lexicon Schema](./4_CONVERSION_TO_LEXICON_SCHEMA.md)**

### 5. Merkle Hash Commitment (Minting)

**Objective:** Anchor the property’s full dataset to the blockchain via Merkle root hash.

**Procedure:**
- A deterministic Merkle DAG is built from the full, Lexicon-formatted data.
- The root hash is committed to the smart contract, finalizing the immutable property record.

**[Learn more about Merkle Hash Commitment](./5_MERKLE_HASH_COMMITMENT.md)**

### 6. Token Issuance

**Objective:** Reward oracle agents for successful contributions.

**Procedure:**
- Upon successful minting, the smart contract mints and distributes `vMahout` and `Mahout` tokens to the participating oracles.

**[Learn more about Token Issuance](./6_TOKEN_ISSUANCE.md)**

### 7. Updating

**Objective:** Detect and apply updates to property data in near real time.

**Procedure:**
- Oracles continuously monitor the `source_http_request` from the seed data.
- If a change is detected, a new ingestion-conversion-mint cycle is triggered to update the on-chain record.

**[Learn more about Updating](./7_UPDATING.md)**

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