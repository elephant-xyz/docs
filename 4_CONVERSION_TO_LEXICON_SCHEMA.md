# Stage 4: Conversion to Lexicon Schema

**Objective:** Normalize ingested data into the protocol-wide Lexicon format.

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