# enterpact-blockchain
docs &amp; info about enterpact blockchain

### Blockchain Info

Enterpact implements the IBFT 2.0 Proof-of-Authority (PoA) consensus protocol. In IBFT 2.0 networks, approved accounts, known as validators, validate transactions and blocks. Validators take turns to create the next block. Before inserting the block onto the chain, a super-majority (greater than 66%) of validators must first sign the block.

Existing validators propose and vote to add or remove validators. Adding or removing a validator requires a majority vote (greater than 50%) of validators.

IBFT 2.0 requires four validators to be Byzantine fault tolerant. Byzantine fault tolerance is the ability for a blockchain network to function correctly and reach consensus despite nodes failing or propagating incorrect information to peers.


### External Node Setup:

How to setup an external RPC Node

[rpc_node_setup](rpc_node_setup.md)
