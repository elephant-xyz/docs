# Stage 1: Seeding

**Objective:** Identify and define the minimum viable dataset (MVD) to initialize a property in the protocol.

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