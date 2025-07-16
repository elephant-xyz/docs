# Stage 7: Updating

**Objective:** Detect and apply updates to property data in near real time.

The Elephant Protocol is designed to keep property data as current as possible. This is achieved through a continuous monitoring and updating process.

## Monitoring for Changes

Oracles are responsible for continuously monitoring the `source_http_request`s provided in the initial seed data. They periodically check for any changes to the data at these sources.

## Update Cycle

If an oracle detects a change in the source data, a new update cycle is triggered. This cycle follows the same core principles as the initial data submission process:

1.  **Ingestion**: The updated data is ingested from the source.
2.  **Conversion**: The new data is converted to the Lexicon schema.
3.  **Minting**: A new Merkle root hash is generated and committed to the smart contract.

This process ensures that the protocol always has the most up-to-date information available, with a complete, versioned history of all changes.
