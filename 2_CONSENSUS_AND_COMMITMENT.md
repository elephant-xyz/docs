# Stage 2: Consensus & Commitment

**Objective:** Achieve agreement across oracles on seed data, convert it to the Lexicon schema, and commit its Merkle root to the smart contract.

This stage is the core of the seeding process, where raw data is transformed into a verifiable, on-chain record.

## 1. Convert to Lexicon Schema

With the raw data in hand, you must now transform it into the IPLD format specified by the Elephant Protocol. To get the latest schema and data examples, refer to [https://lexicon.elephant.xyz/](https://lexicon.elephant.xyz/). This process involves creating a set of linked JSON files.

As explained in the [Tech Stack Guide](./TECH_STACK.md), data is stored on IPFS and linked using IPLD. All data must be canonicalized using the **RFC 8785 JSON Canonicalization Scheme** before uploading to IPFS to ensure hash consistency.

> **Important**: The following examples are for illustrative purposes only. For the most up-to-date data formats and comprehensive examples, please refer to the official documentation at [https://lexicon.elephant.xyz/](https://lexicon.elephant.xyz/).

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

**`unnormalized_address.json`**
Contains the property's address information.

> **Note**: The `"/"` values in the examples above are placeholders. In your actual data, these must be the real CIDs of the linked JSON files.

## 2. Validate and Upload to IPFS

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

## 3. Submit to the Smart Contract for Consensus

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

## 4. Consensus and Commitment

Consensus is reached when three independent oracles submit identical data payloads.

Upon successful consensus, a Merkle DAG (Directed Acyclic Graph) is generated from the seed data. The root hash of this DAG is then committed to the smart contract. This on-chain commitment establishes the foundational identity of the property within the protocol's ledger, creating an immutable and verifiable record.
