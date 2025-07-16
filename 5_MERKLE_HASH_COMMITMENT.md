# Stage 5: Merkle Hash Commitment (Minting)

**Objective:** Anchor the property’s full dataset to the blockchain via Merkle root hash.

Before submitting to the blockchain, you must validate your data against the official schemas and upload it to IPFS.

**Key Validation Points**:

-   **Schema Compliance**: Ensure your JSON files comply with the latest schemas at [lexicon.elephant.xyz](https://lexicon.elephant.xyz/).
-   **JSON Canonicalization**: Apply canonicalization to ensure predictable hashes.
-   **CID Format**: Use CIDv1 with a raw codec and SHA-256 hashing.

> **Warning**: Submitting data that fails validation is futile. Other oracles will not be able to produce an identical hash, meaning your submission will never reach consensus.

### Recommended Tool: `elephant-cli`

To simplify this process, we have developed the `elephant-cli` tool. It is designed to handle data validation, canonicalization, and uploading in a single, streamlined workflow.

You can find the tool and usage instructions here:
[**elephant-cli on GitHub**](https://github.com/elephant-xyz/elephant-cli?tab=readme-ov-file#step-3-validate-and-upload-dry-run-first)

While we highly recommend using `elephant-cli`, you are free to develop your own tools. If you do, please consider sharing them with the community to help improve the protocol.

### Submit to the Smart Contract

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