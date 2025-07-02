# Elephant

Elephant is decentralized real estate market powered by the data provided by the distributed oracles network.

Elephant relies on the oracles providing data from the trusted sources independently, reaching consensus on the data state and therefore certifying the data.

# Tech stack

Elephant protocl is build on top of next set of technologies:

- Polygon POS blockchain
- Smart contracts
- IPFS
- IPLD
- Schemalink (custom JSON Scehma immplementation)
- RFC 8785 JSON Canonicalization Scheme (JCS)

## Why Polygon POS blockchain

[Polygon POS](https://polygon.technology/polygon-pos) blockchain is a sidechain, that aims to solve scalability issue. You can read more about approaches that it takes to get there on their docuemntation. Perfect example of what does it means from the gas perspective can be comparing how much does it cost to [transfer 1 USDT](https://gasfeesnow.com/). As the protocol is about to have hunders of millions of transactions, this gas saving becomes critical.
If you are new to the blockchain we highly recomend finishing [this free course](https://updraft.cyfrin.io/courses/blockchain-basics) to understed the fundmentals and be able to anser yourself a qustion: `Why blockchain`

## Why smart contracts

By using smart contracts we are able to inherit all that good from the blockchain that we need:

- Immutability
- Trustless
- Verifiable

To achieve that we are utilizing smart contracts to be able to implement our custom logic on how to agree on state of the data (consensus) and how to save it.
At the point of writing this Elephant protocol consists of the next smart contracts:

- [Consensus smart contract](https://polygonscan.com/address/0x525e59e4de2b51f52b9e30745a513e407652ab7c)
- [Oracle assignement smart contract](https://polygonscan.com/address/0xb93AcE67A9017D526245E165ADf361dF9588d3fB)
- [vMahount, which is governence token smart contract](https://polygonscan.com/token/0x3b3ad74ff6840fa5ff5e65b551fc5e8ed13c3f18)

Contracts to be created:

- Mahount smart contract
- Governence smart contracts

Each of the smart contracts has verified code on polygonscan and will be open sourced

### vMahout

vMahout smart contract is being used as a non-transfarable, no mint limit governence token, that is beinged minted by the consensus smart contract to the oracles, who provided the data and reached consensus.

### Oracle assignement

Oracle assignement smart contracts act as a mechanism of Elephant DAO to signal to the oracles set of properties, that needs to be extracted by them. This smart contract will be used during early days of the protocol and will be archived once the protocol achieves critical mass

### Consensus

Consensus smart contract is the contract that oracles interacat with to submit the extracted data. Each oracle submits hash of extracted data and once 3 oracles submits same data hash, data is considered truthfull and oracles are getting their rewards.

#### Why data hashes are submitted and not the data?

Even though the Polygon POS is selected, blockchain still remains expensive, when we want to have hunders of millions of transactions. Therefore to reduce gas cost instead of submitting the actual data, oracles submits data hash, therefore dramatically reducing computataion and storage needed for the smart contract. The actual data will be saved to the [IPFS](https://ipfs.tech/).

## Why IPFS

IPFS provides ability to host and retreieve the data in the decentralized way. Core concept of an IPFS is CID(Content Identifier) which serves the purpose of identifing and retreieving the files. CID is dervied from the content hash, which provides ud immutability and allows to easily retreieve file when all we know about it is it's content hash, which in itself combines great with the fact, that only data hashes are submitted to the smart contract. This way once data reaches consensus based on it's hash we can easily get it from IPFS by computin CID. As of now protocol expects everyone to save data to IPFS with CID v1, raw codec (0x55) with sha2-256 hashing (0x12). To understed more about IPFS we highly recomend to visit [the docs](https://docs.ipfs.tech/) or complete this [free IPFS course](https://proto.school/course/ipfs)

## Why IPLD

IPLD is a standart, that allows to represent linked data using IPFS files. This is achieved by including other CID in the data obect using special syntax. Example of a JSON object, that is using IPLD would be

```json
{
  "person": {
    "/": "bafkreifds5n42ksz4q4okkpbdpnmvlzlprtb6uo5ak47rcsfj6aoxg6tdm"
  }
}
```

This eventually results in creation of Mergle DAG, where hashes of one files impacts hashes of other files

```mermaid
the mermaid diagram should be there
```

We are using IPLD for 2 sequantual resons:

1. Real estate data tends to be highly linked, so it makes total sense to use CID as entity identifers to build relationhips between enteties
2. By using CID as links we are not re-creating the data as same data has same CID, therefore it does not need to be saved multiple times on IPFS nodes, which in itself requires less resources to persist same value of data.

We highly recomend to go trough [this free course](https://proto.school/course/ipld) on IPLD to understand it better

## Why Schemalink and what is Schemalink?

Schemalink is a dialect of [JSON schema](https://json-schema.org/overview/what-is-jsonschema) that we had to create to be able to fit usage of an IPLD and be more type and format sctrict.
First we highlt recomend to educate yourself on a JSON schema using [official documentation](https://json-schema.org/overview/what-is-jsonschema) so that you can better understand the things that re added to it in the Schemalink.

### Custom formats in Schemalink

The Schemalink really aims to address 2 issues:

1. Support likned data. As we are using IPLD to represent the data we need custom dialect, that will be able to validate those links and therefore allow schema to be Merkle DAG itself.
2. Tighten data formats. As we are comparing the data hashes and any extra comma chanes the hash it is critically important for the protocol to have strict rules on how the data should be formated.

Custom formats introduced by Schemalink:

- `cid` keyword: Schema can reference other schema by CID. Referencing means, that the actual data should be an IPLD link itself
  Example:

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

- `currency`

Validates positive numeric values with a maximum of 2 decimal places. Suitable for monetary amounts like prices, costs, or fees.

**Type**: `number`

**Validation Rules**:

- Must be greater than 0
- Maximum 2 decimal places
- No special values (NaN, Infinity)

**Valid Examples**:

- `100`
- `100.50`
- `0.01`
- `999999.99`

**Invalid Examples**:

- `0` (must be greater than 0)
- `-100` (negative values not allowed)
- `100.123` (3 decimal places)
- `"100"` (string, not number)

**Schema Example**:

```json
{
  "type": "number",
  "format": "currency"
}
```

- date

Validates ISO 8601 date format (YYYY-MM-DD).

**Valid Examples**:

- `2024-01-01`
- `2023-12-31`
- `2020-02-29` (leap year)

**Schema Example**:

```json
{
  "type": "string",
  "format": "date"
}
```

- uri

Validates HTTP/HTTPS URLs with specific pattern requirements. This overrides the default `uri` format.

**Pattern**: `^https?://([\w-]+@)?[\w-]+(\.[\w-]+)+([\w\-.,@?^=%&:/~+#]*[\w\-@?^=%&/~+#])?$`

**Valid Examples**:

- `http://example.com`
- `https://sub.example.com`
- `https://example.com/path`
- `https://user@example.com`
- `https://example.com:8080/path?query=value#anchor`

**Schema Example**:

```json
{
  "type": "string",
  "format": "uri"
}
```

- ipfs_uri

Validates IPFS URIs with Content Identifiers (CIDs).

**Pattern**: `^ipfs://[A-Za-z0-9]{46,59}$`

**Additional Validation**:

- Validates that the CID portion is a valid IPFS CID
- For CIDv1, requires raw codec (0x55) with SHA-256 hash
- CIDv0 is not accepted

**Valid Examples**:

- `ipfs://bafkreihdwdcefgh4dqkjv67uzcmw7ojee6xedzdetojuzjevtenxquvyku` (CIDv1 raw)
- `ipfs://bafkreiggtrptmp32pl3to7x2tw5eedceyfld6sv25dlcdro6lowvxc5ili`

**Invalid Examples**:

- `ipfs://QmYjtig7VJQ6XsnUjqqJvj7QaMcCAwtrgNdahSiFofrE7o` (CIDv0)
- `ipfs://baguqeeraevt2kit5iquvk554xn7jfr63skcsixiipv3wyexx65g7vyqh5rsq` (wrong codec)

**Schema Example**:

```json
{
  "type": "string",
  "format": "ipfs_uri"
}
```

- rate_percent
  Validates interest rate percentages with exactly 3 decimal places.

**Pattern**: `^\d+\.\d{3}$`

**Valid Examples**:

- `5.250`
- `0.000`
- `10.375`
- `100.000`

**Schema Example**:

```json
{
  "type": "string",
  "format": "rate_percent"
}
```

## Why JSON Canonicalization Scheme

JSON Canonicalization Scheme is an algorithm of producing predicateble JSON by utilizing compacting, sorting etc. This is critically important for the protocol as difference in formatiing and ordering will produce different hashes even when data itself is the same, as smart contract comapres hashes to reach consensus this would have been major issue. To know more about this algorithm please refer to the [RFC](https://www.rfc-editor.org/rfc/rfc8785). For the list of libaries refer to this [GH repo](https://github.com/cyberphone/json-canonicalization)

The one problem, that JCS leaves unandressed is sorting in the arrays, as in some data structures the ordering has meaning in intself. It is not the case for us, so on top of doing JCS oracles has to sort arrays with relationhips as weell, using alphabetical order of the link as sorting function. F.e

**Input:**

```json
{
  "links": [
    { "/": "bafybeigdyrzt5sfp7udm7hu76uh7y26nf3efuylqabf3oclgtqy55fbzdi" },
    { "/": "bafybeibazaarhe5qpbgvfwqnteba5hbgzvqcajqgfgxnhpdvfnqweabk4u" },
    { "/": "bafybeifx7yeb55armcsxwwitkymga5xf53dxiarykms3ygqic223w5sk3m" }
  ]
}
```

**Canonical Output:**

```json
{
  "links": [
    { "/": "bafybeibazaarhe5qpbgvfwqnteba5hbgzvqcajqgfgxnhpdvfnqweabk4u" },
    { "/": "bafybeifx7yeb55armcsxwwitkymga5xf53dxiarykms3ygqic223w5sk3m" },
    { "/": "bafybeigdyrzt5sfp7udm7hu76uh7y26nf3efuylqabf3oclgtqy55fbzdi" }
  ]
}
```
