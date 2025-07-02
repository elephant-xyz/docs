## Understanding the Technology Stack

Before diving into the specifics, let's understand why we've chosen each piece of our technology puzzle. Every choice we've made optimizes for three critical factors: cost efficiency, data integrity, and decentralization.

Our foundation consists of:

- **Polygon POS blockchain** - For affordable, scalable transactions
- **Smart contracts** - For trustless consensus mechanisms
- **IPFS** - For decentralized data storage
- **IPLD** - For linking related data efficiently
- **Schemalink** - Our custom JSON Schema implementation for strict data validation
- **RFC 8785 JSON Canonicalization** - For consistent data formatting

Let's explore each component and understand why it's essential for your success as an oracle.

## Why Polygon POS Blockchain?

When dealing with hundreds of millions of real estate transactions, every penny matters. [Polygon POS](https://polygon.technology/polygon-pos) is a sidechain solution that dramatically reduces transaction costs while maintaining security and decentralization.

To put this in perspective, [transferring 1 USDT](https://gasfeesnow.com/) on Ethereum might cost several dollars, while on Polygon it costs fractions of a cent. For oracles submitting data frequently, this difference transforms from a minor consideration to a critical economic factor.

If you're new to blockchain technology, we highly recommend completing [this free blockchain fundamentals course](https://updraft.cyfrin.io/courses/blockchain-basics). It will help you understand not just the "how" but the crucial "why" behind our blockchain choice.

## Smart Contracts: The Trust Engine

Smart contracts are the beating heart of Elephant's trustless architecture. They provide three fundamental guarantees:

- **Immutability**: Once data reaches consensus, it cannot be altered
- **Trustlessness**: No single party controls the system
- **Verifiability**: Anyone can audit the process

Currently, the Elephant protocol operates through these smart contracts:

### Active Contracts

1. **[Consensus Smart Contract](https://polygonscan.com/address/0x525e59e4de2b51f52b9e30745a513e407652ab7c)**: Where oracles submit data hashes and consensus is reached
2. **[Oracle Assignment Smart Contract](https://polygonscan.com/address/0xb93AcE67A9017D526245E165ADf361dF9588d3fB)**: Coordinates which properties need data extraction
3. **[vMahout Token Contract](https://polygonscan.com/token/0x3b3ad74ff6840fa5ff5e65b551fc5e8ed13c3f18)**: Governance token rewarding participating oracles

### Upcoming Contracts

- **Mahout Smart Contract**: Main protocol token
- **Governance Smart Contracts**: Decentralized decision-making infrastructure

All contracts feature verified code on Polygonscan and will be fully open-sourced, ensuring complete transparency.

### Understanding vMahout Tokens

As an oracle, you'll earn vMahout tokens—non-transferable governance tokens minted by the consensus contract. These tokens represent your contribution to the network and grant voting rights in protocol decisions. Think of them as proof of your expertise and commitment to data accuracy.

### Oracle Assignment Process

The Oracle Assignment contract serves as your mission board. The Elephant DAO uses this contract to signal which properties need data extraction. During the protocol's early phase, this ensures efficient resource allocation. As the network grows and reaches critical mass, this contract will be phased out in favor of more decentralized mechanisms.

### The Consensus Mechanism

Here's where the magic happens. The Consensus contract is your primary interaction point as an oracle. The process works like this:

1. You extract property data from trusted sources
2. You submit a hash of that data to the contract
3. Once three oracles submit identical hashes, consensus is reached
4. Participating oracles receive vMahout rewards

But why submit hashes instead of the actual data? Even on Polygon's efficient network, storing large amounts of data on-chain becomes expensive at scale. By submitting only data hashes, we reduce gas costs by over 90% while maintaining complete data integrity. The actual data lives on IPFS, accessible to anyone who needs it.

## IPFS: Decentralized Data Storage

[IPFS (InterPlanetary File System)](https://ipfs.tech/) provides the perfect complement to our blockchain architecture. While the blockchain stores proof of consensus, IPFS stores the actual property data in a decentralized manner.

The key concept here is the Content Identifier (CID). When you save data to IPFS, it generates a unique CID based on the content's hash. This creates an elegant system:

1. Oracles reach consensus on a data hash
2. That hash corresponds to a specific CID on IPFS
3. Anyone can retrieve the exact data using that CID

For Elephant protocol, we standardize on CID v1 with raw codec (0x55) and SHA-256 hashing (0x12). This ensures consistency across all oracles.

To master IPFS, explore [the official documentation](https://docs.ipfs.tech/) or complete this [free IPFS course](https://proto.school/course/ipfs).

## IPLD: Linking Data Intelligently

Real estate data is inherently interconnected—properties link to lots, lots to neighborhoods, neighborhoods to cities. [IPLD (InterPlanetary Linked Data)](https://proto.school/course/ipld) allows us to represent these relationships efficiently.

Here's a simple example of IPLD in action:

```json
{
  "person": {
    "/": "bafkreifds5n42ksz4q4okkpbdpnmvlzlprtb6uo5ak47rcsfj6aoxg6tdm"
  }
}
```

The `"/"` syntax indicates this is a link to another IPFS object. This creates a Merkle DAG (Directed Acyclic Graph) where changing any piece of data ripples through all connected data, ensuring integrity across the entire dataset.

Benefits for real estate data:

1. **Natural relationships**: Properties naturally reference lots, owners, and transactions
2. **Storage efficiency**: Identical data shares the same CID, eliminating duplication
3. **Data integrity**: Changes in linked data are immediately detectable

## Schemalink: Precision in Data Structure

Schemalink is our custom dialect of [JSON Schema](https://json-schema.org/overview/what-is-jsonschema) designed specifically for blockchain-based linked data. Understanding [standard JSON Schema](https://json-schema.org/overview/what-is-jsonschema) first will help you appreciate our enhancements.

### Why We Created Schemalink

Two critical needs drove Schemalink's development:

1. **IPLD Support**: Validate linked data structures within schemas
2. **Hash Consistency**: Ensure identical data always produces identical hashes

### Custom Format Types

#### CID References

The `cid` keyword enables schemas to reference other schemas via CID, creating a self-validating data network:

```json
{
  "$schema": "https://json-schema.org/draft-07/schema#",
  "additionalProperties": false,
  "description": "JSON Schema for relationship from property to lot",
  "properties": {
    "from": {
      "cid": "bafkreif2rpspl2kynjni5i7jfhyruji2fwz3pj6x2kxfoexv2zl2674kn4",
      "description": "Reference to property class schema",
      "type": "string"
    },
    "to": {
      "cid": "bafkreidrmaeuwha6gulienlwweibgfpm7uecktz26332nk75h6cmckfjp4",
      "description": "Reference to lot class schema",
      "type": "string"
    }
  },
  "required": ["from", "to"],
  "title": "property_to_lot",
  "type": "object"
}
```

#### Currency Format

For monetary values, we enforce precision:

```json
{
  "type": "number",
  "format": "currency"
}
```

- Must be positive
- Maximum 2 decimal places
- Valid: `100`, `100.50`, `999999.99`
- Invalid: `0`, `-100`, `100.123`

#### Date Format

Standard ISO 8601 dates:

```json
{
  "type": "string",
  "format": "date"
}
```

Valid example: `2024-01-01`

#### URI Format

HTTP/HTTPS URLs with specific validation:

```json
{
  "type": "string",
  "format": "uri"
}
```

Pattern: `^https?://([\w-]+@)?[\w-]+(\.[\w-]+)+([\w\-.,@?^=%&:/~+#]*[\w\-@?^=%&/~+#])?$`

#### IPFS URI Format

For IPFS content references:

```json
{
  "type": "string",
  "format": "ipfs_uri"
}
```

- Pattern: `^ipfs://[A-Za-z0-9]{46,59}$`
- Validates CID format (CIDv1 with raw codec only)
- Valid: `ipfs://bafkreihdwdcefgh4dqkjv67uzcmw7ojee6xedzdetojuzjevtenxquvyku`

#### Rate Percent Format

For interest rates with exact precision:

```json
{
  "type": "string",
  "format": "rate_percent"
}
```

- Pattern: `^\d+\.\d{3}$`
- Valid: `5.250`, `10.375`, `0.000`

## JSON Canonicalization: Ensuring Consistency

[RFC 8785 JSON Canonicalization Scheme](https://www.rfc-editor.org/rfc/rfc8785) ensures that identical data always produces identical hashes, regardless of formatting differences. This is crucial because even a single extra space would change the hash, breaking consensus.

The canonicalization process includes:

- Removing unnecessary whitespace
- Sorting object keys alphabetically
- Normalizing number representations

For implementation libraries, see this [GitHub repository](https://github.com/cyberphone/json-canonicalization).

### Array Sorting Extension

Standard JCS doesn't address array ordering, which matters for relationships. We extend JCS by requiring alphabetical sorting of link arrays:

**Before canonicalization:**

```json
{
  "links": [
    { "/": "bafybeigdyrzt5sfp7udm7hu76uh7y26nf3efuylqabf3oclgtqy55fbzdi" },
    { "/": "bafybeibazaarhe5qpbgvfwqnteba5hbgzvqcajqgfgxnhpdvfnqweabk4u" },
    { "/": "bafybeifx7yeb55armcsxwwitkymga5xf53dxiarykms3ygqic223w5sk3m" }
  ]
}
```

**After canonicalization:**

```json
{
  "links": [
    { "/": "bafybeibazaarhe5qpbgvfwqnteba5hbgzvqcajqgfgxnhpdvfnqweabk4u" },
    { "/": "bafybeifx7yeb55armcsxwwitkymga5xf53dxiarykms3ygqic223w5sk3m" },
    { "/": "bafybeigdyrzt5sfp7udm7hu76uh7y26nf3efuylqabf3oclgtqy55fbzdi" }
  ]
}
```
