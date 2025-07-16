# Outsourcing Mahout Token Mining

## The Challenge: Securely Delegating Mining Operations

The process of mining Mahout tokens requires signing blockchain transactions with your private key. While effective for individual operators, this presents a significant security challenge if you wish to outsource or delegate your mining operations to a third party. Directly sharing your private key would grant them complete control over your funds and identity, which is not a recommended practice.

## The Recommended Pattern: A Key Management System

To solve this problem, we recommend implementing a key management system. Instead of sharing your private key, you would create a secure service that manages the key and exposes a limited, controlled API for signing transactions.

This approach offers several advantages:

-   **Security**: Your private key never leaves the secure environment of your key management system.
-   **Control**: You can implement strict validation rules on the types of transactions that can be signed.
-   **Access Management**: You can grant and revoke access to the signing API without changing the underlying key.
-   **Auditing**: The system can maintain a detailed log of all signing requests for a complete audit trail.

## Example API for Key Management

Below is an example of an OpenAPI specification for a service that could manage oracle keys and sign transactions. This API allows for the creation of aliased keys and provides an endpoint for submitting unsigned transactions to be signed and broadcasted, without ever exposing the private key itself.

```yaml
openapi: 3.0.3
x-explorer-enabled: true
info:
  title: Oracles
  description: API for generating Oracle keys and signing batch data on the blockchain
  x-product-family: Platform
  x-product-category: Data
  x-product-name: Oracles
  version: 1.0.0
servers:
  - url: 'https://your-key-management-api.com/oracles'
paths:
  /keys:
    post:
      operationId: createOracleKey
      x-product-component: 'Keys'
      x-product-endpoint: 'Create Oracle key'
      x-product-sequence: 1
      x-api-id:
        - "1b00f59c-1970-4073-9532-23f9ddbb5e12"
      summary: Create Oracle Key
      description: Create a new Oracle Key that allows signing transactions on the blockchain.
      responses:
        "201":
          description: Oracle key created successfully
          headers:
            Strict-Transport-Security:
              schema:
                type: string
              description: Strict Transport Security.
            x-sc-trace-id:
              schema:
                type: string
                pattern: "^[0-9A-HJKMNP-TV-Z]{26}$"
              description: Trace ID of the transaction
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/KeyResponse'
        "403":
          description: Forbidden
          headers:
            Strict-Transport-Security:
              schema:
                type: string
              description: Strict Transport Security.
            x-sc-trace-id:
              schema:
                type: string
                pattern: "^[0-9A-HJKMNP-TV-Z]{26}$"
              description: Trace ID of the transaction
  /submit-data:
    post:
      operationId: submitData
      x-product-component: 'Data'
      x-product-endpoint: 'Submit Data'
      x-product-sequence: 2
      x-api-id:
        - "fa2fa71e-49d5-4ca2-ba52-005b9e32b429"
      summary: Submit Data
      description: Submit data to the blockchain.
      requestBody:
        description: Data to submit
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/SubmitDataRequest'
            example:
              - from: "0x0129aC0508E7490E99c6e7e242129480c4743013"
                to: "0x525E59e4DE2B51f52B9e30745a513E407652AB7c"
                gas: "0x7a120"
                value: "0x0"
                data: "0xb35d6ef20000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000000a0f60f67e21a2bcfe2da888576d4673278e6dea28e6860b28cbbabc0f9e01257a98c8cfee7ccbcf4d3c92d819604e6845969fba92887dcc2fc190a89f7f46ad760f60f67e21a2bcfe2da888576d4673278e6dea28e6860b28cbbabc0f9e01257ae81595a3d863af6b6ab5672c3aceadf605725727a7ca99c9da61b64a3d25c6cb98c8cfee7ccbcf4d3c92d819604e6845969fba92887dcc2fc190a89f7f46ad76e81595a3d863af6b6ab5672c3aceadf605725727a7ca99c9da61b64a3d25c6cb73dc6edfcec744d10cb8ccce634fcad0705235511aa0b6d012f700c715f39f4c98c8cfee7ccbcf4d3c92d819604e6845969fba92887dcc2fc190a89f7f46ad7673dc6edfcec744d10cb8ccce634fcad0705235511aa0b6d012f700c715f39f4c39e8f4e2b71ebb2676f5926cbb17be9312050a3e3143cb9b847e844cae1ddf6798c8cfee7ccbcf4d3c92d819604e6845969fba92887dcc2fc190a89f7f46ad7639e8f4e2b71ebb2676f5926cbb17be9312050a3e3143cb9b847e844cae1ddf67188fe89802be70358f18304ec71ec4c40627ad562473a38384a5746ec7e8ef8f98c8cfee7ccbcf4d3c92d819604e6845969fba92887dcc2fc190a89f7f46ad76188fe89802be70358f18304ec71ec4c40627ad562473a38384a5746ec7e8ef8f256bbb7f58daf66d1d2000568d8f59e54f3cbd4abfc26cc406df0af5453331b298c8cfee7ccbcf4d3c92d819604e6845969fba92887dcc2fc190a89f7f46ad76256bbb7f58daf66d1d2000568d8f59e54f3cbd4abfc26cc406df0af5453331b230bf3c2708e2bc5d3737217b82b2947a4aced3bbb73e02cd53198882726192ca98c8cfee7ccbcf4d3c92d819604e6845969fba92887dcc2fc190a89f7f46ad7630bf3c2708e2bc5d3737217b82b2947a4aced3bbb73e02cd53198882726192caef114394908661dd4478dc4071487be7f919011c8c680bdf3fe22e683825b6ed98c8cfee7ccbcf4d3c92d819604e6845969fba92887dcc2fc190a89f7f46ad76ef114394908661dd4478dc4071487be7f919011c8c680bdf3fe22e683825b6edd1824d33e7255db145415dd6add8267813ef392729d45de2041bd315a7734ed398c8cfee7ccbcf4d3c92d819604e6845969fba92887dcc2fc190a89f7f46ad76d1824d33e7255db145415dd6add8267813ef392729d45de2041bd315a7734ed3ac63615605769e774a1a4c6f4da7eb83e9ee17f21c6650487a7959084fc6cf3098c8cfee7ccbcf4d3c92d819604e6845969fba92887dcc2fc190a89f7f46ad76ac63615605769e774a1a4c6f4da7eb83e9ee17f21c6650487a7959084fc6cf30"
                nonce: "0x1"
                type: "0x0"
                gasPrice: "0x6fc23ac00"

      responses:
        "200":
          description: Data submitted successfully
          headers:
            Strict-Transport-Security:
              schema:
                type: string
              description: Strict Transport Security.
            x-sc-trace-id:
              schema:
                type: string
                pattern: "^[0-9A-HJKMNP-TV-Z]{26}$"
              description: Trace ID of the transaction
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/SubmitDataResponse'
        "403":
          description: Forbidden
          headers:
            Strict-Transport-Security:
              schema:
                type: string
              description: Strict Transport Security.
            x-sc-trace-id:
              schema:
                type: string
                pattern: "^[0-9A-HJKMNP-TV-Z]{26}$"
              description: Trace ID of the transaction

components:
  schemas:
    KeyResponse:
      type: object
      required:
        - oracle_key_id
        - eoa_address
      properties:
        oracle_key_id:
          type: string
          description: Unique identifier for the Oracle key, used for signing operations
          example: "550e8400-e29b-41d4-a716-446655440000"
        eoa_address:
          type: string
          description: Ethereum EOA address derived from the KMS key, used for transaction formation
          pattern: "^0x[a-fA-F0-9]{40}$"
          example: "0x742d35Cc6634C0532925a3b8D4e7C0dce3f8b9Ab"

    SubmitDataRequest:
      type: object
      required:
        - oracle_key_id
        - unsigned_transaction
      properties:
        oracle_key_id:
          type: string
          description: Unique identifier for the Oracle key, used for signing operations
          example: "550e8400-e29b-41d4-a716-446655440000"
        unsigned_transaction:
          type: array
          description: Unsigned transaction data in "Ethereum JSON‑RPC" format
          items:
            type: object
            required:
              - from
              - to
              - gas
              - value
              - data
              - nonce
              - type
              - gasPrice
            additionalProperties: false
            properties:
              from:
                type: string
                description: "Hexadecimal string representing the sender address of the transaction."
                example: "0x0129aC0508E7490E99c6e7e242129480c4743013"
              to:
                type: string
                description: "Hexadecimal string representing the recipient address of the transaction."
                example: "0x525E59e4DE2B51f52B9e30745a513E407652AB7c"
              gas:
                type: string
                description: "Hexadecimal string representing the gas limit for the transaction."
                example: "0x7a120"
              value:
                type: string
                description: "Hexadecimal string representing the amount of Ether to transfer."
                example: "0x0"
              data:
                type: string
                description: "Hexadecimal string containing the data payload of the transaction (e.g., smart contract call)."
                example: "0xb35d6ef2000000000000000000000000000000000000000000000000000000000000002…ac63615605769e774a1a4c6f4da7eb83e9ee17f21c6650487a7959084fc6cf30"
              nonce:
                type: string
                description: "Hexadecimal string representing the transaction count (nonce) of the sender account."
                example: "0x1"
              type:
                type: string
                description: "Hexadecimal string representing the transaction type (e.g., 0x0 for legacy transactions)."
                example: "0x0"
              gasPrice:
                type: string
                description: "Hexadecimal string representing the gas price for the transaction."
                example: "0x6fc23ac00"

    SubmitDataResponse:
      type: object
      properties:
        transaction_hash:
          type: string
          description: Hash of the submitted transaction
          example: "0x742d35Cc6634C0532925a3b8D4e7C0dce3f8b9Ab"
  securitySchemes:
    ApiKeyAuth:
      type: "apiKey"
      name: "x-api-key"
      in: "header"
      description: "Documentation x-api-key: (please use your own documentation api key)"
security:
  - ApiKeyAuth: [ ]
```
