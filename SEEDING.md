# Elephant Protocol Seeding Guide

This guide details the process of "seeding" the Elephant Protocol with new property data. Seeding is the foundational step where oracles source, format, and submit real estate data to the network. Seed data represents a minimal set of information, yet it is sufficient to uniquely identify a property.

## Prerequisites

Before you begin, ensure you have completed the following:

- **[Setup Guide](./SETUP.md)**: You must have a configured environment with a Google account, Pinata JWT, MetaMask wallet, and POL tokens.
- **[Tech Stack Review](./TECH_STACK.md)**: Familiarize yourself with the core technologies, including IPFS, IPLD, and JSON Canonicalization. This guide assumes you understand these concepts.

## The Seeding Process

Seeding involves finding reliable property data, structuring it according to the Elephant Protocol's schema, and submitting it to the blockchain.

### Step 1: Identify a Data Source

Your first task is to find a reputable source for property data. The best sources are official County Appraisal websites, which provide government-certified information.

Examples of reliable sources:

- **Palm Beach County, FL**: [https://www.pbcpao.gov](https://www.pbcpao.gov)
- **Lee County, FL**: [https://www.leepa.org](https://www.leepa.org)

To maximize your potential vMahout rewards, consider joining our [Discord](https://discord.com/invite/elephant-xyz) to coordinate with the community on which counties or states are high-priority targets.

### Step 2: Extract Property Data

Once you have a source, you need to extract the data required by the [Seed Data Group Schema](https://lexicon.elephant.xyz/). This typically includes the parcel ID and property address.

Extraction methods vary:

- **CSV Export**: Some sites offer a simple "download as CSV" option.
- **Search Queries**: You may need to perform searches (e.g., by postal code) and export the results.
- **Web Scraping**: For more complex sites, you might need to scrape HTML or analyze network requests to find a hidden API that returns structured JSON.

**Crucially, you must record the exact HTTP request used to obtain the data.** This information is required for data verification and future updates.

### Step 3: Structure and Link the Data (IPLD)

With the raw data in hand, you must now transform it into the IPLD format specified by the Elephant Protocol. This involves creating a set of linked JSON files.

As explained in the [Tech Stack Guide](./TECH_STACK.md), data is stored on IPFS and linked using IPLD. All data must be canonicalized using the **RFC 8785 JSON Canonicalization Scheme** before uploading to IPFS to ensure hash consistency.

Here is an example of the required structure:

**`root.json`**
This is the entry point, defining the data group.

```json
{
  "label": "Seed",
  "relationships": {
    "property_root": {
      "/": "bafkreia..."
    }
  }
}
```

**`relationship_property_to_address.json`**
This file links the property to its unnormalized address.

```json
{
  "from": {
    "/": "bafkreib..."
  },
  "to": {
    "/": "bafkreic..."
  }
}
```

**`property_root.json`**
Contains the core property identifier.

```json
{
  "parcel_id": "494207051020",
  "source_http_request": "POST /BcpaClient/search.aspx/getParcelInformation HTTP/1.1
Host: web.bcpa.net
Content-Type: application/json
Content-Length: 100

{"folioNumber":"494207051020","taxyear":"2025","action":"CURRENT","use":""}",
  "request_identifier": "494207051020"
}
```

**`unnormalized_address.json`**
Contains the property's address information.

```json
{
  "full_address": "3319 NW 69 COURT FORT, LAUDERDALE, FL",
  "source_http_request": "POST /BcpaClient/search.aspx/getParcelInformation HTTP/1.1
Host: web.bcpa.net
Content-Type: application/json
Content-Length: 100

{"folioNumber":"494207051020","taxyear":"2025","action":"CURRENT","use":""}",
  "request_identifier": "494207051020",
  "county_jurisdiction": "Broward"
}
```

> **Note**: The `"/"` values in the examples above are placeholders. In your actual data, these must be the real CIDs of the linked JSON files.

### Step 4: Validate and Upload to IPFS

Before submitting to the blockchain, you must validate your data against the official schemas and upload it to IPFS.

**Key Validation Points**:

- **Schema Compliance**: Ensure your JSON files comply with the latest schemas at [lexicon.elephant.xyz](https://lexicon.elephant.xyz/).
- **JSON Canonicalization**: Apply canonicalization to ensure predictable hashes.
- **CID Format**: Use CIDv1 with a raw codec and SHA-256 hashing.

> **Warning**: Submitting data that fails validation is futile. Other oracles will not be able to produce an identical hash, meaning your submission will never reach consensus.

### Recommended Tool: `elephant-cli`

To simplify this process, we have developed the `elephant-cli` tool. It is designed to handle data validation, canonicalization, and uploading in a single, streamlined workflow.

You can find the tool and usage instructions here:
[**elephant-cli on GitHub**](https://github.com/elephant-xyz/elephant-cli?tab=readme-ov-file#step-3-validate-and-upload-dry-run-first)

While we highly recommend using `elephant-cli`, you are free to develop your own tools. If you do, please consider sharing them with the community to help improve the protocol.

### Step 5: Submit to the Smart Contract

With your data validated and uploaded to IPFS, the final step is to submit the data hash to the consensus smart contract.

You will interact with the `submitBatchData` function on the **Consensus Smart Contract** at `0x525e59e4de2b51f52b9e30745a513e407652ab7c`.

The ABI for this function is as follows:

```json
[
  {
    "inputs": [
      {
        "components": [
          {
            "internalType": "bytes32",
            "name": "propertyHash",
            "type": "bytes32"
          },
          {
            "internalType": "bytes32",
            "name": "dataGroupHash",
            "type": "bytes32"
          },
          {
            "internalType": "bytes32",
            "name": "dataHash",
            "type": "bytes32"
          }
        ],
        "internalType": "struct IPropertyDataConsensus.DataItem[]",
        "name": "items",
        "type": "tuple[]"
      }
    ],
    "name": "submitBatchData",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  }
]
```

The function expects an array of `DataItem` structs, where each struct contains the SHA-256 hash of the canonicalized data group file. The smart contract uses this hash to form a Merkle DAG, with your submission as the root.

**Why a hash and not a CID?**
The EVM is optimized for 32-byte data, which is the exact size of a SHA-256 hash. Sending a full CID would be significantly more expensive in gas fees. The CID can be reconstructed off-chain from the hash and the standard CIDv1 format.

### Using `elephant-cli` for Submission

The `elephant-cli` also simplifies this final step. Refer to the documentation for instructions on how to submit your data to the contract:
[**elephant-cli: Submit to Contract**](https://github.com/elephant-xyz/elephant-cli?tab=readme-ov-file#step-2-submit-to-contract-dry-run-first)

---

Congratulations! You have now seeded the Elephant Protocol. Once two other oracles submit an identical hash, consensus will be reached, and you will be rewarded with vMahout tokens for your contribution.

