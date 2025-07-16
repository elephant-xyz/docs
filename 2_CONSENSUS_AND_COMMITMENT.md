# Stage 2: Consensus & Commitment

**Objective:** Achieve agreement across oracles on seed data and commit its Merkle root to the smart contract.

Once you have extracted the seed data, the next step is to achieve consensus with other oracles and commit the data to the blockchain.

## Consensus Mechanism

Consensus is reached when multiple oracles independently validate and submit the exact same seed data. The Elephant Protocol requires a supermajority (greater than 50%) of participating oracles to submit identical data payloads for consensus to be achieved.

## Merkle DAG Commitment

Upon successful consensus, a Merkle DAG (Directed Acyclic Graph) is generated from the seed data. The root hash of this DAG is then committed to the smart contract. This on-chain commitment establishes the foundational identity of the property within the protocol's ledger, creating an immutable and verifiable record.
