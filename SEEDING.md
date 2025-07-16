Prerequsits:

- [Setup](./SETUP.md) is completed
- [Tech stack](./TECH_STACK.md) is read. This section will not focus on what is blockchain, IPFS etc.

Seeding is the first and therefore exicting step of the process.
Seed data contains minimal, yet enough information to be able to idnetify the property, like parcel ID and property address. You can review the latest schema of the Seed data group https://lexicon.elephant.xyz/ and checking Seed data group.

## Step 1

Choose a properties you want the world to know about or you can do the whole county. Feel free to join our [Discord]() to find out what county/state can give you more vMahouts.

## Step 2

Find the County Appraisal website. This is the websites, that contains goverment-certified information about the properties. Few examples, so you can understand what to look for:

- Palm Beach County, Florida - [https://pbcpao.gov/index.htm](https://pbcpao.gov/index.htm)
- Lee County, Florid - [https://www.leepa.org/](https://www.leepa.org/)

## Step 3

Find out a way to get list of all properties you are interested it, with the information, that is needed to satisfy the seed data group. That can be simple csv file download, set of search queries by postal code and csv export or traverse executions of seraches and downloading data as HTML. It is also usefull to check the network requests, that the website is doing, it might be, that the county has an API, that returns data in semi-structured format like JSON, so you will be able to avoid HTML parsing and make your life easier.
It is also critical to record HTTP request, that you performed, as it should be part of the data for the sake of beinged verifiable and easily updatable.

## Step 4

Now it's time to convert the data to the format everybody loves and understands.
As you know from reading the [Tech Stack section](./TECH_STACK.md) data should be stored on the IPFS, use IPLD as linking mechanism and be compliant to the schema from [https://lexicon.elephant.xyz/](https://lexicon.elephant.xyz/).

So you need to convert raw data to the structure, that will look something like this:

Root file:

```json
{
  "label": "Seed",
  "relationships": {
    "property_root": {
      "/": "./relationship_property_to_address.json"
    }
  }
}
```

relationship_property_to_address.json

```json
{
  "from": {
    "/": "./property_root.json"
  },
  "to": {
    "/": "./unnormalized_address.json"
  }
}
```

property_root.json

```json
{
  "parcel_id": "494207051020",
  "source_http_request": "POST /BcpaClient/search.aspx/getParcelInformation HTTP/1.1\r\nHost: web.bcpa.net\r\nContent-Type: application/json\r\nContent-Length: 100\r\n\r\n{\"folioNumber\":\"494207051020\",\"taxyear\":\"2025\",\"action\":\"CURRENT\",\"use\":\"\"}",
  "request_identifier": "494207051020"
}
```

unnormalized_address.json

```json
{
  "full_address": "3319 NW 69 COURT FORT, LAUDERDALE, FL",
  "source_http_request": "POST /BcpaClient/search.aspx/getParcelInformation HTTP/1.1\r\nHost: web.bcpa.net\r\nContent-Type: application/json\r\nContent-Length: 100\r\n\r\n{\"folioNumber\":\"494207051020\",\"taxyear\":\"2025\",\"action\":\"CURRENT\",\"use\":\"\"}",
  "request_identifier": "494207051020",
  "county_jurisdiction": "Broward"
}
```

> [!IMPORTANT]
> Make sure to apply JSON canonicalization before uploading files to the IPFS. Canonicalizationallows to produce JSON with same key ordering and no escape chars, which allows to have way more predictable data and as a result way more predictable hash

In this example CIDs are intentonally replaced with file names to provide better visibility. In reallity those links values should be actual CIDs.

> [!IMPORTANT]
> Make sure to use CID v1 with base codec and SHA-256 hashing

It is important to validate your data using schemalink to check that your data satisfies schema requirements. Latest schemas definitions are available at [https://lexicon.elephant.xyz/](https://lexicon.elephant.xyz/).

> [!IMPORTANT]
> Unvalidated data will never reach consensus since submitting invalid data makes chances of producing same data near 0%.

---

## You can use (elephant cli)[https://github.com/elephant-xyz/elephant-cli?tab=readme-ov-file#step-3-validate-and-upload-dry-run-first] to go trough this step. You are free to use and modify it. You are also more, than welcome to create your own implementation of this step, but make sure to share it with the community to make the protocol better.

## Step 5

Now, that you have perfecly formatted data and it is uploaded to the IPFS it is time to submit transaction to the smart contract and let the world know the true state of the real estate.

To do this you need to `submitBatchData` function of a [0x525e59e4de2b51f52b9e30745a513e407652ab7c](https://polygonscan.com/address/0x525e59e4de2b51f52b9e30745a513e407652ab7c) smart contract. The ABI of this function is next:

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

You need to submit the SHA-256 hash value of a canonicalized data group file, that has CID as relationship value, and those CID has relationship file, that has CID as links to the enteties. This way Merkle DAG is created and you submit the root of it. The reason smart contract expects hash and not the actual CID is that SHA-256 hash is exacly 32 bytes long which fits perfecly into the EVM storage slot. If we would have to send the actual CID, we would have to use more storage slots and as result pay higher gas fees.
To calcualte the CID from the hashes, that are sent by other oracles or to review other data you need to contstruct CID v1 with base codec and sha-256 hash codec

---

You can use (elephant cli)[https://github.com/elephant-xyz/elephant-cli?tab=readme-ov-file#step-2-submit-to-contract-dry-run-first] to go trough this step. You are free to use and modify it. You are also more, than welcome to create your own implementation of this step, but make sure to share it with the community to make the protocol better.
