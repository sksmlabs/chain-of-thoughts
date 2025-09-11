
## Macha Federated Protocol

Macha is building a Federated Network for Social Layers with a mission to bring decentralized social content from **blockchains and P2P ecosystem** together, into a single discovery and distribution layer of new internet.

We're building an infrastructure that access social data from protocols and projects like Lens, Farcaster, Zora, Poap and ENS and many more. Built with its own data nodes network, Macha is developed to fetch, and index data from blockchains and abstract the data making it accessible and searchable to various applications through Macha Protocol.


### Problems in Distributed Social Layers

Accessing different social data of multiple protocols on any blockchain network is difficult due to its nature and data structure which is fragmented and scattered on different chains and P2P Networks. Published content either on-chain or off-chain using IPFS and Arweave becomes siloed to a particular environment and is not openly discoverable to many users on the network.

Hence even though the blockchain and P2P data are public, there are a lot of blockers before the public can access this data. So it needs re-engineering for the future of the internet.

And with further advancements in the layer2 scaling solutions and the modular architecture there are likely be an ecosystem of *app-chains*. Which means there will be more fragmentation and bad user experience in utilizing assets and performing actions across the ecosystems. 

### Fixing fragmentation with federation

A federated network is a network model in which a number of separate networks or locations share resources (such as network services and gateways) via a central management framework that enforces consistent configuration and policies.

Here are some characteristics of a federated network:

- Semi-autonomous nodes: Each node can make its own decisions about granting data access.
- Common infrastructure: Federated networks are supported by a common infrastructure with harmonized interoperability standards and tools.
- Heterogeneity: The storage, computational, and communication capabilities of the devices that are part of a federated network may differ significantly


Data in decentralized ecosystem is usually indexed in a centralized system and deliever to clients using APIs and SDKs for faster queries. But this limits the ecosystem from leveraging this data from multiple data sources.

Imagine a potential of the network which has indexed social data from all the prominent social layers like ENS, Lens, Zora, Farcaster, and Poap. And with the evergrowing exposure of decentralized networks for identities, social graphs, NFTs there will be more and more projects that will find the need to access these datas together.

A federated network for the social layers will allow each actors to publish their live data to nodes under the same structured format and governed by the same consensus framework.

---
## Architecture
Macha architecture is modular and is sufficiently decentralised in nature with states and actions are store on chain while data is indexed and stored in a distributed network of nodes and hubs respectively. 

The overall architecture of the Macha protocol is illustrated below:





### Meta Assets

Macha uses **Meta** **Assets** term as a designation to represent an indexed and formatted identity, content and any other data from social layers to the Macha Federated Protocol. These Meta Assets irrespective of their source are structured in a unified metadata format and indexed with their own ***Meta IDentifier*** (MID). 

Every Meta Assets also has a ***Content IDentifier*** (CID) which is created using a cryptographic hashing into a content-addressed data. The protocol could use any hashing algorithm, such as [SHA-1 (opens new window)](https://en.wikipedia.org/wiki/SHA-1)(used by git), [SHA-256 (opens new window)](https://en.wikipedia.org/wiki/SHA-2), or [BLAKE2 (opens new window)](https://en.wikipedia.org/wiki/BLAKE_(hash_function)#BLAKE2), but a given hash algorithm will always returns the same value for a given input.

MID and CIDs are critical for a distributed system like Macha Protocol, where we want to be able to store and retrieve data from many places. A user requesting a certain content can ask all the peers it's connected to whether they have a content with a particular hash and, if one of them does, they send back the whole content.


### Contracts

##### Global Registry 
A 'Registry' is the primary directory of the global state of Macha Protocol. 

##### Storage Registry Contract
This contract allows a anyone to permissionlesly storing data from the Data Nodes


##### Relay Registry Contract


### Nodes

A node in Macha network is responsible for reading data from various blockchains and sending it to the Macha consensus protocol for validation. Anyone can run a node and join the network.

But each node to participate in the network requires to stake in the network to ensure the security and participate in reward acquiral/slashing mechanism. 

Also Indexer Nodes only maintain a temporary record of data and communicate this information to Storage Hubs to record data as per the storage deals.


### Hubs

Hubs are servers that stores and relay data for a longer period and operates storage deals with partners. Hubs are classified as Storage Hub and Relay Hub but both the kinds also work 
#### Storage Hub
Storage Hubs are distributed network of storage servers that validate and store data indexed via Network Nodes. Each Hub could stores a data from multiple data sources which can be accessed over an API.

#### Relay Hub

A  Relay Hub, is a node that maps content identifiers (CIDs) to records of who has the data and how to retrieve that data. Because the Macha protocol can index and stores so much data, clients can’t perform efficient retrieval without proper indexing. Relay Hubs work like a specialized key-value indexers for efficient retrieval of content-addressed data.

Relay Hubs receives request for a data and uses relaying mechanism (PUB/SUB relays) to talk to Data Hubs to collect data and return it back to the request initiator. 
