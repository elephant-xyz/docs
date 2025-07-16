# Stage 3: Ingestion

**Objective:** Acquire the full property data set from the seed’s source URL.

After the seed data has been committed, the next stage is to ingest the complete property dataset from the source URL identified during the seeding process.

## Data Ingestion Pipelines

Oracles use standardized ingestion pipelines to retrieve a comprehensive set of property data, which may include:

-   Owner information
-   Parcel and tax data
-   Assessed values
-   Structural and zoning details

To facilitate this process, the Elephant Protocol provides a suite of shared tools, including API connectors, parsers, and scraping agents. These tools are designed to handle various data formats and sources, ensuring that oracles can efficiently and accurately acquire the necessary information.

## Intermediate Data Storage

Once the data is acquired, it is stored in an intermediate structure. This temporary storage allows for a standardized format before the data is transformed into the final Lexicon schema in the next stage.